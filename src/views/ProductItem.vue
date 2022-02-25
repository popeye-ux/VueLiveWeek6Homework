<template>
  <h1 class="text-center">產品內容</h1>
  <div class="container">
    <div class="row">
          <div class="col-sm-6">
            <img class="img-fluid" :src="productDetail.imageUrl" alt="" />
          </div>
          <div class="col-sm-6">
            <span class="badge bg-primary rounded-pill">{{ }}</span>
            <p>{{productDetail.title }}</p>
            <p>{{productDetail.description }}</p>
            <div class="h5" v-if="productDetail.price===productDetail.origin_price">{{ productDetail.price }} 元</div>
            <div v-else>
            <del class="h6">原價 {{ productDetail.origin_price}} 元</del>
            <div class="h5">現在只要 {{productDetail.price }} 元</div>
            </div>
            <div>
              <div class="input-group">
                <input type="number" class="form-control" min="1" v-model.number="qty"/>
                <button type="button" class="btn btn-danger" @click="addToCart(productDetail.id)" :disabled="isLoadingItem===productDetail.id">
                  <span
                        class="spinner-border spinner-border-sm"
                        v-show="isLoadingItem===productDetail.id"
                      ></span>加入購物車
                </button>
              </div>
            </div>
            <div>
                <router-link to="/cart" class="btn btn-primary d-flex justify-content-center mt-4" >檢視購物車</router-link>
              </div>
          </div>
          <!-- col-sm-6 end -->
        </div>
  </div>
</template>
<script>
import emitter from '../libs/emitter'
export default {
  data () {
    return {
      productDetail: {},
      qty: 1,
      isLoadingItem: ''
    }
  },
  methods: {
    getProduct () {
      const { id } = this.$route.params
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/product/${id}`
      this.$http.get(url).then((response) => {
        this.productDetail = response.data.product
      })
    },
    addToCart (id, qty = 1) {
      const data = {
        product_id: id,
        qty
      }
      this.isLoadingItem = id
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.$http.post(url, { data })
        .then(res => {
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
        .catch(err => {
          console.log(err.data.message)
          this.isLoadingItem = ''
        })
    }
  },
  mounted () {
    this.getProduct()
  }
}
</script>
