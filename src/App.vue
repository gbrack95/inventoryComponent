<template>
    <div id="app" v-cloak>
      <v-app>
        <v-content v-if="isLoggedIn == false">
          <v-container
            fill-height>
            <v-layout
              align-center>
                <v-flex
                  xs1>
                </v-flex>
                <v-flex
                  xs10>
                  <v-card
                    height=400>
                    <v-container
                      fill-height>
                      <v-layout
                        align-center>
                        <v-flex xs2></v-flex>
                          <v-flex xs8>
                            <v-form>
                                <v-text-field
                                  v-model="login_username"
                                  label="Email"
                                  required
                                  >
                                </v-text-field>
                                <v-text-field
                                  v-model="login_password"
                                  label="Password"
                                  required
                                  :type="show1 ? 'text' : 'password'"
                                  @click:append="show1 = !show1"
                                  :append-icon="show1 ? 'visibility' : 'visibility_off'"
                                  >
                                </v-text-field>
                                <v-layout
                                  justify-space-between>
                                  <v-btn
                                    @click="login()"
                                    >login
                                  </v-btn>
                                  <template>
                                    <v-layout>
                                      <v-dialog v-model="dialogRegister" persistent max-width="600px">
                                        <template v-slot:activator="{ on }">
                                          <v-btn
                                            v-on="on"
                                            >register
                                          </v-btn>
                                        </template>
                                        <v-card>
                                          <v-card-title>
                                            <span class="headline">Register</span>
                                          </v-card-title>
                                          <v-card-text>
                                            <v-container grid-list-md>
                                              <v-layout wrap>
                                                <v-flex xs12>
                                                  <v-text-field v-model="register_username" label="New Username*" required></v-text-field>
                                                </v-flex>
                                                <v-flex xs12>
                                                  <v-text-field
                                                    v-model="register_password"
                                                    label="New Password*"
                                                    required
                                                    :type="show2 ? 'text' : 'password'"
                                                    @click:append="show2 = !show2"
                                                    :append-icon="show2 ? 'visibility' : 'visibility_off'"
                                                    >
                                                  </v-text-field>
                                                </v-flex>
                                              </v-layout>
                                            </v-container>
                                          </v-card-text>
                                          <v-card-actions>
                                            <v-spacer></v-spacer>
                                            <v-btn color="blue darken-1" flat @click="dialogRegister = false">Cancel</v-btn>
                                            <v-btn color="blue darken-1" flat @click="register(), dialogRegister = false">Save</v-btn>
                                          </v-card-actions>
                                        </v-card>
                                      </v-dialog>
                                    </v-layout>
                                  </template>
                                </v-layout>
                            </v-form>
                          </v-flex>
                        <!-- <v-flex xs2></v-flex> -->
                      </v-layout>
                      </v-container>
                    </v-card>
                </v-flex>
                <v-flex xs1></v-flex>
            </v-layout>
          </v-container>
        </v-content>
          <v-content v-else>
              <v-toolbar>
                <v-toolbar-title><span class="font-weight-thin">Universal</span> <span>Inventory</span></v-toolbar-title>
                <v-spacer></v-spacer>
                <v-toolbar-title
                  class="body-2">
                  Hello, {{currentUser}}
                </v-toolbar-title>
                <v-toolbar-items>
                  <v-btn flat v-on:click="logout()"> Log Out</v-btn>
                </v-toolbar-items>
              </v-toolbar>

              <v-navigation-drawer class="blue darken-1" dark app v-model="drawer">
                  <v-list>
                      <v-list-tile>
                        <v-list-tile-title class="title">
                          <span class="font-weight-thin">Universal</span> <span>Inventory</span>
                        </v-list-tile-title>
                      </v-list-tile>
                      <v-divider></v-divider>

                      <v-list-tile v-for="item in items" v-on:click="page = item.page">
                          <v-list-tile-action>
                            <v-icon>{{ item.icon}}</v-icon>
                          </v-list-tile-action>
                          {{item.page}}
                      </v-list-tile>
                  </v-list>

              </v-navigation-drawer>
              <v-container grid-list-lg fluid v-if="page == 'dashboard'">
                  <h1> Dashboard </h1>
                  <v-layout row>
                      <v-flex xs6>
                          <v-card>
                              <v-card-title>
                                  <v-icon size="80px" left color="#ff5252" class="mb-10" z-index="1">
                                      insert_chart
                                  </v-icon>
                                      Total Inventory Items {{inventory.length}}
                              </v-card-title>
                          </v-card>
                      </v-flex>
                      <v-flex xs6>
                          <v-card>
                              <v-card-title>
                                  <v-icon size="80px" left color="#FFEB3B" class="mb-10" z-index="1">
                                      monetization_on
                                  </v-icon>
                                  Total Sales
                              </v-card-title>
                          </v-card>
                      </v-flex>
                  </v-layout>
                  <v-layout row>
                      <v-flex xs6>
                          <v-card>
                              <v-card-title>
                                  <v-icon size="80px" left color="#8E24AA" class="mb-10" z-index="1">
                                      local_shipping
                                  </v-icon>
                                      Total Orders To Be Shipped
                              </v-card-title>
                          </v-card>
                      </v-flex>
                      <!-- <v-flex xs6>
                          <v-card>
                              <v-card-title>
                                  <v-icon size="80px" left color="#FFEB3B" class="mb-10" z-index="1">
                                      monetization_on
                                  </v-icon>
                                  Total Sales
                              </v-card-title>
                          </v-card>
                      </v-flex> -->
                  </v-layout>
                  <v-layout row>

                      <v-flex xs12>

                              <h1> Calendar <h2>{{todaysdate}}</h2></h1>

                              <v-calendar v-if="re_render" color="#00C853">
                                  <template v-slot:day="{ date }">
                                      <template v-for="item in eventsMap[date]">
                                        <v-menu :key="item.title" v-model="item.open" full-width offset-x>
                                          <template v-slot:activator="{ on }">
                                            <div
                                              v-if="!item.time"
                                              v-ripple
                                              class="my-event"
                                              v-on="on"
                                              v-html="item.title"
                                            ></div>
                                          </template>
                                          <v-card
                                            color="#00C853"
                                            min-width="350px"
                                            flat
                                          >
                                              <v-toolbar color="#00C853" dark>
                                                  <v-toolbar-title>
                                                      {{item.title}}
                                                  </v-toolbar-title>
                                                  <v-spacer></v-spacer>
                                              </v-toolbar>
                                              <v-card>
                                                  <v-card-title primary-title dark>
                                                      {{item.marketplace}} - {{item.category}}
                                                  </v-card-title>
                                                  <v-card-actions>
                                                      <v-btn flat>
                                                              Cancel
                                                      </v-btn>
                                                      </v-card-actions>
                                                  </v-card>
                                              </v-card>
                                          </v-menu>
                                      </template>
                                  </template>
                              </v-calendar>
                      </v-flex>
                  </v-layout>
              </v-container>

              <!-- Inventory Page -->
                <v-container grid-list-lg fluid v-if="page == 'inventory'">
                  <h1> Inventory</h1>
                  <v-layout grid-list-lg wrap>
                    <v-flex xs4>
                      <v-select
                        label="Marketplace"
                        :items="marketplaces"
                        v-model="marketplaceType"
                      ></v-select>
                    </v-flex>
                    <v-flex xs4>
                      <v-select
                        label="Category"
                        :items="categories"
                        v-model="categoryType"
                      ></v-select>
                    </v-flex>
                    <v-flex xs4>
                      <v-text-field
                        v-model="search"
                        append-icon="search"
                        label="Search"
                        single-line
                        hide-details
                      ></v-text-field>
                    </v-flex>
                  </v-layout>

                  <!--new item button -->
                      <div>
                          <v-dialog v-model="dialog" max-width="500px">
                          <template v-slot:activator="{ on }">
                              <v-layout justify-start row>
                                  <v-flex xs2>
                                      <v-btn color="blue darken-1" dark class="mb-2 " v-on="on">New Item</v-btn>
                                  </v-flex>
                              </v-layout>
                          </template>
                          <v-card>
                            <v-card-title>
                                <h1> New Item </h1>
                            </v-card-title>


                            <v-card-text>
                              <v-container grid-list-md>
                                <v-layout wrap>
                                  <v-flex xs12 sm6 md4>
                                    <v-select
                                      v-model="newMarketplace"
                                      label="MarketPlace"
                                      :items="marketplaces"
                                      >
                                    </v-select>
                                  </v-flex>
                                  <v-flex xs12 sm6 md4>
                                    <v-text-field v-model="newImage" label="Image URL"></v-text-field>
                                  </v-flex>
                                  <v-flex xs12 sm6 md4>
                                    <v-text-field v-model="newTitle" label="Title"></v-text-field>
                                  </v-flex>
                                  <v-flex xs12 sm6 md4>
                                    <v-select
                                      v-model="newCategory"
                                      label="Category"
                                      :items="categories"
                                      >
                                    </v-select>
                                  </v-flex>
                                  <v-flex xs12 sm6 md4>
                                    <v-text-field v-model="newQuantity" label="Qty"></v-text-field>
                                  </v-flex>
                                  <v-flex xs12 sm6 md4>
                                    <v-text-field v-model="newCost" label="Cost"></v-text-field>
                                  </v-flex>
                                  <v-flex xs12 sm6 md4>
                                    <v-text-field v-model="newSku" label="SKU"></v-text-field>
                                  </v-flex>
                                  <v-flex xs12 sm6 md4>
                                    <v-text-field v-model="newLocation" label="Location"></v-text-field>
                                  </v-flex>
                                  <v-flex xs12 sm6 md4>
                                      <v-menu
                                        ref="menu"
                                        v-model="menu"
                                        :close-on-content-click="false"
                                        :nudge-right="40"
                                        :return-value.sync="date"
                                        lazy
                                        transition="scale-transition"
                                        offset-y
                                        full-width
                                        min-width="290px"
                                      >
                                        <template v-slot:activator="{ on }">
                                          <v-text-field
                                            v-model="date"
                                            label="Start Date"
                                            prepend-icon="event"
                                            readonly
                                            v-on="on"
                                          ></v-text-field>
                                        </template>
                                        <v-date-picker v-model="date" no-title scrollable>
                                          <v-spacer></v-spacer>
                                          <v-text-field v-model="newTitle" label="Title"></v-text-field>
                                          <v-spacer></v-spacer>
                                          <v-btn flat color="primary" @click="menu = false">Cancel</v-btn>
                                          <v-btn flat color="primary" @click="$refs.menu.save(date)">OK</v-btn>
                                        </v-date-picker>
                                      </v-menu>
                                  </v-flex>
                                </v-layout>
                              </v-container>
                            </v-card-text>

                            <v-card-actions>
                              <v-spacer></v-spacer>
                              <v-btn color="blue darken-1" flat @click="cancelnewitem()">Cancel</v-btn>
                              <v-btn color="blue darken-1" flat @click="addItem()">Save</v-btn>
                            </v-card-actions>
                          </v-card>
                        </v-dialog>
                    </div>

                <!-- CSV Upload -->

                    <div>
                        <v-dialog v-model="csvDialog" max-width="500px">
                            <template v-slot:activator="{ on }">
                                <v-btn color="blue darken-1" dark class="mb-2 " v-on="on">CSV Upload</v-btn>
                            </template>
                            <v-card>
                                <v-card-title>
                                    <h1>CSV Upload</h1>
                                </v-card-title>
                                <v-card-text>
                                    <vue-csv-import v-model="csv" :map-fields="['marketplace', 'image', 'title', 'category', 'qty', 'sku', 'location', 'cost']"></vue-csv-import>
                                </v-card-text>
                                <v-card-actions>
                                  <v-spacer></v-spacer>
                                  <v-btn color="blue darken-1" flat @click="cancelCsvUpload()">Cancel</v-btn>
                                  <v-btn color="blue darken-1" flat @click="sendData()">Upload</v-btn>
                                </v-card-actions>
                            </v-card>
                        </v-dialog>
                    </div>

                <!-- Inventory List -->
                  <v-data-table
                    :headers="headers"
                    :items="filteredItem"
                    :search="search"
                    class="elevation-1"
                  >
                   <template v-slot:items="props">
                     <td v-if="!editing[props.index].show">{{ props.item.marketplace }}</td>
                     <td v-else >
                        <v-select
                          v-model="props.item.marketplace"
                          :items="marketplaces"
                          >
                        </v-select>
                     </td>
                       <td v-if="!editing[props.index].show">
                          <v-img width="100%" v-bind:src="props.item.image"></v-img>
                        </td>
                       <td v-else >
                         <v-text-field
                             v-model="props.item.image"
                             label="Edit"
                             single-line
                             height="20"
                             mb-0
                             class="!text-field_details"
                            ></v-text-field>
                       </td>
                       <td v-if="!editing[props.index].show">{{ props.item.title }}</td>
                       <td v-else >
                         <v-text-field
                             v-model="props.item.title"
                             label="Edit"
                             single-line
                             height="20"
                             mb-0
                             class="!text-field_details"
                            ></v-text-field>
                       </td>
                       <td v-if="!editing[props.index].show">{{ props.item.category }}</td>
                       <td v-else >
                         <v-select
                           v-model="props.item.category"
                           :items="categories"
                           >
                         </v-select>
                       </td>
                       <td v-if="!editing[props.index].show">{{ props.item.quantity }}</td>
                       <td v-else >
                         <v-text-field
                             v-model="props.item.quantity"
                             label="Edit"
                             single-line
                             height="20"
                             mb-0
                             class="!text-field_details"
                            ></v-text-field>
                       </td>
                       <td v-if="!editing[props.index].show">{{ props.item.sku }}</td>
                       <td v-else >
                         <v-text-field
                             v-model="props.item.sku"
                             label="Edit"
                             single-line
                             height="20"
                             mb-0
                             class="!text-field_details"
                            ></v-text-field>
                       </td>
                       <td v-if="!editing[props.index].show">{{ props.item.location }}</td>
                       <td v-else >
                         <v-text-field
                             v-model="props.item.location"
                             label="Edit"
                             single-line
                             height="20"
                             mb-0
                             class="!text-field_details"
                            ></v-text-field>
                       </td>
                       <td v-if="!editing[props.index].show">{{ props.item.cost }}</td>
                       <td v-else >
                         <v-text-field
                             v-model="props.item.cost"
                             label="Edit"
                             single-line
                             height="20"
                             mb-0
                             class="!text-field_details"
                            ></v-text-field>
                       </td>
                       <td
                          class="layout"
                          v-if="!editing[props.index].show">
                                <v-icon class="mr-2 align-center pt-4" small  @click="updateList(props.index)"> edit </v-icon>
                                <v-icon class="mr-2 align-center pt-4" small @click="deleteItem(props.item)"> delete </v-icon>
                      </td>
                      <td
                        class="layout"
                        v-else >
                          <v-btn class="white--text" color="red" @click="updateList(props.index)">cancel</v-btn>
                          <v-btn class="white--text" color="blue darken-1" @click="updateInventory(props.item, props.index)">save</v-btn>
                      </td>
                    </template>
                    <template v-slot:no-results>
                      <v-alert :value="true" color="error" icon="warning">
                        Your search for "{{ search }}" found no results.
                      </v-alert>
                    </template>
                  </v-data-table>
                </v-container>

              <!-- Orders Page -->
              <v-container grid-list-lg fluid v-if="page == 'orders'">
                  <h1> Orders</h1>
                  <v-layout grid-list-lg wrap>
                      <v-flex xs4>
                          <v-select
                            label="Marketplace"
                            :items="marketplaces"
                            v-model="marketplaceType"
                          ></v-select>
                      </v-flex>
                      <v-flex xs4>
                          <v-select
                            label="Category"
                            :items="categories"
                            v-model="categoryType"
                          ></v-select>
                      </v-flex>
                      <v-flex xs4>
                          <v-text-field
                            v-model="search"
                            append-icon="search"
                            label="Search"
                            single-line
                            hide-details
                          ></v-text-field>
                      </v-flex>
              </v-layout>

                  <template>
                  <div>
                      <v-dialog v-model="dialogOrder" max-width="500px">
                      <template v-slot:activator="{ on }">
                          <v-layout justify-start row>
                              <v-flex xs2>
                                  <v-btn color="blue darken-1" dark class="mb-2" v-on="on">New Order</v-btn>
                              </v-flex>
                          </v-layout>
                      </template>
                      <v-card>
                        <v-card-title>
                            <h1> New Order </h1>
                        </v-card-title>


                        <v-card-text>
                          <v-container grid-list-md>
                            <v-layout wrap>
                              <v-flex xs12 sm6 md4>
                                <v-text-field v-model="newOrderCustomer" label="Customer"></v-text-field>
                              </v-flex>
                              <v-flex xs12 sm6 md4>
                                <v-text-field v-model="newOrderSku" label="Sku"></v-text-field>
                              </v-flex>
                              <v-flex xs12 sm6 md4>
                                <v-text-field v-model="newOrderTitle" label="Title"></v-text-field>
                              </v-flex>
                              <v-flex xs12 sm6 md4>
                                <v-select v-model="newOrderCategory" label="Category"
                                :items="categories">
                            </v-select>
                              </v-flex>
                              <v-flex xs12 sm6 md4>
                                <v-text-field v-model="newOrderQuantity" label="Qty"></v-text-field>
                              </v-flex>
                              <v-flex xs12 sm6 md4>
                                <v-text-field v-model="newOrderPrice" label="Price"></v-text-field>
                              </v-flex>
                              <v-flex xs12 sm6 md4>
                                <v-select v-model="newOrderMarketplace" label="Marketplace"
                                :items="marketplaces"></v-select>
                              </v-flex>
                              <v-flex xs12 sm6 md4>
                                <v-text-field v-model="newOrderLocation" label="Location"></v-text-field>
                              </v-flex>
                            </v-layout>
                          </v-container>
                        </v-card-text>

                        <v-card-actions>
                          <v-spacer></v-spacer>
                          <v-btn color="blue darken-1" flat @click="cancelNewOrder()">Cancel</v-btn>
                          <v-btn color="blue darken-1" flat @click="addOrder()">Save</v-btn>
                        </v-card-actions>
                      </v-card>
                    </v-dialog>
                </div>
                </template>

                <v-data-table
                  :headers="Orderheaders"
                  :items="order"
                  class="elevation-1"
                > <!--change to filtered items-->
                <template v-slot:items="props">
                  <td v-if="!order_editing[props.index].show">{{ props.item.customer }}</td>
                  <td v-else >
                    <v-text-field
                        v-model="props.item.customer"
                        label="Edit"
                        single-line
                        height="20"
                        mb-0
                        class="!text-field_details"
                       ></v-text-field>
                  </td>
                    <td v-if="!order_editing[props.index].show">{{ props.item.sku}}</td>
                    <td v-else >
                      <v-text-field
                          v-model="props.item.sku"
                          label="Edit"
                          single-line
                          height="20"
                          mb-0
                          class="!text-field_details"
                         ></v-text-field>
                    </td>
                    <td v-if="!order_editing[props.index].show">{{ props.item.title }}</td>
                    <td v-else >
                      <v-text-field
                          v-model="props.item.title"
                          label="Edit"
                          single-line
                          height="20"
                          mb-0
                          class="!text-field_details"
                         ></v-text-field>
                    </td>
                    <td v-if="!order_editing[props.index].show">{{ props.item.category }}</td>
                    <td v-else >
                      <v-text-field
                          v-model="props.item.category"
                          label="Edit"
                          single-line
                          height="20"
                          mb-0
                          class="!text-field_details"
                         ></v-text-field>
                    </td>
                    <td v-if="!order_editing[props.index].show">{{ props.item.quantity }}</td>
                    <td v-else >
                      <v-text-field
                          v-model="props.item.quantity"
                          label="Edit"
                          single-line
                          height="20"
                          mb-0
                          class="!text-field_details"
                         ></v-text-field>
                    </td>
                    <td v-if="!order_editing[props.index].show">{{ props.item.marketplace }}</td>
                    <td v-else >
                      <v-text-field
                          v-model="props.item.marketplace"
                          label="Edit"
                          single-line
                          height="20"
                          mb-0
                          class="!text-field_details"
                         ></v-text-field>
                    </td>
                    <td v-if="!order_editing[props.index].show">{{ props.item.location }}</td>
                    <td v-else >
                      <v-text-field
                          v-model="props.item.location"
                          label="Edit"
                          single-line
                          height="20"
                          mb-0
                          class="!text-field_details"
                         ></v-text-field>
                    </td>
                    <td v-if="!order_editing[props.index].show">{{ props.item.price }}</td>
                    <td v-else >
                      <v-text-field
                          v-model="props.item.price"
                          label="Edit"
                          single-line
                          height="20"
                          mb-0
                          class="!text-field_details"
                         ></v-text-field>
                    </td>
                    <td
                       class="layout"
                       v-if="!order_editing[props.index].show">
                             <v-icon class="mr-2 align-center pt-4" small  @click="updateOrderList(props.index)"> edit </v-icon>
                             <v-icon class="mr-2 align-center pt-4" small @click="deleteOrder(props.item)"> delete </v-icon>
                   </td>
                   <td
                     class="layout"
                     v-else >
                       <v-btn class="white--text" color="red" @click="updateOrderList(props.index)">cancel</v-btn>
                       <v-btn class="white--text" color="blue darken-1" @click="updateOrder(props.item, props.index)">save</v-btn>
                   </td>
                 </template>
                 <template v-slot:no-results>
                   <v-alert :value="true" color="error" icon="warning">
                     Your search for "{{ search }}" found no results.
                   </v-alert>
                 </template>
                </v-data-table>
                  </v-container>
                          <v-container grid-list-lg fluid v-if="page == 'admin'">
                              <h1> Admin</h1>
                          </v-container>
                      </v-content>
                  </v-app>
              </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld';
import {VueCsvImport} from 'vue-csv-import';
const url = "https://codeschool-inventory-project.herokuapp.com";

export default {
  name: 'App',
  components: {
    // HelloWorld,
    VueCsvImport
  },
  data () {
    return {
        csv: null,
        todaysdate: new Date(),
        detail: "",
        calTitle: "",
        page: "dashboard",
        drawer: true,
        date: new Date().toISOString().substr(0, 10),
        menu: false,
        dialog: false,
        dialogRegister: false,
        dialogOrder: false,
        csvDialog: false,
        editing: [],
        order_editing: [],
        currentUser: "",
        login_username: "",
        login_password: "",
        register_username: "",
        register_password: "",
        isLoggedIn: false,
        show1: false,
        show2: false,
        search: '',
        max25chars: v => v.length <= 25 || 'Input too long!',
        items: [
            { title: 'DashBoard', icon: 'dashboard', page: 'dashboard'},
            { title: 'Inventory', icon: 'shopping_cart', page: 'inventory' },
            { title: 'Orders', icon: 'store', page: 'orders' },
            { title: 'Admin', icon: 'person', page: 'admin' }

          ],
          headers: [
          {
            text: 'MarketPlace',
            align: 'left',
            sortable: false,
            value: 'marketplace'
          },
          { text: 'Image', value: 'Image' },
          { text: 'Title', value: 'title' },
          { text: 'Category', value: 'category' },
          { text: 'qty', value: 'quantity' },
          { text: 'sku', value: 'sku' },
          { text: 'Location', value: 'location' },
          { text: 'Cost', value: 'cost' },
          { text: 'Actions', value: 'name', sortable: false }
        ],
        Orderheaders: [
        {
          text: 'Customer',
          align: 'left',
          sortable: false,
          value: 'name'
        },
        { text: 'Sku', value: 'SKU' },
        { text: 'Title', value: 'Title' },
        { text: 'Category', value: 'Category' },
        { text: 'qty', value: 'Qty' },
        { text: 'MarketPlace', value: 'MarketPlace' },
        { text: 'Location', value: 'Location' },
        { text: 'Price', value: 'Price' },
        { text: 'Actions', value: 'name', sortable: false }
      ],
        events: [
            {
              title: 'Vacation',
              details: 'Going to the beach!',
              date: '2019-07-12',
              open: false
            },
            {
              title: 'Vacation',
              details: 'Going to the beach!',
              date: '2019-07-11',
              open: false
            },
            {
              title: 'Vacation',
              details: 'Going to the beach!',
              date: '2019-07-15',
              open: false
            },
        ],
        filteredItems: [],
        filteredOrders: [],
        marketplaces: [],
        marketplaceType: "all",
        categories: ["all", "shoe", "cooking", "books", "sports", "entertainment"],
        categoryType: "all",

        inventory: [],
          newSku: "",
          newImage: "",
          newTitle: "",
          newCategory: "",
          newMarketplace: "",
          newQuantity: "",
          newCost: "",
          newLocation: "",
          order: [],
          newOrderCustomer: "",
          newOrderSku: "",
          newOrderTitle: "",
          newOrderCategory: "",
          newOrderMarketplace: "",
          newOrderQuantity: "",
          newOrderPrice: "",
          newOrderLocation: "",

          re_render: true,
        }
    },
    created: function() {
        this.getInventory();
        this.getOrder();
    },
    methods: {
        sendData: function() {
            var app = this;
            console.log(this.csv);
            for (var item in this.csv) {
                var req_body = {
                    sku: this.csv[item].sku,
                    image: this.csv[item].image,
                    title: this.csv[item].title,
                    category: this.csv[item].category,
                    marketplace: this.csv[item].marketplace,
                    quantity: this.csv[item].qty,
                    cost: this.csv[item].cost,
                    location: this.csv[item].location,
                    date: new Date().toISOString().substr(0, 10)
                };
                console.log(req_body);
                fetch(`${url}/inventory`, {
                    method: "POST",
                    headers: {
                        "Content-type": "application/json"
                    },
                    body: JSON.stringify(req_body)
                }).then(function(response) {
                    console.log(response);
                });
            }
        },
        newevent: function(){
            var app = this;
            var new_event = {
                title: this.calTitle,
                details: this.detail,
                date: this.date,
                open: false,
            };
            this.events.push(new_event);
        },

        login: function() {
            var app = this;
            fetch(`${url}/users/login`, {
                method: "POST",
                credentials: "include",
                headers: {
                    "Content-type": "application/json"
                },
                body: JSON.stringify({
                    username: this.login_username,
                    password: this.login_password
                })
            }).then(function(response) {
                if (response.status == 200) {
                    response.json().then(function(data) {
                        app.isLoggedIn = true;
                        app.getInventory();
                        app.getOrder();
                    });
                }
                else if (response.status == 403) {
                    response.json().then(function(data) {
                        alert(data.msg);
                    })
                }
            });
        },
        logout: function() {
            var app = this;
            fetch(`${url}/logout`, {
              credentials: "include"
          }).then(function(response) {
                if(response.status == 200 ) {
                  console.log(response.status)
                    app.isLoggedIn = false;
                    console.log(app.isLoggedIn);
                    // app.getInventory();
                } else {
                    response.json().then(function(data) {
                        alert(data.msg);
                    });
                }
            });
        },
        register: function() {
            var app = this;
            fetch(`${url}/users/register`, {
                method: "POST",
                credentials: "include",
                headers: {
                    "Content-type": "application/json"
                },
                body: JSON.stringify({
                    username: this.register_username,
                    password: this.register_password
                })
            }).then(function(response) {
                if (response.status == 422 || response.status == 400) {
					response.json().then(function(data) {
						alert(data.msg);
                        app.isLoggedIn = true;
					})
				} else if (response.status == 201) {
					console.log("It worked");
                    app.isLoggedIn = false;
                    app.getInventory();
                    app.getOrder();
				}
            });
        },

        updateOrderList: function(index) {
            var app = this;
            var new_editing = this.order_editing;
            new_editing[index].show = !new_editing[index].show;
            this.order_editing = new_editing;
            console.log(this.order_editing);
        },

        updateList: function(index) {
            var app = this;
            var new_editing = this.editing;
            new_editing[index].show = !new_editing[index].show;
            this.editing = new_editing;
            this.getInventory();
            // console.log(this.editing);
        },

        cancelnewitem: function(){
            this.dialog = false;
        },
        cancelNewOrder: function(){
            this.dialogOrder = false;
        },
        cancelCsvUpload: function(){
            this.csvDialog = false;
        },
        getInventory: function() {
            var app = this;
            console.log("Getting Inventory");
            fetch(`${url}/inventory`, {
              credentials: "include",
            }).then(function(response) {
              if (response.status == 403) {
                this.isLoggedIn = false
              } else {
                response.json().then(function(data) {
                    console.log(data);
                    app.isLoggedIn = true;
                    app.inventory = data.inventory;
                    app.currentUser = data.user_name;
                    app.marketplaceList;
                    app.inventory.forEach(function(product) {
                      app.editing.push({show: false});
                    });
                });
              }
            });
        },
        addItem: function() {
            var app = this;
            console.log("Adding Item");
            var req_body = {
                sku: this.newSku,
                image: this.newImage,
                title: this.newTitle,
                category: this.newCategory,
                marketplace: this.newMarketplace,
                quantity: this.newQuantity,
                cost: this.newCost,
                location: this.newLocation,
                date: this.date,
            };
            fetch(`${url}/inventory`, {
                method: "POST",
                credentials: "include",
                headers: {
                    "Content-type": "application/json"
                },
                body: JSON.stringify(req_body)
            }).then(function(response) {
                if(response.status == 400) {
                    response.json().then(function(data) {
                        alert(data.msg);
                    })
                } else if (response.status == 201) {
                    app.newSku = "";
                    app.newImage = "";
                    app.newTitle = "";
                    app.newCategory = "";
                    app.newMarketplace = "";
                    app.newQuantity = "";
                    app.newCost = "";
                    app.newLocation = "";
                    app.dialog= false;
                    app.newevent();
                    app.getInventory();
                }
            });
        },
        deleteItem: function(item) {
            var app = this;
            console.log("Deleting item");
            confirm("Are you sure you want to delete this item?");
            fetch(`${url}/inventory/${item._id}`, {
                method: "DELETE",
                credentials: "include",
            }).then(function(response) {
                if(response.json == 404) {
                    response.json().then(function(data) {
                        alert(data.msg);
                    });
                } else if(response.status == 204) {
                    app.getInventory();
                }
            });
        },
        updateInventory: function(item, index) {
            var app = this;
            console.log("Updating item");
            var req_body = {
                sku: item.sku,
                image: item.image,
                title: item.title,
                category: item.category,
                marketplace: item.marketplace,
                quantity: item.quantity,
                cost: item.cost,
                location: item.location
            };
            fetch(`${url}/inventory/${item._id}`, {
                method: "PUT",
                credentials: "include",
                headers: {
                    "Content-type": "application/json"
                },
                body: JSON.stringify(req_body)
            }).then(function(response) {
                if(response.status == 400 || response.status == 404) {
                    response.json().then(function(data) {
                        alert(data.msg);
                    });
                } else if(response.status == 200) {
                  console.log(app.editing)
                    item.editing = false;
                    app.getInventory();
                    app.editing[index].show = !app.editing[index].show;
                }
            });
        },
        getOrder: function() {
            var app = this;
            console.log("Getting Order");
            fetch(`${url}/order`, {
              credentials: "include",
            }).then(function(response) {
              if (response.status == 403) {
                this.isLoggedIn = false
              } else {
                response.json().then(function(data) {
                    console.log(data);
                    app.isLoggedIn = true;
                    app.order = data.order;
                    app.marketplaceList;
                    app.order.forEach(function(order) {
                     app.order_editing.push({show: false});
                    });
                });
              }
            });
        },
        addOrder: function() {
            var app = this;
            console.log("Adding Order");
            var req_body = {
                customer: this.newOrderCustomer,
                sku: this.newOrderSku,
                title: this.newOrderTitle,
                category: this.newOrderCategory,
                marketplace: this.newOrderMarketplace,
                quantity: this.newOrderQuantity,
                price: this.newOrderPrice,
                location: this.newOrderLocation
            };
            fetch(`${url}/order`, {
                method: "POST",
                headers: {
                    "Content-type": "application/json"
                },
                body: JSON.stringify(req_body)
            }).then(function(response) {
                if(response.status == 400) {
                    response.json().then(function(data) {
                        alert(data.msg);
                    })
                } else if (response.status == 201) {
                    app.newOrderCustomer = "";
                    app.newOrderSku = "";
                    app.newOrderTitle = "";
                    app.newOrderCategory = "";
                    app.newOrderMarketplace = "";
                    app.newOrderQuantity = "";
                    app.newOrderPrice = "";
                    app.newOrderLocation = "";
                    app.dialogOrder= false;
                    app.getOrder();
                }
            });
        },
        deleteOrder: function(order) {
            var app = this;
            console.log("Deleting order");
            confirm("Are you sure you want to delete this order?");
            fetch(`${url}/order/${order._id}`, {
                method: "DELETE"
            }).then(function(response) {
                if(response.json == 404) {
                    response.json().then(function(data) {
                        alert(data.msg);
                    });
                } else if(response.status == 204) {
                    app.getOrder();
                }
            });
        },
        updateOrder: function(order, index) {
            var app = this;
            console.log("Updating order");
            var req_body = {
                customer: order.customer,
                sku: order.sku,
                title: order.title,
                category: order.category,
                marketplace: order.marketplace,
                quantity: order.quantity,
                price: order.price,
                location: order.location
            };
            fetch(`${url}/order/${order._id}`, {
                method: "PUT",
                headers: {
                    "Content-type": "application/json"
                },
                body: JSON.stringify(req_body)
            }).then(function(response) {
                if(response.status == 400 || response.status == 404) {
                    response.json().then(function(data) {
                        alert(data.msg);
                    });
                } else if(response.status == 200) {
                  console.log(app.order_editing)
                    order.order_editing = false;
                    app.getOrder();
                    app.order_editing[index].show = !app.order_editing[index].show;
                }
            });
        },
    },
    computed: {
        eventsMap() {
            const map = {}
            this.inventory.forEach(e => (map[e.date] = map[e.date] || []).push(e))
            return map
        },
        marketplaceList: function() {
            this.marketplaces = ["all"];
            this.inventory.forEach((item) => {
                if (!this.marketplaces.includes(item.marketplace)) {
                    this.marketplaces.push(item.marketplace);
                }
            })
        },
        filteredItem: function() {
            return this.filteredCategory.filter((i) => {
                return this.filteredMarketplace.includes(i);
            })
        },
        filteredMarketplace: function() {
            return this.inventory.filter((i) => {
                return this.marketplaceType == "all" || (i.marketplace == this.marketplaceType);
            })
        },
        filteredCategory: function() {
            return this.inventory.filter((i) => {
                return this.categoryType == "all" || (i.category == this.categoryType);
            })
        },
        filteredOrders: function() {
            return this.filteredCategory.filter((i) => {
                return this.filteredMarketplace.includes(i);
            })
        },

    }
}
</script>

<style>
.form-control {
    background-color: #F5F5F5;
    -webkit-appearance: menulist-button;
}
</style>
