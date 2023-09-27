<template>
    <div> 
    <v-data-table
      :headers="headers"
      :items="productItem"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar
          flat
        >
          <v-toolbar-title>Table Product</v-toolbar-title>
          <v-divider
            class="mx-4"
            inset
            vertical
          ></v-divider>
          <v-spacer></v-spacer>
          <v-btn
                color="primary"
                dark
                class="mb-2"
                @click="openDialog('add', defaultItem)"
              >
                เพิ่มข้อมูล
              </v-btn>
        </v-toolbar>
      </template>
      <!-- <template v-slot:[`item.role`]="{ item }">
        {{ item.role.name }}
      </template> -->
      <template v-slot:[`item.actions`]="{ item }">
        <v-btn small outlined
          class="mr-2"
          @click="openDialog('edit', item)"
          color="blue">
          <v-icon>
          mdi-pencil
        </v-icon>
        </v-btn>
        <v-btn small outlined
          @click="deleteItem(item)"
          color="red" class="m1-2">
          <v-icon>
          mdi-delete
        </v-icon>
        </v-btn>
      </template>
      <template v-slot:no-data>
        <v-btn
          color="primary"
          @click="initialize"
        >
          Reset
        </v-btn>
      </template>
    </v-data-table>
    <v-dialog v-model="dialogCreate" max-width="500px">
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>
  
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col
                      cols="12"
                      sm="6"
                      md="6"
                    >
                      <v-text-field
                        v-model="productName"
                        label="ชื่อ"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="6"
                    >
                      <v-text-field
                        v-model="productPrice"
                        label="ราคา"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="6"
                    >
                      <v-text-field
                        v-model="productAmount"
                        label="จำนวน"
                      ></v-text-field>
                    </v-col>
                    <!-- <v-col
                      cols="12"
                      sm="6"
                      md="6"
                    >
                      <v-text-field
                        v-model="role"
                        label="ตำแหน่ง"
                      ></v-text-field>
                    </v-col> -->
                    
                  </v-row>
                </v-container>
              </v-card-text>
  
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  color="blue darken-1"
                  text
                  @click="close"
                >
                  ยกเลิก
                </v-btn>
                <v-btn
                  color="blue darken-1"
                  text
                  @click="save(formTitle)"
                >
                  บันทึก
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">คุณต้องการลบข้อมูลนี้ในตาราง ใช่ หรือ ไม่?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete">ยกเลิก</v-btn>
                <v-btn color="blue darken-1" text @click="deleteItemConfirm">ตกลง</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
      </div> 
  </template>
  
  <script>
    export default {
      data: () => ({
        productName: '',
        productPrice: '',
        productAmount: '',
        dialogCreate: false,
        dialogDelete: false,
        
        headers: [
          {
            text: 'ไอดี',
            align: 'start',
            sortable: false,
            value: 'productId',
          },
          { text: 'ชื่อ', value: 'productName' },
          { text: 'ราคา', value: 'productPrice' },
          { text: 'จำนวน', value: 'productAmount' },
          { text: 'จัดการ', value: 'actions', sortable: false },
        ],
        productItem: [],
        editedIndex: -1,
        editedItem: {
          name: '',
          calories: 0,
          fat: 0,
          carbs: 0,
          protein: 0,
        },
        defaultItem: {
          name: '',
          calories: 0,
          fat: 0,
          carbs: 0,
          protein: 0,
        },
        formTitle: '',
        idProduct: '',
        idForDelete: ''
      }),
  
      watch: {
        dialog (val) {
          val || this.close()
        },
        dialogDelete (val) {
          val || this.closeDelete()
        },
      },
  
      created () {
        this.initialize()
      },
  
      methods: {
        async initialize () {
          this.productItem = []
          try {
            var data = await this.axios.get('http://172.28.48.249:9000/product')
            console.log('data product ====>', data)
            this.productItem = data.data
          } catch (error) {
            
          }
        },
        openDialog(Action, item) {
          //console.log(Action, item)
          this.formTitle = ''
          if (Action === 'add') {
              this.dialogCreate = true
              this.formTitle = 'เพิ่มข้อมูล'
          } else  {
              this.dialogCreate = true
              this.formTitle = 'แก้ไขข้อมูล'
              this.productName = item.productName
              this.productPrice = item.productPrice
              this.productAmount = item.productAmount

              this.idProduct = item.productId
          }
        },
  
        editItem (item) {
          this.editedIndex = this.desserts.indexOf(item)
          this.editedItem = Object.assign({}, item)
          this.dialog = true
        },
  
        deleteItem (item) {
          this.idForDelete = item.productId
          this.dialogDelete = true
        },
  
        async deleteItemConfirm () {
          try {
            var response = await this.axios.delete('http://172.28.48.249:9000/product/' + this.idForDelete)
            this.initialize()
          } catch (error) {
            
          }
          this.closeDelete()
        },
  
        closeDelete () {
          this.dialogDelete = false
          this.$nextTick(() => {
            this.editedItem = Object.assign({}, this.defaultItem)
            this.editedIndex = -1
          })
        },
  
        close () {
          this.dialogCreate = false
          this.productName = ''
          this.productPrice = ''
          this.productAmount = ''
          
          
        },
  
        async save (action) {
          var data = {
              productName: this.productName,
              productPrice: this.productPrice,
              productAmount: this.productAmount
    
            }
          if(action === 'เพิ่มข้อมูล') {
            // this.desserts.push(this.editedItem)
            //console.log('data after send ===>', data)
            try {
              var dataResponse = await this.axios.post('http://172.28.48.249:9000/product', data)
              console.log('dataResponse ===>', dataResponse)
              this.close()
              this.initialize()
            } catch (error) {
              console.log(error.message)
            }
          } else {
            try {
              var dataResponseEdit = await this.axios.put('http://172.28.48.249:9000/product/' + this.idProduct, data)
              console.log('dataResponse ===>', dataResponseEdit)
              this.close()
              this.initialize()
            } catch (error) {
              console.log(error.message)
            }
  
          }
          this.close()
        },
      },
    }
  </script>
  
  <style>
  
  </style>