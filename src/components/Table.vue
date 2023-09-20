<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <div>
          <v-data-table :headers="headers" :items="employeeItem" sort-by="calories" class="elevation-1">
            <template v-slot:top>
              <v-toolbar flat>
                <v-toolbar-title>จัดการข้อมูล</v-toolbar-title>
                <v-divider class="mx-4" inset vertical></v-divider>
                <v-spacer></v-spacer>
                <v-btn color="primary" dark class="mb-2" @click="openDialog('add', defaultItem)">
                  เพิ่มข้อมูล
                </v-btn>
              </v-toolbar>
            </template>
            <template v-slot:[`item.role`]="{ item }">
              {{ item.role.name }}
            </template>
            <template v-slot:[`item.actions`]="{ item }">
              <v-icon small class="mr-2" @click="openDialog('edit', item)" color="blue">
                mdi-pencil
              </v-icon>
              <v-icon small @click="deleteItem(item)" color="red" class="ml-2">
                mdi-delete
              </v-icon>
            </template>
            <template v-slot:no-data>
              <v-btn color="primary" @click="initialize">
                Reset
              </v-btn>
            </template>
          </v-data-table>
          <v-dialog v-model="dialog" max-width="500px">
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field v-model="firstName" label="ชื่อ"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field v-model="lastName" label="นามสกุล"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field v-model="salary" label="เงินเดือน"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field v-model="role" label="ตำแหน่ง"></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  ยกเลิก
                </v-btn>
                <v-btn color="blue darken-1" text @click="save(formTitle)">
                  บันทึก
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">แน่ใจนะ? ว่าจะลบอ่ะ</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="deleteItemConfirm()">ยืนยัน</v-btn>
                <v-btn color="blue darken-1" text @click="closeDelete()">ยกเลิก</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
  data: () => ({
    firstName: '',
    lastName: '',
    salary: '',
    role: '',
    employeeItem: [],
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        text: 'ไอดี',
        align: 'start',
        sortable: false,
        value: 'id',
      },
      { text: 'ชื่อ', value: 'firstName' },
      { text: 'นามสกุล', value: 'lastName' },
      { text: 'เงินเดือน', value: 'salary' },
      { text: 'ตำแหน่ง', value: 'role' },
      { text: 'Actions', value: 'actions', sortable: false },
    ],
    desserts: [],
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
    IdEmployee:'',
    IdForDelete:''
  }),

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    initialize() {
      this.desserts = [
        {
          name: 'Frozen Yogurt',
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
        },
        {
          name: 'Ice cream sandwich',
          calories: 237,
          fat: 9.0,
          carbs: 37,
          protein: 4.3,
        },
        {
          name: 'Eclair',
          calories: 262,
          fat: 16.0,
          carbs: 23,
          protein: 6.0,
        },
        {
          name: 'Cupcake',
          calories: 305,
          fat: 3.7,
          carbs: 67,
          protein: 4.3,
        },
        {
          name: 'Gingerbread',
          calories: 356,
          fat: 16.0,
          carbs: 49,
          protein: 3.9,
        },
        {
          name: 'Jelly bean',
          calories: 375,
          fat: 0.0,
          carbs: 94,
          protein: 0.0,
        },
        {
          name: 'Lollipop',
          calories: 392,
          fat: 0.2,
          carbs: 98,
          protein: 0,
        },
        {
          name: 'Honeycomb',
          calories: 408,
          fat: 3.2,
          carbs: 87,
          protein: 6.5,
        },
        {
          name: 'Donut',
          calories: 452,
          fat: 25.0,
          carbs: 51,
          protein: 4.9,
        },
        {
          name: 'KitKat',
          calories: 518,
          fat: 26.0,
          carbs: 65,
          protein: 7,
        },
      ]
    },

    openDialog(Actions, item) {
      // console.log(Actions, item)
      this.formTitle = ''
      if (Actions === 'add') {
        this.dialog = true
        this.formTitle = 'เพิ่มข้อมูล'
      } else {
        this.formTitle = 'แก้ไขข้อมูล'
        this.dialog = true
        this.firstName = item.firstName
        this.lastName = item.lastName
        this.salary = item.salary
        this.role = item.role.name
        this.IdEmployee = item.id
      }
    },
    async initialize() {
      this.employeeItem = []
      try {
        var data = await this.axios.get('http://localhost:9000/employee')
        console.log('data employee ====>', data)
        this.employeeItem = data.data
      } catch (error) {

      }
    },

    editItem(item) {
      console.log('item select', item);
      console.log('index item', this.deserts.indexOf(item));
      this.dialog = true
    },

    deleteItem(item) {
      this.IdForDelete = item.id
      this.dialogDelete = true
    },

    async deleteItemConfirm() {
      try{
      var response = await this.axios.delete('http://localhost:9000/employee/'+ this.IdForDelete)
      console.log('response ====>', response)
      this.initialize()
      }catch (error){
        console.log(error.message);
      }
      this.closeDelete()
    }, 

    close() {
      this.dialog = false
      this.firstName = ''
      this.lastName= ''
      this.salary= ''
      this.role= ''
    },
    async save(Actions) {
      if (Actions === 'เพิ่มข้อมูล') {
        // this.desserts.push(this.editedItem)
        var data = {
          firstName: this.firstName,
          lastName: this.lastName,
          salary: this.salary,
          role: {
            name: this.role
          },
          skills:[
            {skill: ''}
          ]
        }
        try {
          var dataResponse = await this.axios.post('http://localhost:9000/employee', data)
          console.log('dataResponse ====>', dataResponse)
          this.close()
          this.initialize()
        } catch (error) {
          console.log(error.message);
        }
      console.log('data after send ====>', data);
    } else {
      try {
          var dataResponseEdit = await this.axios.put('http://localhost:9000/employee/'+ this.IdEmployee, data)
          console.log('dataResponse ====>', dataResponseEdit)
          this.close()
          this.initialize()
        } catch (error) {
          console.log(error.message);
        }
    } this.close()
  },

  closeDelete() {
    this.dialogDelete = false
    this.editedItem = []
    this.editedIndex = -1

  },


},
};
</script>

<style></style>