<template>
  <h1 class="text-center">產品列表</h1>
  <div class="container">
    <!-- row 決定內層的欄位數量 -->
    <div class="row row-cols-1 row-cols-sm-2 row-cols-lg-4 g-5">
      <!-- 內層不決定寬度 -->
      <div class="col" v-for="item in products" :key="item.id">
        <div class="card h-100">
          <div class="card-body">
            <img
              :src="item.imageUrl"
              class="card-img-top img-fluid"
              alt="..."
            />
            <div class="text-center mt-2">
              <h5 class="card-title">{{ item.title }}</h5>
              <h6 class="card-subtitle">{{ item.description }}</h6>
              <p class="card-text">{{ item.price }}</p>
              <del class="card-text">{{ item.origin_price }}</del>
            </div>
          </div>
          <div class="card-footer">
            <router-link :to="`/product/${item.id}`" class="btn btn-primary">
              查看更多
            </router-link>
            <button
              type="button"
              class="btn btn-danger"
              @click="addToCart(item.id)"
              :disabled="isLoadingItem === item.id"
            >
              <span
                class="spinner-border spinner-border-sm"
                v-show="isLoadingItem === item.id"
              ></span
              >加入購物車
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="container mt-3">
    <div class="row">
      <!-- props 內層:pages 外層:pagination -->
      <pagination :pages="pagination" @get-products="getData"></pagination>
    </div>
  </div>
</template>
<script>
import emitter from '../libs/emitter'
import pagination from '@/components/PaginationComp.vue'
export default {
  data () {
    return {
      products: [],
      isLoadingItem: '',
      pagination
    }
  },
  components: {
    pagination
  },
  methods: {
    getData (page = 1) {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products?page=${page}`
      this.$http.get(url).then((response) => {
        const { products, pagination } = response.data
        this.products = products
        this.pagination = pagination
      })
    },
    addToCart (id, qty = 1) {
      const data = {
        product_id: id,
        qty
      }
      this.isLoadingItem = id
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.$http
        .post(url, { data })
        .then((res) => {
          console.log(res)
          if (data.qty <= 0) {
            alert('數量必須大於0')
            // this.isLoadingItem = '';
            return
          }
          this.isLoadingItem = ''
          alert(res.data.message)
          emitter.emit('get-cart')
        })
        .catch((err) => {
          console.log(err.data.message)
          this.isLoadingItem = ''
        })
    }
  },
  mounted () {
    // const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products`
    // this.$http.get(url).then((response) => {
    //   this.products = response.data.products
    // })
    this.getData()
  }
}
</script>
