<style>
.card-header {
    display: none
}
.buttonAdd {
    margin-top: 15px;
    width: 150px;
    height: 40px;
    cursor: pointer;
    margin-left: 30px;
}
.card {
    border: 0 !important;
}
</style>
<template>
    <div id="app">
        <b-button class="buttonAdd" @click="addToDo()" >Fetch All Values</b-button>
        <b-button v-b-modal.modal-1 class="buttonAdd" >Add drone</b-button>
            <b-modal @ok="addDrone" id="modal-1" title="Add Drone">
                <form>
                    <div class="form-group">
                        <label for="name" class="col-form-label">Name:</label>
                        <input v-model="name" type="text" class="form-control" id="name">
                    </div>
                    <div class="form-group">
                        <label for="battery" class="col-form-label">Battery:</label>
                        <input  v-model="battery" type="number" class="form-control" id="battery">
                    </div>
                    <div class="form-group">
                        <label for="maxSpeed" class="col-form-label">Max Speed:</label>
                        <input v-model="maxSpeed" type="number" class="form-control" id="maxSpeed">
                    </div>
                    <div class="form-group">
                        <label for="averageSpeed" class="col-form-label">Average Speed:</label>
                        <input v-model="averageSpeed" type="number" class="form-control" id="averageSpeed">
                    </div>
                    <div class="form-group">
                        <label for="currentFly" class="col-form-label">Current fly:</label>
                        <input v-model="currentFly" type="number" class="form-control" id="currentFly">
                    </div>
                    <div class="form-group">
                        <label for="status" class="col-form-label">Status:</label>
                        <input v-model="status" type="text" class="form-control" id="status">
                    </div>
                    <div class="form-group">
                        <label for="address" class="col-form-label">Address:</label>
                        <input v-model="address" type="text" class="form-control" id="address">
                    </div>
                    <div class="form-group">
                        <label for="image" class="col-form-label">Image:</label>
                        <input v-model="image" type="text" class="form-control" id="image">
                    </div>
                </form>
            </b-modal>
        <vue-bootstrap4-table :rows="rows" :columns="columns" :config="config" @status="onDownload">
            <template slot="name" slot-scope="props">
                  <img src="./assets/boneco.png" style="width: 32px; height: 32px;">
                  <label>{{props.cell_value}}</label>
            </template>
            <template slot="status">
                  <b-button style="background-color: green; color: white" size="sm" class="mr-1" variant="warning">
                  Success 
                  </b-button>
            </template>
            <template slot="battery" slot-scope="props">
                  <div class="progress">
                    <div class="progress-bar"  :style="{'width': `${props.cell_value}`+'%'}">{{props.cell_value}}</div>
                  </div>
            </template>
            <template slot="fly" slot-scope="props">
                  <div class="progress">
                    <div class="progress-bar" :style="{'width': `${props.cell_value}`+'%'}">{{props.cell_value}}</div>
                  </div>
            </template>
        </vue-bootstrap4-table>
    </div>
</template>
 
<script>
import VueBootstrap4Table from 'vue-bootstrap4-table';
import axios from "axios";
 
export default {
    name: 'App',
    el: '#app',
    data: function() {
        return {
            name: '',
            address: '',
            image: '',
            battery: 0,
            maxSpeed: 0,
            averageSpeed: 0,
            currentFly: 0,
            status: '',
            rows: [],
            columns: [{
                    label: "DRONE",
                    name: "id",
                    filter: {
                        type: "simple",
                        placeholder: "id"
                    },
                    sort: true,
                },
                {
                    label: "CUSTOMER",
                    name: "name",
                    filter: {
                        type: "simple",
                        placeholder: "Enter name"
                    },
                    sort: true,
                },
                {
                    label: "BATTERIES",
                    name: "battery",
                    sort: true,
                    callback: 'batteries',
                    sortField: 'battery'
                },
                {
                    label: "MAX SPEED",
                    name: "maxSpeed",
                    filter: {
                        type: "simple",
                        placeholder: "Enter max speed"
                    },
                },{
                    label: "AVERAGE SPEED",
                    name: "averageSpeed",
                    filter: {
                        type: "simple",
                        placeholder: "Enter average speed"
                    },
                },{
                    label: "CURRENT FLY",
                    name: "fly",
                    filter: {
                        type: "simple",
                        placeholder: "Enter current fly"
                    },
                },{
                    isLocal: true,
                    label: "STATUS",
                    name: "status",
                    filter: {
                        type: "simple",
                        placeholder: "Enter status"
                    },
                }
                ],
                actions: [
                    {
                        btn_text: "Download",
                        event_name: "status",
                        class: "btn btn-primary my-custom-class",
                        event_payload: {
                            msg: "my custom msg"
                        }
                    }
                ],
            config: {
                checkbox_rows: true,
                rows_selectable: true,
                show_refresh_button: false,
                show_reset_button: false,
                 global_search: {
                        placeholder: "Enter filter values",
                 }
            }
        }
    },
    methods: {
    addToDo() {
        let body = {
            query: ` 
               query {
                    getDrones {
                     name,
                     id,
                     image,
                     address,
                     fly,
                     averageSpeed,
                     status,
                     battery,
                     maxSpeed,
                    }
                }
            `,
        }
    let options = {
                headers: {
                    'Content-Type': 'application/json'
                }
            }

      axios.post("http://localhost:3003/graphql",body, options).then(response => {
         this.rows = response.data.data.getDrones;
     });
    },
    addDrone() {
        let body = {
            query: `
            mutation {
                createDrone(data: {
                  name: "${this.name}",
                  fly: ${this.currentFly},
                  image: "${this.image}",
                  address: "${this.address}",
                  averageSpeed: ${this.averageSpeed},
                  status: "${this.status}",
                  battery: ${this.battery},
                  maxSpeed: ${this.maxSpeed},
                }) {
                  id
                  name
                  address
                  createdAt
                  updatedAt  }
            }`
        }

        let options = {
                headers: {
                    'Content-Type': 'application/json'
                }
            }
        
        axios.post("http://localhost:3003/graphql",body, options).then(response => {
         this.rows.push(response.data.data.createDrone);
        })
    },
    onDownload(payload) {
        console.log(payload);
    }
  },
  components: {
        VueBootstrap4Table
  }
}
</script>