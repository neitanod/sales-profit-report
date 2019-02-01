<template>
    <div class="sales-profit-report report-container">
        <div v-if="errors.length" class="alert alert-error">
            <div>Errors were found:</div>
            <table>
                <tr>
                    <td v-for="(error, i) in errors" v-bind:key="i">
                        {{ error }}
                    </td>
                </tr>
            </table>
        </div>

        <table class="table">
            <thead>
                <tr>
                    <th scope="col" @click="reorder('id')">#</th>
                    <th scope="col" @click="reorder('name')">Name</th>
                    <th scope="col" @click="reorder('phone')">Phone</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="record in records" v-bind:key="record.id">
                    <td>{{ record.id }}</td>
                    <td>{{ record.name }}</td>
                    <td>{{ record.phone }}</td>
                </tr>
            </tbody>
        </table>
        <div>
            <div class="pull-left showing">Showing 1 to 10 of 50 entries</div>
            <div class="pull-right paginator"><paginator class="bottom" :first="true" :last="true" v-model="page" :pages="pages()" links="7"></paginator></div>
            <div class="pull-right perpage">Display
                <select v-model="perpage" @change="retrieveData">
                    <option value="10">10</option>
                    <option value="50">50</option>
                    <option value="100">100</option>
                </select>
            </div>
        </div>
    </div>
</template>
<script>
var paginator = require('./paginator.vue').default;
export default {
    components: { paginator: paginator },
    props: {endpoint: {
        type: String,
        default: 'api/something'
    }},
    data: function() {
        return {
            records: [
            ],
            last_request: 1,
            page: 1,
            perpage: 10,
            order_by: '',
            count: 0,
            errors: []
        }
    },
    methods: {
        retrieveData() {
            if(typeof axios == 'function') {
                // Axios is present and we can use it
                let self = this;  // we save a reference to this in self to use it inside the callback.

                // here we call Axios.get() against the API endpoint that would give us the JSON list of records
                self.last_request++;
                let this_request = self.last_request;
                let parameters = {
                    page: self.page,
                    perpage: self.perpage,
                    sort: self.order_by,
                }
                // eslint-disable-next-line
                axios.get(self.endpoint, {params: parameters})
                .then(function(response) {
                    if(self.last_request != this_request) {
                        // this request is not the last one
                        // we made (maybe because of network issues)
                        // Discard it.
                        return;
                    }
                    let data = {};
                    console.log(response);
                    top.lastResponse = response;
                    if(typeof response.data == 'string') {
                        // I believe this explicit decoding is only needed because
                        // current mock response do not have the correct mime type
                        // header (json)
                        data = JSON.parse(response.data);
                    } else {
                        data = response.data;
                    }
                    console.log(data);
                    self.records = data.records;
                    self.count = data.count;
                }).catch(function(error) {
                    self.errors = [error.message];
                });
            } else {
                this.errors = ['Axios library is not loaded.  Cannot retrieve data.'];
            }
            this.$forceUpdate();
        },
        pages() {
            return Math.ceil(this.count / this.perpage);
        },
        reorder(by) {
            if(by == this.order_by) {
                this.order_by = by+"_desc";
            } else if(by+"_desc" == this.order_by) {
                this.order_by = "";
            } else {
                this.order_by = by;
            }
            this.retrieveData();
        }
    },
    watch: {
        page: function(old, new_val) {
            // console.log("this.page old value:", old);
            // console.log("this.page new value:", new_val);
            this.retrieveData();
        }
    },
    mounted() {
        this.retrieveData();
    },
}
</script>
<style>
    .sales-profit-report {
        margin: 5px;
    }
    .sales-profit-report .showing,
    .sales-profit-report .paginator,
    .sales-profit-report .perpage {
        margin-left: 3px;
        margin-right: 3px;
    }
</style>
