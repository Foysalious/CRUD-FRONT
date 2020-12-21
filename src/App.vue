<template>
  <div id="app" data-app>
    <div class="ui fixed inverted menu vue-color">
      <div class="ui container">
        <a href="#" class="header item"> VUE CRUD with Laravel API </a>
      </div>
    </div>

    <div class="ui main container">
      <MyForm :form="form" @onFormSubmit="onFormSubmit" />
      <Loader v-if="loader" />
      <table class="ui celled table">
        <thead>
          <tr>
            <th style="width: 50px; text-align: center">#</th>
            <th>Title</th>
            <th>Description</th>
            <th>Price</th>
            <th>Image</th>
            <th style="width: 148px">Action</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="item in product.data" :key="item.id">
            <td>{{ item.id }}</td>
            <td>{{ item.title }}</td>
            <td>{{ item.description }}</td>
            <td>{{ item.price }}</td>
            <td>
              <img :src="item.image" width="50px" alt="" />
            </td>
            <td>
              <v-dialog v-model="dialog" width="500">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn
                    color="red lighten-2"
                    dark
                    v-bind="attrs"
                    v-on="on"
                    @click="edit(item)"
                  >
                    Edit
                  </v-btn>
                </template>

                <v-card>
                  <form action="">
                    <v-row>
                      <v-col md="12">
                        <v-text-field
                          label="Title"
                          required
                          name="title"
                          v-model="editedItem.title"
                        ></v-text-field>
                      </v-col>
                      <v-col md="12">
                        <v-text-field
                          label="Description"
                          required
                          v-model="editedItem.description"
                        ></v-text-field>
                      </v-col>
                      <v-col md="12">
                        <v-text-field
                          label="Price"
                          required
                          v-model="editedItem.price"
                        ></v-text-field>
                      </v-col>
                      <v-col md="12">
                        <v-img
                          max-height="100"
                          max-width="100"
                          :src="editedItem.image"
                        ></v-img>
                        <v-file-input
                          label="File input"
                          name="image"
                          v-model="editedItem.image"
                          @change="onChange"
                        ></v-file-input>
                      </v-col>
                    </v-row>
                  </form>

                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="primary" text @click="saveData">
                      Submit
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
              <v-btn
                color="red lighten-2"
                dark
                v-bind="attrs"
                v-on="on"
                @click="deleteItem(item)"
              >
                Delete
              </v-btn>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import MyForm from "./components/MyForm";
import Loader from "./components/Loader";

export default {
  name: "App",
  components: {
    MyForm,
    Loader,
  },
  data() {
    return {
      url: "http://localhost:8000/api/Product",
      form: { title: "", description: "", price: "", image: "", isEdit: false },
      loader: false,
      product: [],
      dialog: false,
      image:"",
      editedItem: {
        title: "",
        description: "",
        price: "",
        image: "",
      },
    };
  },

  created() {
    this.initialize();
  },
  methods: {
    initialize() {
      axios.get("http://127.0.0.1:8000/api/Product", {}).then((res) => {
        this.product = res.data.product;
      });
    },
    
    onChange(e) {
      console.log(e)
            this.editedItem.image = e;
        },
    edit(item) {
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    saveData() {
      let id = this.editedItem.id;
      let form = new FormData();
      form.append("title", this.editedItem.title);
      form.append("description", this.editedItem.description);
      form.append("price", this.editedItem.price);
      form.append("image", this.editedItem.image);
      axios
        .post(`http://127.0.0.1:8000/api/Product/update/${id}`,form,{
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((res) => {
          this.product.data.filter((value, index) => {
            if (res.data.product.id == value.id) {
              this.dialog = false;
              return this.product.data.splice(index, 1, res.data.product);
            }
          });
        });
    },
    showModal() {
      this.dialog = true;
    },

    deleteItem(item) {
      axios
        .post(`http://127.0.0.1:8000/api/Product/delete/${item.id}`, {})
        .then((res) => {
          (this.loader = false),
            this.product.data.filter((value, index) => {
              if (res.data.product.id == value.id) {
                return this.product.data.splice(index, 1);
              }
            });
        });
    },
    deleteProduct(id) {
      this.loader = true;

      axios
        .delete(`${this.url}/${id}`)
        .then(() => {})
        .catch((e) => {
          alert(e);
        });
    },

    createProduct(data) {
      this.loader = true;
      let form = new FormData();
      form.append("title", data.title);
      form.append("description", data.description);
      form.append("price", data.price);
      form.append("image", data.image);
      axios
        .post("http://127.0.0.1:8000/api/Product", form, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((res) => {
          this.loader = false;
          this.product.data.push(res.data.product);
        })
        .catch((e) => {
          alert(e);
        });
    },

    onDelete(id) {
      // window.console.log("app delete " + id);

      this.deleteProduct(id);
    },
    onEdit(data) {
      // window.console.log("app edit ", data);

      this.form = data;
      this.form.isEdit = true;
    },
    onFormSubmit(data) {
      // window.console.log("app onFormSubmit", data);

      if (data.isEdit) {
        // call edit product
        this.editProduct(data);
      } else {
        // call create product
        this.createProduct(data);
      }
    },
  },
};
</script>

<style>
.vue-color {
  background: #41b883 !important;
}

.main.container {
  margin-top: 60px;
}

.submit-button {
  margin-top: 24px !important;
  float: right;
}

.data {
  margin-top: 15px;
}

thead tr th {
  background: #e0e0e0 !important;
}

.ui.inverted.dimmer {
  background-color: rgba(255, 255, 255, 0) !important;
}
</style>
