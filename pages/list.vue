<template>
  <v-data-table :headers="headers" :items="arr" sort-by="calories" class="elevation-1" :search="search">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>База данных</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Поиск..."
          single-line
          hide-details
        ></v-text-field>
        <v-spacer></v-spacer>

        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
              Добавить
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="12" md="12">
                    <v-text-field v-model="editedItem.name" label="Имя"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="12" md="12">
                    <v-date-picker v-model="editedItem.date" label="Дата рождения"></v-date-picker>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Выйти</v-btn>
              <v-btn color="blue darken-1" text @click="save">Сохранить</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5 t">Подтвердить удаление?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">Да</v-btn>
              <v-btn color="blue darken-1" text @click="closeDelete">Отмена</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon small class="mr-2" @click="editItem(item)">mdi-pencil</v-icon>
      <v-icon small @click="deleteItem(item)">mdi-delete</v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>

<script>
function randomDate(start, end) {
  return new Date(start.getTime()
    + Math.random() * (end.getTime() - start.getTime()));
}
function format(date){
  var dd = date.getDate();
  if (dd < 10) dd = '0' + dd;

  var mm = date.getMonth() + 1;
  if (mm < 10) mm = '0' + mm;

  var yy = date.getFullYear();
  if (yy < 10) yy = '0' + yy;

  return yy + '-' + mm + '-' + dd;
}
var arr = [];
var maxId;
for (let i = 1; i<=100; i++){
  var randDate = randomDate(new Date(1980,0,1), new Date());
  var date = format(randDate);
  var contact = {
    id : i,
    name: 'name-' + i,
    date: (date),
  }
  maxId = i + 1;
  arr.push(contact);
}

export default {
  name: "list",
  data: function (){
    return {
      dialog: false,
      dialogDelete: false,
      arr,
      maxId,
      search: '',
      headers: [
        {text: 'ID', value: 'id'},
        {
          text: 'Имя',
          align: 'start',
          sortable: false,
          value: 'name',
        },
        {text: 'Дата рождения', value: 'date'},
        { text: 'Действия', value: 'actions', sortable: false },
      ],
      editedIndex: -1,
      editedItem: {
        id: maxId++,
        name: '',
        date: '',
      },
      defaultItem: {
        id: maxId++,
        name: '',
        date: '',
      },
    }
  },
  computed: {
    formTitle () {
      return this.editedIndex === -1 ? 'Новый пользователь' : 'Редактировать'
    },
  },

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
    initialize () {
      this.arr = arr
    },

    editItem (item) {
      this.editedIndex = this.arr.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem (item) {
      this.editedIndex = this.arr.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },

    deleteItemConfirm () {
      this.arr.splice(this.editedIndex, 1)
      this.closeDelete()
    },

    close () {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete () {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    save () {
      if (this.editedIndex > -1) {
        Object.assign(this.arr[this.editedIndex], this.editedItem)
      } else {
        this.arr.push(this.editedItem)
      }
      this.close()
    },
  },

}
</script>

<style scoped>

</style>
