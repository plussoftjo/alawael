<template>
  <div>
    <v-toolbar flat color="black">
      <v-toolbar-title>Orders</v-toolbar-title>
      <v-divider
        class="mx-2"
        inset
        vertical
      ></v-divider>
      <v-spacer></v-spacer>
      <v-dialog v-model="dialog" max-width="500px">
        <v-card>
          <v-card-title color="blue" class="pb-0 mb-0">
            <span class="headline">Order Info</span>
          </v-card-title>
            <v-container grid-list-md justify-center align-item>
              <v-layout row wrap>
              	<v-flex xs12>
              		<div style="font-weight: 700; font-size: 18px;">
              			Name : <span style="font-size: 16px;">{{editedItem.name}}</span>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			Phone : <span style="font-size: 16px;">{{editedItem.phone}}</span>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			Address : <span style="font-size: 16px;">{{editedItem.address}}</span>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			city : <span style="font-size: 16px;">{{editedItem.name}}</span>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			ZIP CODE : <span style="font-size: 16px;">{{editedItem.post}}</span>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			ID IMAGE : 
              			<v-img :src="editedItem.image" width="150px" height="150px"></v-img>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			Info IMAGE : 
              			<v-img :src="editedItem.imageI" width="150px" height="150px"></v-img>
              		</div>
              	</v-flex>
              </v-layout>
            </v-container>
        </v-card>
        <v-card>
          <v-card-title color="blue" class="pb-0 mb-0">
            <span class="headline">CV info</span>
          </v-card-title>
            <v-container grid-list-md justify-center align-item>
              <v-layout row wrap>
              	<v-flex xs12>
              		<div style="font-weight: 700; font-size: 18px;">
              			CV ID : <span style="font-size: 16px;">{{editedItem.cv.id}}</span>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			Name : <span style="font-size: 16px;">{{editedItem.cv.name}}</span>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			refCode : <span style="font-size: 16px;">{{editedItem.cv.refcode}}</span>
              		</div>
              		<div style="font-weight: 700; font-size: 18px;">
              			IMAGE : 
              			<v-img :src="editedItem.cv.image" width="150px" height="150px"></v-img>
              		</div>
              	</v-flex>
              </v-layout>
            </v-container>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" flat @click="close">Cancel</v-btn>
            <v-btn color="blue darken-1" flat @click="save">Done</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-toolbar>

    <v-data-table
      :headers="headers"
      :items="desserts"
      class="elevation-1 "
    >
      <template v-slot:items="props" >
        <td class="text-xs-center ">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center">{{ props.item.phone }}</td>
        <td class="text-xs-center">{{ props.item.created_at }}</td>
        <td class="text-xs-center">
			<span class="green--text" v-if="props.item.status">Done</span>
			<span class="red--text" v-else>Not Done</span>
        </td>
        <td class="justify-center layout px-0">
          <v-icon
            small
            class="mr-2"
            @click="editItem(props.item)"
          >
            edit
          </v-icon>
        </td>
      </template>
    </v-data-table>

  </div>
</template>
<script>
  export default {
    data: () => ({
      dialog: false,
      headers: [
        {
          text: '#',
          align: 'center',
          sortable: false,
          value: 'id'
        },
        { text: 'name', value: 'name',sortable: false,align: 'center', },
        { text: 'phone', value: 'phone',sortable: false,align: 'center', },
        { text: 'Order Date', value: 'phone',sortable: false,align: 'center', },
        { text: 'status', value: 'status',sortable: false,align: 'center', },
        { text: 'Index', value: 'Index',sortable: false,align: 'center', },
      ],
      desserts: [],
      editedIndex: -1,
      editedItem: {
       cv:{}
      },
      defaultItem: {
        title: '',
       	image:null
      }
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      }
    },

    watch: {
      dialog (val) {
        val || this.close()
      }
    },

    created () {
      this.initialize()
    },

    methods: {
      initialize () {
        const vm = this;
        axios.get('admin/orders/index').then(response => {
          vm.desserts = response.data;
        }).catch(err => {
          console.log(err)
        });
      },

      editItem (item) {
        this.editedIndex = this.desserts.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        const index = this.desserts.indexOf(item)
        confirm('Are you sure you want to delete this item?') && 
        axios.get('admin/country/destroy/' + item.id).then(response => {
          this.desserts.splice(index, 1)
        }).catch(err => {
          console.log(err)
        });
      },

      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },

      save () {
      	const vm = this;
        axios.get('admin/orders/done/' + vm.editedItem.id).then(response => {
        	location.reload();
        }).catch(err => {
        	console.log(err)
        });
      },
      onFileChange(e) {
	    let files = e.target.files || e.dataTransfer.files;
	     if (!files.length)
	        return;
	    this.createImage(files[0]);
	    },
	    createImage(file) {
	     let reader = new FileReader();
	      let vm = this;
	      reader.onload = (e) => {
	          vm.editedItem.image = e.target.result;
	      };
	      reader.readAsDataURL(file);
	   }
    }
  }
</script>