<template>
  <div>
    <div class="mb-3">
      <h3>Tìm kiếm bài viết</h3>
      <div class="d-flex">
        <va-input
          autofocus
          :clearable="clearable"
          @confirm="search"
          v-model="searchText"
          class="mr-2"
        />
        <va-button @click="addArticleToSeries">Thêm vào</va-button>
      </div>
    </div>
    <div>
      <h3>Bài viết của bạn</h3>
      <template v-if="!$wait.is('search.articles')">
        <template v-if="userArticles">
          <va-checkbox-group v-model="post" class="d-flex flex-column">
            <div
              class="wrap-article py-1 px-1"
              v-for="(article, index) in userArticles"
              :key="`article-${index}`"
            >
              <va-checkbox-btn :label="article.id">
                <div class="title-item w-100">{{ article.title }}</div>
              </va-checkbox-btn>
            </div>
          </va-checkbox-group>
        </template>
        <span v-else>Chưa có bài viết nào ở đây</span>
      </template>
      <p v-else>Đang tải...</p>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  data() {
    return {
      userArticles: null,
      originArticles: [],
      clearable: false,
      searchText: '',
      post: [],
      dataID: [],
    }
  },
  computed: {
    ...mapGetters({
      user: 'auth/user',
      series: 'series/series',
      articles: 'search/articles',
    }),
  },
  mounted() {
    if (this.series) {
      this.series.articles.forEach(item => this.dataID.push(item.id))
    }
  },
  methods: {
    removeSign(str) {
      str = str.replace(/à|á|ạ|ả|ã|â|ầ|ấ|ậ|ẩ|ẫ|ă|ằ|ắ|ặ|ẳ|ẵ/g, 'a')
      str = str.replace(/è|é|ẹ|ẻ|ẽ|ê|ề|ế|ệ|ể|ễ/g, 'e')
      str = str.replace(/ì|í|ị|ỉ|ĩ/g, 'i')
      str = str.replace(/ò|ó|ọ|ỏ|õ|ô|ồ|ố|ộ|ổ|ỗ|ơ|ờ|ớ|ợ|ở|ỡ/g, 'o')
      str = str.replace(/ù|ú|ụ|ủ|ũ|ư|ừ|ứ|ự|ử|ữ/g, 'u')
      str = str.replace(/ỳ|ý|ỵ|ỷ|ỹ/g, 'y')
      str = str.replace(/đ/g, 'd')
      str = str.replace(/À|Á|Ạ|Ả|Ã|Â|Ầ|Ấ|Ậ|Ẩ|Ẫ|Ă|Ằ|Ắ|Ặ|Ẳ|Ẵ/g, 'A')
      str = str.replace(/È|É|Ẹ|Ẻ|Ẽ|Ê|Ề|Ế|Ệ|Ể|Ễ/g, 'E')
      str = str.replace(/Ì|Í|Ị|Ỉ|Ĩ/g, 'I')
      str = str.replace(/Ò|Ó|Ọ|Ỏ|Õ|Ô|Ồ|Ố|Ộ|Ổ|Ỗ|Ơ|Ờ|Ớ|Ợ|Ở|Ỡ/g, 'O')
      str = str.replace(/Ù|Ú|Ụ|Ủ|Ũ|Ư|Ừ|Ứ|Ự|Ử|Ữ/g, 'U')
      str = str.replace(/Ỳ|Ý|Ỵ|Ỷ|Ỹ/g, 'Y')
      str = str.replace(/Đ/g, 'D')
      return str.toLowerCase()
    },
    select(article, index) {
      this.$emit('addArticle', article)
    },
    search() {
      if (this.searchText !== '') {
        this.$store
          .dispatch('search/article', { keyword: this.searchText })
          .then(() => {
            this.userArticles = this.articles
          })
      } else {
        this.userArticles = null
      }
    },
    addArticleToSeries() {
      if (this.post.length !== 0) {
        this.dataID = [...new Set([...this.dataID, ...this.post])]
        let data = {
          articles: this.dataID,
          slug: this.series.slug,
        }
        this.$store.dispatch('series/edit', data)
        this.$emit('closeModel')
      }
    },
  },
}
</script>

<style lang="scss" scoped>
@import '@pubnow/ui/scss/_sizes.scss';
@import '@pubnow/ui/scss/_colors.scss';
@import '@pubnow/ui/scss/_mixins.scss';

.wrap-article {
  cursor: pointer;
  .va-btn {
    width: 100%;
    .title-item {
      text-align: left;
      white-space: normal;
      line-height: 1.5rem;
    }
  }
  .title {
    font-size: $unit / 1.3;
  }
}
.selected {
  background: $gray90;
}
</style>
