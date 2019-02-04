<template>
    <div class="sales-profit-report report-container">
        <div v-if="errors.length" class="alert alert-error">
            <div>Errors were found:</div>
            <table>
                <tr v-for="(error, i) in errors" v-bind:key="i">
                    <td>
                        {{ error }}
                    </td>
                </tr>
            </table>
        </div>

        <div class="metrics-container">
            <ul class="metrics">
                <li class="metric">
                    <div class="metric-value en-money" v-html="metric('gross-sales')"></div>
                    <div class="metric-label">gross sales in this period</div>
                </li>
                <li class="metric">
                    <div class="metric-value en-money" v-html="metric('order-based-cogs')"></div>
                    <div class="metric-label">order-based COGS</div>
                </li>
                <li class="metric">
                    <div class="metric-value en-money" v-html="metric('product-based-cogs')"></div>
                    <div class="metric-label">product-based COGS</div>
                </li>
                <li class="metric">
                    <div class="metric-value en-money" v-html="metric('gross-profit')"></div>
                    <div class="metric-label">gross profit period</div>
                </li>
                <li class="metric">
                    <div class="metric-value" v-html="metric('orders')"></div>
                    <div class="metric-label">orders placed</div>
                </li>
                <li class="metric">
                    <div class="metric-value" v-html="metric('products')"></div>
                    <div class="metric-label">units sold</div>
                </li>
            </ul>
        </div>

        <div class="chart-container">
            <div class="chart">
            </div>
        </div>

        <table class="table table-bordered table-hover">
            <thead>
                <tr>
                    <th scope="col" :class="orderClass('id')" @click="reorder('id')">#</th>
                    <th scope="col" :class="orderClass('name')" @click="reorder('name')">Name</th>
                    <th scope="col" :class="orderClass('phone')" @click="reorder('phone')">Phone</th>
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
            <div class="pull-left showing">Showing {{ (page-1)*perpage+1 }} to {{ (page-1)*perpage+records.length }}  of {{ count }} entries</div>
            <div class="pull-right paginator"><paginator class="bottom" :first="true" :last="true" v-model="page" :pages="pages()" links="5"></paginator></div>
            <div class="pull-right perpage">Display
                <select class="perpage-select" v-model="perpage" @change="retrieveTable">
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
    props: {
        endpoint: {
            type: String,
            default: 'api/something'
        },
        metrics_endpoint: {
            type: String,
            default: 'api/metrics'
        }
    },
    data: function() {
        return {
            records: [
            ],
            metrics: {
            },
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
            this.retrieveTable();
            this.retrieveMetrics();
        },
        retrieveTable() {
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
                };

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
                        if(typeof response.data == 'string') {
                            // I believe this explicit decoding is only needed because
                            // current mock response do not have the correct mime type
                            // header (json)
                            data = JSON.parse(response.data);
                        } else {
                            data = response.data;
                        }
                        self.records = data.records;
                        self.count = data.count;
                    }).catch(function(error) {
                        self.addError("Table: "+error.message);
                    });

            } else {
                self.addError('Axios library is not loaded.  Cannot retrieve data.');
            }
            this.$forceUpdate();
        },
        retrieveMetrics() {
            if(typeof axios == 'function') {
                // Axios is present and we can use it
                let self = this;  // we save a reference to this in self to use it inside the callback.

                // here we call Axios.get() against the API endpoint that would give us the JSON list of records

                let metrics_parameters = {
                };

                // eslint-disable-next-line
                axios.get(self.metrics_endpoint, {params: metrics_parameters})
                    .then(function(response) {
                        let data = {};
                        console.log(response);
                        if(typeof response.data == 'string') {
                            data = JSON.parse(response.data);
                        } else {
                            data = response.data;
                        }
                        self.metrics = data.metrics;
                    }).catch(function(error) {
                        self.addError("Metrics: "+error.message);
                    });

            } else {
                self.addError('Axios library is not loaded.  Cannot retrieve data.');
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
            this.retrieveTable();
        },
        orderClass(order){
            /*
             :class= attribute on Vue accepts an object like:
             {
               classname1: bool_apply_or_not,
               classname2: bool_apply_or_not,
               classname3: bool_apply_or_not
             }
             and it does not remove classes added by regular class= attribute
            */
            let css_classes = {};
            css_classes['sorting'] = true;
            css_classes['sorting_asc'] = (order == this.order_by);
            css_classes['sorting_desc'] = (order+"_desc" == this.order_by);
            return css_classes;
        },
        metric(name){
            // reads from this.metrics, but does not throw error if index does not exist
            if(!(typeof this.metrics[name] == 'undefined')) {
                return this.metrics[name];
            } else {
                return 0;
            }
        },
        addError(errormsg, timeout){
            let self = this;
            if(timeout == undefined) {
                timeout = 3000;
            }
            setTimeout(function(){ self.dismissErrors(); }, timeout);
            this.errors.push(errormsg);
        },
        dismissErrors(){
            this.errors.shift();
        }
    },
    watch: {
        page: function() {
            this.retrieveTable();
        }
    },
    mounted() {
        this.retrieveData();
    },
}
</script>
<style>
    @import url(//fonts.googleapis.com/css?family=Poppins:300,400,500,600,700%7CRoboto:300,400,500,600,700);
    .sales-profit-report {
        font-family: Poppins;
        font-weight: 300;
        line-height: 1.5;
        margin: 5px;
    }

    /* Metrics */
    .sales-profit-report .metrics-container {
        flex: 0 0 100%;
        max-width: 100%;
    }

    @media (min-width: 1200px) {
        .sales-profit-report .metrics-container {
            flex: 0 0 16.66667%;
            max-width: 16.66667%;
        }
    }

    .sales-profit-report .metrics {
        width: 100%;
        padding: 15px;
        float: left;
    }

    .sales-profit-report .metrics li {
        display: block;
        box-shadow: 0px 1px 15px 1px rgba(69, 65, 78, 0.08);
        background-color: #ffffff;
        padding: 15px;
        color: #6f727d;
    }

    .sales-profit-report .metrics .metric-value {
        font-size: 20px;
        font-weight: 600;
    }

    .sales-profit-report .metrics .metric-label {
        font-size: 13px;
    }

    .sales-profit-report .metrics .en-money::before {
        content:"$";
    }

    .sales-profit-report .metrics .neg-money {
        color:red;
    }

    /* Chart */
    .sales-profit-report .chart-container {
        background-color: grey;
        width: 100%;
        height: 500px;
    }

    .sales-profit-report .chart-container {
        float: left;
        flex: 0 0 100%;
        max-width: 100%;
    }

    @media (min-width: 1200px) {
        .sales-profit-report .chart-container {
            flex: 0 0 83.33333%;
            max-width: 83.33333%;
        }
    }

    /* Table */
    .sales-profit-report .table {
        font-size: 13px;
    }
    .sales-profit-report .showing,
    .sales-profit-report .paginator,
    .sales-profit-report .perpage {
        margin-left: 3px;
        margin-right: 3px;
    }
    .sales-profit-report .sorting {
        cursor: pointer;
    }
    .sales-profit-report .perpage-select {
        background-color: #ffffff;
        border-color: #dddddd;
    }

    /* Sorting arrows */
    .sales-profit-report .sorting:before,
    .sales-profit-report .sorting:after {
        right: 0.5em;
        float: right;
        color: #cccccc;
    }
    .sales-profit-report .sorting:after {
        content: "\2193";
    }
    .sales-profit-report .sorting:before {
        content: "\2191";
    }
    .sales-profit-report .sorting_asc:after,
    .sales-profit-report .sorting_desc:before {
        font-weight: bolder;
        color: black;
    }

    /* Error message */
    .alert.alert-error {
        background-color: #f8d7da;
        border-color: #f5c6cb;
    }


</style>
