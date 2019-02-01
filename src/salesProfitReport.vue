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

        <div>
            Search everywhere: <input type="text" @change="retrieveData" v-model="search_string" class="form-control">
        </div>
        <table class="table">
            <thead>
                <tr>
                    <th></th>
                    <th scope="col" @click="reorder('id')">#</th>
                    <th scope="col" @click="reorder('name')">Name</th>
                    <th scope="col" @click="reorder('phone')">Phone</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td scope="col"><input type="checkbox" @click="selectRecords"></td>
                    <td><input type="text" @change="retrieveData" class="form-control" v-model="search_in_id"></td>
                    <td><input type="text" @change="retrieveData" class="form-control" v-model="search_in_name"></td>
                    <td><input type="text" @change="retrieveData" class="form-control" v-model="search_in_phone"></td>
                </tr>
                <tr v-for="record in records" v-bind:key="record.id">
                    <td><input type="checkbox"></td>
                    <td>{{ record.id }}</td>
                    <td>{{ record.name }}</td>
                    <td>{{ record.phone }}</td>
                </tr>
                <tr>
                    <td scope="col"><input type="checkbox" @click="selectRecords"></td>
                    <td><input type="text" @change="retrieveData" class="form-control" v-model="search_in_id"></td>
                    <td><input type="text" @change="retrieveData" class="form-control" v-model="search_in_name"></td>
                    <td><input type="text" @change="retrieveData" class="form-control" v-model="search_in_phone"></td>
                </tr>
            </tbody>
        </table>
        <div>
            <div class="pull-left showing">Showing 1 to 10 of 50 entries</div>
            <div class="pull-right paginator"><paginator :first="true" :last="true" page="49" pages="55" links="10"></paginator></div>
            <div class="pull-right perpage">Display
                <select v-model="perpage">
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
            order_by: '',
            requested_page: 1,
            requested_perpage: 10,
            page: 1,
            pages: 1,
            perpage: 10,
            search_string: '',
            search_in_id: '',
            search_in_name: '',
            search_in_phone: '',
            selected_ids: [],
            all_selected: false,
            errors: []
        }
    },
    methods: {
        retrieveData() {
            if(typeof axios == 'function') {
                // Axios is present and we can use it
                let self = this;  // we save a reference to this in self to use it inside the callback.

                // here we call Axios.get() against the API endpoint that would give us the JSON list of records
                let params = {
                    page: self.requested_page,
                    perpage: self.requested_perpage,
                    search_string: self.search_string,
                    search_in_id: self.search_in_id,
                    search_in_name: self.search_in_name,
                    search_in_phone: self.search_in_phone,
                    selected_ids: self.selected_ids,
                    all_selected: self.all_selected
                }
                axios.get(self.endpoint, params)
                .then(function(response) {
                    let data = {};
                    console.log(response);
                    top.lastResponse = response;
                    if(typeof response.data == 'string') {
                        data = JSON.parse(response.data);
                    } else {
                        data = response.data;
                    }
                    console.log(data);
                    self.records = data.records;
                }).catch(function(error) {
                    self.errors = [error.message];
                });
            } else {
                this.errors = ['Axios library is not loaded.  Cannot retrieve data.'];
            }
            this.$forceUpdate();
        },
        reorder() {

        },
        selectRecords() {
            this.selected_ids = [];
        }
    },
    mounted() {    // This function runs when the component is shown
        console.log('MOUNTED');
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
