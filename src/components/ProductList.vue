<template>
  <div class="product-list">
    <div class="data">
      <table class="ui celled table">
        <thead>
          <tr>
            <th style="width: 50px; text-align: center;">#</th>
            <th>Title</th>
            <th>Description</th>
            <th>Price</th>
            <th style="width: 148px;">Action</th>
          </tr>
        </thead>

        <tbody>
          <product
            v-for="product in product"
            :key="product.id"
            :product="product"
            @onDelete="onDelete"
            @onEdit="onEdit"
          />
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import product from "./Product";
export default {
  name: "ProductList",
  components: {
    product
  },
  props: {
    product: {
      type: Array
    }
  },
  methods: {
    onDelete(id) {
      // window.console.log("product list delete " + id);
      this.$emit("onDelete", id);
    },
    onEdit(data) {
      // window.console.log("product list edit " + data);
      this.$emit("onEdit", data);
    }
  }
};
</script>

<style scoped></style>
