<template>
  <div class="my-form"> 
    <form class="ui form" enctype="multipart/form-data">
      <div class="fields">
        <div class="four wide field">
          <label>Title</label>
          <input
            type="text"
            name="title"
            placeholder="Title"
            @change="handleChange"
            :value="form.title"
          />
        </div>

        <div class="four wide field">
          <label>Description</label>
          <input
            type="text"
            name="description"
            placeholder="description"
            @change="handleChange"
            :value="form.description"
          />
        </div>

        <div class="six wide field">
          <label>Price</label>
          <input
            type="text"
            name="price"
            placeholder="Enter the Price"
            @change="handleChange"
            :value="form.price"
          />
        </div>

        <div class="six wide field">
          <label>Image</label>
          <input
            type="file"
            name="image"
            placeholder="File"
            @change="onChange"
            :value="form.image"
          />
        </div>

        <div class="two wide field">
          <button :class="btnClass" @click="onFormSubmit">
            {{ btnName }}
          </button>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "MyForm",
  data() {
    return {
      btnName: "Save",
      btnClass: "ui primary button submit-button",
    };
  },
  props: {
    form: {
      type: Object,
    },
  },
  methods: {
    onChange(e){
      this.form.image = e.target.files[0]
    },
    handleChange(event) {
      const { name, value } = event.target;
      let form = this.form;
      form[name] = value;
      this.form = form;
    },
    onFormSubmit(event) {
      // prevent form submit
      event.preventDefault();

      // form validation
      if (this.formValidation()) {
        // window.console.log("ready to submit");
        this.$emit("onFormSubmit", this.form);

        // change the button to save
        this.btnName = "Save";
        this.btnClass = "ui primary button submit-button";

        // clear form fields
        this.clearFormFields();
      }
    },
    formValidation() {
      // first name
      if (document.getElementsByName("title")[0].value === "") {
        alert("Enter Title");
        return false;
      }

      // last name
      if (document.getElementsByName("description")[0].value === "") {
        alert("Enter Description");
        return false;
      }

      // email
      if (document.getElementsByName("price")[0].value === "") {
        alert("Enter price");
        return false;
      }

      return true;
    },
    clearFormFields() {
      // clear form data
      // this.form.title = "";
      // this.form.description = "";
      // this.form.price = "";
      this.form.isEdit = false;

      // clear form fields
      document.querySelector(".form").reset();
    },
  },
  updated() {
    if (this.form.isEdit) {
      this.btnName = "Update";
      this.btnClass = "ui orange button submit-button";
    }
  },
};
</script>

<style scoped></style>
