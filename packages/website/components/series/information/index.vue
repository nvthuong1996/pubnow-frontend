<template>
  <div>
    <div class="d-flex justify-content-between align-items-center">
      <h2 class="title">{{ title }}</h2>
      <va-dropdown effect="fadeUp" v-if="user && user.id === author.id ">
        <div slot="trigger">
          <i class="fas fa-ellipsis-h" />
        </div>
        <li>
          <nuxt-link :to="`${slug}/chinh-sua`">Chỉnh sửa</nuxt-link>
        </li>
        <li>
          <a @click="openModal">Xóa</a>
        </li>
      </va-dropdown>
    </div>
    <va-modal title="Xóa series" ref="deleteModal" :backdrop-clickable="true">
      <div slot="body">
        <p>Bạn có chắc chắn muốn xóa series này không?</p>
      </div>
      <div slot="footer">
        <va-button @click="$refs.deleteModal.close()">Hủy bỏ</va-button>
        <va-button type="danger" @click="deleteSeries" icon-before="trash">Xóa</va-button>
      </div>
    </va-modal>
    <div class="mt-2">
      <span>Đăng {{ date }}</span>
      <span class="middot"></span>
      <va-tooltip trigger="hover" content="Bài viết" placement="bottom" class="ml-2">
        <i class="fas fa-paste icon"></i>
      </va-tooltip>
      <span class="number">{{ articles.length }}</span>
    </div>
    <div class="mt-3 content-article fr-element fr-view" v-html="content"></div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  props: {
    slug: {
      type: String,
      required: true,
    },
    date: {
      type: String,
      required: true,
    },
    title: {
      type: String,
      required: true,
    },
    content: {
      type: String,
      required: true,
    },
    articles: {
      type: Array,
      required: true,
    },
    author: {
      type: Object,
      required: true,
    },
  },
  computed: {
    ...mapGetters({
      user: 'auth/user',
    }),
  },
  methods: {
    openModal() {
      this.$refs.deleteModal.open()
    },
    deleteSeries() {
      if (this.user) {
        this.$store.dispatch('series/delete', this.slug)
        this.notification.info({
          title: `Thông báo`,
          message: `Xóa series thành công`,
          duration: 1690,
          onHide: () => {
            this.$router.push(`/series`)
          },
        })
        this.$refs.deleteModal.close()
      } else {
        this.$router.push('/dang-nhap')
      }
    },
  },
}
</script>

<style lang="scss" scoped>
@import '@pubnow/ui/scss/_colors.scss';
@import '@pubnow/ui/scss/_fonts.scss';

.title {
  font-size: 30px;
  line-height: 1.1;
}

.icon {
  font-size: 12px;
  margin-right: 2px;
}
.number {
  margin-right: 8px;
}
</style>
