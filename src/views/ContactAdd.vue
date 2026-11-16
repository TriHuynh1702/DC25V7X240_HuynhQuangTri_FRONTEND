
<template>
  <div class="page">
    <h4>Thêm mới Liên hệ</h4>

    <ContactForm
      :contact="{}"
      @submit:contact="createContact"
    />
  </div>
</template>

<script>
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

export default {
  components: {
    ContactForm,
  },

  methods: {
    async createContact(data) {
      try {
        await ContactService.create(data);

        alert("Liên hệ được thêm thành công.");

        this.$router.push({ name: "contactbook" });
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style scoped>
.page {
  text-align: left;
  max-width: 750px;
}
</style>
