
<script>
export default {
  props: {
    contact: {
      type: Object,
      required: true,
    },
  },

  computed: {
    interestGroups() {
      const interests = this.contact.interests || {};

      return [
        {
          key: "sports",
          name: "Thể thao",
          items: interests.sports || [],
        },
        {
          key: "music",
          name: "Âm nhạc",
          items: interests.music || [],
        },
        {
          key: "news",
          name: "Tin tức",
          items: interests.news || [],
        },
        {
          key: "travel",
          name: "Du lịch",
          items: interests.travel || [],
        },
      ];
    },
  },
};
</script>

<template>
  <div>
    <div class="p-1">
      <strong>Tên:</strong>
      {{ contact.name }}
    </div>

    <div class="p-1">
      <strong>E-mail:</strong>
      {{ contact.email }}
    </div>

    <div class="p-1">
      <strong>Địa chỉ:</strong>
      {{ contact.address }}
    </div>

    <div class="p-1">
      <strong>Điện thoại:</strong>
      {{ contact.phone }}
    </div>

    <div class="p-1">
      <strong>Liên hệ yêu thích:&nbsp;</strong>
      <i v-if="contact.favorite" class="fas fa-check"></i>
      <i v-else class="fas fa-times"></i>
    </div>

    <!-- Hiển thị sở thích -->
    <div v-if="contact.favorite" class="mt-2">
      <strong>Sở thích:</strong>

      <div
        v-for="group in interestGroups"
        :key="group.key"
        v-show="group.items.length > 0"
        class="ml-2 mt-1"
      >
        <strong>{{ group.name }}:</strong>
        {{ group.items.join(", ") }}
      </div>

      <p
        v-if="!interestGroups.some(group => group.items.length > 0)"
        class="ml-2 mt-1 text-muted"
      >
        Chưa có sở thích nào được chọn.
      </p>
    </div>
  </div>
</template>
