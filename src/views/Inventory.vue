<template>
    <v-app>
        <v-container grid-list-lg fluid>
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
    </v-app>
</template>

<script>

  export default {

  }
</script>
