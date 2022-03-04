const newLocal=getPosts()
<template>
 <Loading :active="isLoading" :z-index="1060"></Loading>
  <h1 class="text-center">這是文章管理介面</h1>
  <div class="container">
       <div class="text-end mt-4">
      <button class="btn btn-primary" type="button" @click="openArticleModal(true)">
        建立新的頁面
      </button>
    </div>
    <table class="table mt-4">
      <thead>
        <tr>
          <th style="width: 200px">標題</th>
          <th style="width: 200px">作者</th>
          <th>描述</th>
          <th style="width: 100px">建立時間</th>
          <th style="width: 150px">是否公開</th>
          <th style="width: 120px">編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in posts" :key="item.id">
          <td>{{item.title}}</td>
          <td>{{item.author}}</td>
          <td>{{item.description}}</td>
          <td>{{getDate(item.create_at)}}</td>
          <td>
            <div class="form-check form-switch">
              <input
                class="form-check-input"
                type="checkbox"
                :id="`${item.id}`"
                v-model="item.isPublic"
                @change="updatePost(item)"
              />
              <label class="form-check-label" :for="`${item.id}`">
                <span v-if="item.isPublic===true">上架</span>
                <span v-else>未上架</span>
              </label>
            </div>
          </td>
          <td>
            <div class="btn-group">
              <button class="btn btn-outline-primary btn-sm" type="button" @click="openArticleModal(false, item)">
                編輯
              </button>
              <button class="btn btn-outline-danger btn-sm" type="button">
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <post-modal
      :article="tempArticle"
      :is-new="isNew"
      ref="postModal"
      @update-article="getPosts"
    ></post-modal>
  </div>
</template>
<script>
import PostModal from '@/components/PostModal.vue'
export default {
  data () {
    return {
      nowPage: 1,
      posts: [],
      pagination: {},
      isLoading: false,
      tempArticle: {
        tag: ['']
      },
      getArticle: {},
      isNew: true
    }
  },
  components: {
    PostModal
  },
  methods: {
    getDate (timestamp) {
      const date = new Date(timestamp * 1000)
      return date.toLocaleDateString()
    },
    getPosts (page = 1) {
      this.nowPage = page
      const url = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/admin/articles?page=${page}`
      this.$http.get(url)
        .then((res) => {
          console.log(res)
          this.posts = res.data.articles
        })
        .catch((err) => {
          console.log(err)
        })
    },
    updatePost (item) {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/article/${item.id}`
      this.$http.get(url)
        .then((res) => {
          console.log(res)
          this.getArticle = res.data.article
          console.log(this.getArticle.content)
          const content = this.getArticle.content
          this.tempArticle = item
          this.tempArticle.content = content
          console.log(this.tempArticle.content)
          console.log(this.tempArticle)
          this.$http.put(url, { data: this.tempArticle })
            .then((res) => {
              console.log(res)
              alert(res.data.message)
            })
            .catch((err) => {
              console.log(err)
            })
        })
        .catch((err) => {
          console.log(err)
        })
    },
    openArticleModal (isNew, item) {
      this.isNew = isNew
      if (this.isNew) {
        item = ''
        this.tempArticle = { ...item }
        this.tempArticle = {
          create_at: new Date().getTime() / 1000
        }
      } else {
        const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/article/${item.id}`
        this.$http.get(url)
          .then((res) => {
            this.tempArticle = res.data.article
            console.log(this.tempArticle)
          })
          .catch((err) => {
            console.log(err)
          })
      }
      this.$refs.postModal.openModal()
    }
  },
  mounted () {
    this.getPosts()
  }
}
</script>
