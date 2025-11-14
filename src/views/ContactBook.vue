<!-- src/views/ContactBook.vue -->
<template>
  <div class="container page">
    <!-- Tìm kiếm -->
    <div class="search-bar">
      <InputSearch v-model="searchText" />
    </div>

    <!-- Danh bạ -->
    <div class="mt-3">
      <h4 class="d-flex align-items-center">
        Danh bạ <i class="fas fa-address-book ml-2"></i>
      </h4>

      <ContactList
        v-if="filteredContactsCount > 0"
        :contacts="filteredContacts"
        v-model:activeIndex="activeIndex"
      />
      <p v-else>Không có liên hệ nào.</p>

      <!-- Nhóm nút hành động -->
      <div
        class="mt-3 d-flex justify-content-around flex-nowrap action-buttons"
      >
        <button class="btn btn-sm btn-primary" @click="refreshList">
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
    <div class="mt-4" v-if="activeContact">
      <h4 class="d-flex align-items-center">
        Chi tiết Liên hệ <i class="fas fa-address-card ml-2"></i>
      </h4>
      <ContactCard :contact="activeContact" />
      <router-link
        :to="{ name: 'contact.edit', params: { id: activeContact._id } }"
      >
        <span class="mt-2 badge badge-warning d-inline-flex align-items-center">
          <i class="fas fa-edit mr-1"></i> Hiệu chỉnh
        </span>
      </router-link>
    </div>
  </div>
</template>

<style scoped>
.search-bar {
  max-width: 100%;
}

.action-buttons button {
  white-space: nowrap; /* giữ nút không xuống dòng */
  flex: 1 1 auto;
  margin: 0 5px;
}
</style>

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
  // Đoạn mã xử lý đầy đủ sẽ trình bày bên dưới
  data() {
    return {
      contacts: [],
      activeIndex: -1,
      searchText: "",
    };
  },
  watch: {
    // Giám sát các thay đổi của biến searchText.
    // Bỏ chọn phần tử đang được chọn trong danh sách.
    searchText() {
      this.activeIndex = -1;
    },
  },
  computed: {
    // Chuyển các đối tượng contact thành chuỗi để tiện cho tìm kiếm.
    contactStrings() {
      return this.contacts.map((contact) => {
        const { name, email, address, phone } = contact;
        return [name, email, address, phone].join("");
      });
    },
    // Trả về các contact có chứa thông tin cần tìm kiếm.
    filteredContacts() {
      if (!this.searchText) return this.contacts;
      return this.contacts.filter((_contact, index) =>
        this.contactStrings[index].includes(this.searchText)
      );
    },
    activeContact() {
      if (this.activeIndex < 0) return null;
      return this.filteredContacts[this.activeIndex];
    },
    filteredContactsCount() {
      return this.filteredContacts.length;
    },
  },
  methods: {
    async retrieveContacts() {
      try {
        this.contacts = await ContactService.getAll();
      } catch (error) {
        console.log(error);
      }
    },
    refreshList() {
      this.retrieveContacts();
      this.activeIndex = -1;
    },
    async removeAllContacts() {
      if (confirm("Bạn muốn xóa tất cả Liên hệ?")) {
        try {
          await ContactService.deleteAll();
          this.refreshList();
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
    this.refreshList();
  },
};
</script>
<style scoped>

.page {
text-align: left;
max-width: 750px;
}
</style>
