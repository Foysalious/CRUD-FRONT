<template>
  <div class="product-list">
    <div class="data">
      
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      product: [],
      dialog: false,
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
    deleteItem(item){
      console.log(item.id)
      axios.post(`http://127.0.0.1:8000/api/Product/delete/${item.id}`,{})
      .then( res => {
        this.product.filter( (value,index) => {
          if( res.data.product.id == value.id ){
            return this.product.splice(index,1)
          }
        })
      })
    }
  },
};
</script>

<style scoped></style>
