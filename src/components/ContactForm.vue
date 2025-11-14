<template>
  <!-- Form với v-slot handleSubmit để validation hoạt động -->
  <Form :validation-schema="contactFormSchema" v-slot="{ handleSubmit }">
    <form @submit.prevent="handleSubmit(submitContact)">
      <!-- Tên -->
      <div class="form-group">
        <label for="name">Tên</label>
        <Field
          name="name"
          type="text"
          class="form-control"
          v-model="contactLocal.name"
        />
        <ErrorMessage name="name" class="error-feedback" />
      </div>

      <!-- Email -->
      <div class="form-group">
        <label for="email">E-mail</label>
        <Field
          name="email"
          type="email"
          class="form-control"
          v-model="contactLocal.email"
        />
        <ErrorMessage name="email" class="error-feedback" />
      </div>

      <!-- Địa chỉ -->
      <div class="form-group">
        <label for="address">Địa chỉ</label>
        <Field
          name="address"
          type="text"
          class="form-control"
          v-model="contactLocal.address"
        />
        <ErrorMessage name="address" class="error-feedback" />
      </div>

      <!-- Điện thoại -->
      <div class="form-group">
        <label for="phone">Điện thoại</label>
        <Field
          name="phone"
          type="tel"
          class="form-control"
          v-model="contactLocal.phone"
        />
        <ErrorMessage name="phone" class="error-feedback" />
      </div>

      <!-- Sở thích -->
      <div class="form-group">
        <label><strong>Sở thích:</strong></label>
        <div class="hobbies-container">
          <label>
            <Field
              name="hobbies"
              type="checkbox"
              value="Nghe nhạc"
              v-model="contactLocal.hobbies"
            />
            Nghe nhạc
          </label>
          <label>
            <Field
              name="hobbies"
              type="checkbox"
              value="Xem phim"
              v-model="contactLocal.hobbies"
            />
            Xem phim
          </label>
          <label>
            <Field
              name="hobbies"
              type="checkbox"
              value="Đọc sách"
              v-model="contactLocal.hobbies"
            />
            Đọc sách
          </label>
        </div>
        <ErrorMessage name="hobbies" class="error-feedback" />
      </div>

      <!-- Tình trạng hôn nhân -->
      <div class="form-group">
        <label><strong>Tình trạng hôn nhân:</strong></label>
        <div class="marital-container">
          <label>
            <Field
              name="maritalStatus"
              type="radio"
              value="Độc thân"
              v-model="contactLocal.maritalStatus"
            />
            Độc thân
          </label>
          <label>
            <Field
              name="maritalStatus"
              type="radio"
              value="Đã lập gia đình"
              v-model="contactLocal.maritalStatus"
            />
            Đã lập gia đình
          </label>
          <label>
            <Field
              name="maritalStatus"
              type="radio"
              value="Khác"
              v-model="contactLocal.maritalStatus"
            />
            Khác
          </label>
        </div>
        <ErrorMessage name="maritalStatus" class="error-feedback" />
      </div>

      <!-- Liên hệ yêu thích -->
      <div class="form-group form-check">
        <input
          name="favorite"
          type="checkbox"
          class="form-check-input"
          v-model="contactLocal.favorite"
        />
        <label for="favorite" class="form-check-label">
          <strong>Liên hệ yêu thích</strong>
        </label>
      </div>

      <!-- Nút hành động -->
      <div class="form-group">
        <!-- Nút Lưu (gửi form bình thường) -->
        <button type="submit" class="btn btn-primary">Lưu</button>

        <!-- Nút Hiệu chỉnh (cũng chạy handleSubmit để validation) -->
        <button
          v-if="contactLocal._id"
          type="button"
          class="ml-2 btn btn-success"
          @click="handleSubmit(submitContact)"
        >
          Hiệu chỉnh
        </button>

        <!-- Nút Xóa -->
        <button
          v-if="contactLocal._id"
          type="button"
          class="ml-2 btn btn-danger"
          @click="deleteContact"
        >
          Xóa
        </button>

        <!-- Nút Thoát -->
        <button type="button" class="ml-2 btn btn-secondary" @click="Cancel">
          Thoát
        </button>
      </div>
    </form>
  </Form>
</template>

<script>
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";

export default {
  components: { Form, Field, ErrorMessage },
  emits: ["submit:contact", "delete:contact"],
  props: { contact: { type: Object, required: true } },
  data() {
    const contactFormSchema = yup.object().shape({
      name: yup.string().required("Tên phải có giá trị.").min(2).max(50),
      email: yup.string().email("E-mail không đúng.").max(50),
      address: yup.string().max(100),
      phone: yup
        .string()
        .matches(
          /((09|03|07|08|05)+([0-9]{8})\b)/g,
          "Số điện thoại không hợp lệ."
        ),
      hobbies: yup.array().min(1, "Chọn ít nhất một sở thích."),
      maritalStatus: yup
        .string()
        .required("Vui lòng chọn tình trạng hôn nhân."),
    });

    return {
      contactLocal: {
        ...this.contact,
        hobbies: Array.isArray(this.contact.hobbies)
          ? this.contact.hobbies
          : [],
        maritalStatus: this.contact.maritalStatus || "",
      },
      contactFormSchema,
    };
  },
  methods: {
    submitContact() {
      this.$emit("submit:contact", this.contactLocal);
      this.$router.push({ name: "contactbook" });
    },
    deleteContact() {
      this.$emit("delete:contact", this.contactLocal.id);
    },
    Cancel() {
      const reply = window.confirm(
        "You have unsaved changes! Do you want to leave?"
      );
      if (!reply) return false;
      this.$router.push({ name: "contactbook" });
    },
  },
};
</script>

<style scoped>
@import "@/assets/form.css";

.error-feedback {
  color: red;
  font-size: 0.9em;
  margin-top: 4px;
}

/* checkbox và radio xếp ngang, responsive */
.hobbies-container,
.marital-container {
  display: flex;
  gap: 20px;
  align-items: center;
  flex-wrap: wrap;
}
</style>
