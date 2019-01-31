<template>
    <div class="sales-profit-report report-container">
        <table v-if="errors.length">
            <tr>
                <td v-for="error in errors" class="alert alert-error">
                    {{ error }}
                </td>
            </tr>
        </table>

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
                <tr v-for="record in records">
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
            <div class="pull-right paginator">[ Paginator goes here ]</div>
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
export default {
    props: [],
    data: function() {
        return {
            records: [
            ],
            order_by: '',
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
            if(typeof Axios == 'undefined') {
                this.errors = ['Axios library is not loaded.  Cannot retrieve data.'];
            } else {
                // Axios is present and we can use it
            }
            // here we call Axios.get() against the API endpoint that would give us the JSON list of records
            // but for now we just assign a demo array:
            this.records = [
                {id: 1, name: "Sebastian", phone: "15-6767-3010" },
                {id: 2, name: "Kalvin",    phone: "555-12345" }
            ];
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
