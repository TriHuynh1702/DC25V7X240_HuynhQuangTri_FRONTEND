
<template>
  <div class="page row">
    <div class="col-md-10">
      <InputSearch v-model="searchText" />
    </div>

    <!-- Danh sách liên hệ -->
    <div class="mt-3 col-md-6">
      <h4>
        Danh bạ
        <i class="fas fa-address-book"></i>
      </h4>

      <ContactList
        v-if="filteredContactsCount > 0"
        :contacts="filteredContacts"
        v-model:activeIndex="activeIndex"
      />

      <p v-else>Không có liên hệ nào.</p>

      <!-- Các nút chức năng -->
      <div class="mt-3 row justify-content-around align-items-center">
        <button class="btn btn-sm btn-primary" @click="refreshList()">
          <i class="fas fa-redo"></i> Làm mới
        </button>

        <button class="btn btn-sm btn-success" @click="goToAddContact">
          <i class="fas fa-plus"></i> Thêm mới
        </button>

        <button class="btn btn-sm btn-danger" @click="removeAllContacts">
          <i class="fas fa-trash"></i> Xóa tất cả
        </button>
      </div>
    </div>

    <!-- Chi tiết liên hệ -->
    <div class="mt-3 col-md-6">
      <h4>
        Chi tiết Liên hệ
        <i class="fas fa-address-card"></i>
      </h4>

      <div v-if="activeContact">
        <ContactCard :contact="activeContact" />

        <router-link
          :to="{
            name: 'contact.edit',
            params: { id: activeContact._id },
          }"
        >
          <span class="mt-2 badge badge-warning">
            <i class="fas fa-edit"></i> Hiệu chỉnh
          </span>
        </router-link>
      </div>

      <p v-else>Chưa có liên hệ nào được chọn.</p>
    </div>
  </div>
</template>

<script>
import ContactCard from "@/components/ContactCard.vue";
import InputSearch from "@/components/InputSearch.vue";
import ContactList from "@/components/ContactList.vue";
import ContactService from "@/services/contact.service";

export default {
  components: {
    ContactCard,
    InputSearch,
    ContactList,
  },

  data() {
    return {
      contacts: [],
      activeIndex: -1,
      searchText: "",
    };
  },

  watch: {
    searchText() {
      this.activeIndex = this.filteredContacts.length > 0 ? 0 : -1;
    },
  },

  computed: {
    contactStrings() {
      return this.contacts.map((contact) => {
        const { name, email, address, phone } = contact;
        return [name, email, address, phone].join("");
      });
    },

    filteredContacts() {
      if (!this.searchText) return this.contacts;

      return this.contacts.filter((_contact, index) =>
        this.contactStrings[index].toLowerCase().includes(
          this.searchText.toLowerCase()
        )
      );
    },

    activeContact() {
      if (this.activeIndex < 0) return null;
      return this.filteredContacts[this.activeIndex] || null;
    },

    filteredContactsCount() {
      return this.filteredContacts.length;
    },
  },

  methods: {
    async retrieveContacts() {
      try {
        this.contacts = await ContactService.getAll();

        // Tự động chọn liên hệ đầu tiên
        this.activeIndex = this.filteredContacts.length > 0 ? 0 : -1;
      } catch (error) {
        console.log("Lỗi lấy danh sách liên hệ:", error);
      }
    },

    async refreshList() {
      await this.retrieveContacts();
    },

    async removeAllContacts() {
      if (confirm("Bạn muốn xóa tất cả Liên hệ?")) {
        try {
          await ContactService.deleteAll();
          this.contacts = [];
          this.activeIndex = -1;
        } catch (error) {
          console.log(error);
        }
      }
    },

    goToAddContact() {
      this.$router.push({ name: "contact.add" });
    },
  },

  mounted() {
    this.retrieveContacts();
  },
};
</script>

<style scoped>
.page {
  text-align: left;
  max-width: 750px;
}
</style>
