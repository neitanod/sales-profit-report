<template>
    <div>
    <table>
        <thead>
            <tr>
                <th>checkbox</th>
                <th @click="reorder('id')">ID</th>
                <th @click="reorder('name')">Name</th>
                <th @click="reorder('phone')">Phone</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="record in records">
                <td>checkbox</td>
                <td>{{ record.id }}</td>
                <td>{{ record.name }}</td>
                <td>{{ record.phone }}</td>
            </tr>
        </tbody>
    </table>
    <table v-if="errors.lenght">
        <tr>
            <td v-for"error in errors">
                {{ error }}
            </td>
        </tr>
    </table>
    </div>
</template>
<script>
export default {
    props: ['axios', 'text'],
    data: function() {
        return {
            records: [
            ],
            order_by: '',
            page: '',
            search_string: '',
            search_in_id: '',
            search_in_name: '',
            search_in_phone: '',
            errors: []
        }
    },
    methods: {
        retrieveData() {
            if(Axios) {
                // Axios is present and we can use it

            } else {
                this.errors = ['Axios library is not loaded.  Cannot retrieve data.'];
            }
            // here we call Axios.get() against the API endpoint that would give us the JSON list of records
            // but for now we just assign a demo array:
            this.records = [
                {id: 1, name: "Sebastian", phone: "15-6767-3010" },
                {id: 2, name: "Kalvin",    phone: "555-12345" }
            ]
        },
        reorder() {

        }
    },
}
</script>
