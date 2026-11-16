
<template>
    <Form @submit="submitContact" :validation-schema="contactFormSchema">
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

        <div class="form-group form-check">
            <input
                id="favorite"
                name="favorite"
                type="checkbox"
                class="form-check-input"
                v-model="contactLocal.favorite"
            />
            <label for="favorite" class="form-check-label">
                <strong>Liên hệ yêu thích</strong>
            </label>
        </div>

        <!-- Các nhóm sở thích -->
        <div v-if="contactLocal.favorite" class="form-group">
            <h5 class="mb-2">Sở thích</h5>

            <div
                v-for="group in interestGroups"
                :key="group.key"
                class="card mb-1"
            >
                <div class="card-header py-1 px-2">
                    <button
                        type="button"
                        class="btn btn-link btn-sm p-0 text-left"
                        @click="toggleGroup(group.key)"
                    >
                        <strong>{{ group.name }}</strong>
                        <span class="ml-2">
                            {{ expandedGroups.includes(group.key) ? "▲" : "▼" }}
                        </span>
                    </button>
                </div>

                <div
                    v-if="expandedGroups.includes(group.key)"
                    class="card-body py-2 px-3"
                >
                    <div
                        v-for="item in group.items"
                        :key="item"
                        class="form-check mb-1"
                    >
                        <input
                            :id="group.key + '-' + item"
                            type="checkbox"
                            class="form-check-input"
                            :value="item"
                            v-model="contactLocal.interests[group.key]"
                        />
                        <label
                            :for="group.key + '-' + item"
                            class="form-check-label"
                        >
                            {{ item }}
                        </label>
                    </div>
                </div>
            </div>
        </div>

        <div class="form-group">
            <button class="btn btn-primary">Lưu</button>

            <button
                v-if="contactLocal._id"
                type="button"
                class="ml-2 btn btn-danger"
                @click="deleteContact"
            >
                Xóa
            </button>

            <button
                type="button"
                class="ml-2 btn btn-danger"
                @click="Cancel"
            >
                Thoát
            </button>
        </div>
    </Form>
</template>

<script>
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";

export default {
    components: {
        Form,
        Field,
        ErrorMessage,
    },

    emits: ["submit:contact", "delete:contact"],

    props: {
        contact: { type: Object, required: true },
    },

    data() {
        const contactFormSchema = yup.object().shape({
            name: yup
                .string()
                .required("Tên phải có giá trị.")
                .min(2, "Tên phải ít nhất 2 ký tự.")
                .max(50, "Tên có nhiều nhất 50 ký tự."),

            email: yup
                .string()
                .email("E-mail không đúng.")
                .max(50, "E-mail tối đa 50 ký tự."),

            address: yup.string().max(100, "Địa chỉ tối đa 100 ký tự."),

            phone: yup
                .string()
                .matches(
                    /((09|03|07|08|05)+([0-9]{8})\b)/g,
                    "Số điện thoại không hợp lệ."
                ),
        });

        return {
            contactLocal: {
                ...this.contact,
                interests: {
                    sports: [],
                    music: [],
                    news: [],
                    travel: [],
                    ...(this.contact.interests || {}),
                },
            },

            expandedGroups: [],

            interestGroups: [
                {
                    key: "sports",
                    name: "Thể thao",
                    items: ["Bóng đá", "Cầu lông", "Gym", "Bóng chuyền"],
                },
                {
                    key: "music",
                    name: "Âm nhạc",
                    items: ["Jazz", "Rock", "Đồng quê", "Pop"],
                },
                {
                    key: "news",
                    name: "Tin tức",
                    items: ["Công nghệ", "Thể thao", "Kinh tế", "Giải trí"],
                },
                {
                    key: "travel",
                    name: "Du lịch",
                    items: ["Biển", "Núi", "Khám phá", "Nghỉ dưỡng"],
                },
            ],

            contactFormSchema,
        };
    },

    methods: {
        toggleGroup(key) {
            if (this.expandedGroups.includes(key)) {
                this.expandedGroups = this.expandedGroups.filter(
                    (item) => item !== key
                );
            } else {
                this.expandedGroups.push(key);
            }
        },

        submitContact() {
            this.$emit("submit:contact", this.contactLocal);
        },

        deleteContact() {
            this.$emit("delete:contact", this.contactLocal.id);
        },

        Cancel() {
            const reply = window.confirm(
                "You have unsaved changes! Do you want to leave?"
            );

            if (!reply) {
                return false;
            } else {
                this.$router.push({ name: "contactbook" });
            }
        },
    },
};
</script>

<style scoped>
@import "@/assets/form.css";
</style>
