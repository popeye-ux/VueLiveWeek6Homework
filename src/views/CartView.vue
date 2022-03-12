<template>
  <!-- <Loading :active="isLoading" :z-index="1060"></Loading> -->
  <h1 class="text-center fw-bolder">購物車</h1>
  <div class="container  mb-7">
    <div class="text-end pt-5">
      <button
        class="btn btn-outline-danger"
        type="button"
        :disabled="cartData.carts.length === 0"
        @click="removeAllCart"
      >
        清空購物車
      </button>
    </div>
    <table class="table table-striped align-middle">
      <thead>
        <tr>

          <th style="width: 100px"></th>
          <th class="text-center">產品名稱</th>
          <th  class="text-center" style="width: 200px">數量/單位</th>
          <th class="text-center">單價</th>
          <th class="text-center">合計</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <!-- 判斷購物車資料有沒有存在 -->
        <template v-if="cartData.carts.length">
          <tr v-for="item in cartData.carts" :key="item.id">
            <td class="text-center px-0 table-width-s">
              <div
              style="
                height: 100px;
                background-size: cover;
                background-position: center;
              "
              :style="{ backgroundImage: `url(${item.product.imageUrl})` }"
            ></div>
            </td>
            <td>
              {{ item.product.title }}
              <div class="text-success">已套用優惠券</div>
            </td>
            <td>
              <div class="input-group input-group-sm">
                <div class="input-group mb-3">
                  <!-- <input
                          min="1"
                          type="number"
                          class="form-control"
                          v-model.number="item.qty"
                        /> -->
                  <select
                    name=""
                    id=""
                    class="form-select"
                    v-model="item.qty"
                    @change="updateCartItem(item)"
                    :disabled="isLoadingItem === item.id"
                  >
                    <option
                      :value="num"
                      v-for="num in 20"
                      :key="`${num}${item.id}`"
                    >
                      {{ num }}
                    </option>
                  </select>
                  <span class="input-group-text" id="basic-addon2">{{
                    item.product.unit
                  }}</span>
                </div>
              </div>
            </td>
            <td class="text-center">
              <del class="fs-5">{{
                $filters.currency(item.product.origin_price)
              }}</del>
              元 /
              <span class="text-danger">折扣價：</span>
              {{ $filters.currency(item.product.price) }}元
            </td>
            <td class="text-end">{{ $filters.currency(item.total) }} 元</td>
            <td>
              <button
                type="button"
                class="btn btn-outline-danger btn-sm"
                @click="removeCartItem(item.id)"
              >
                <!-- <i class="fas fa-spinner fa-pulse"></i> -->
                x
              </button>
            </td>
          </tr>
        </template>
      </tbody>
      <tfoot>
        <tr>
          <td colspan="4" class="text-end">原價總計</td>
          <td class="text-end">
            <del>{{ $filters.currency(originalPriceTotal) }}</del
            >元
          </td>
          <td></td>
        </tr>
        <tr>
          <td colspan="4" class="text-end text-danger fw-bolder"><h3>折扣價總計</h3></td>
          <td class="text-end text-danger fw-bolder">
           <h3>{{ $filters.currency(cartData.total) }}元</h3>
          </td>
          <td></td>
        </tr>
      </tfoot>
    </table>
    <div class="d-flex justify-content-between">
      <router-link
        to="/products"
        class="btn btn-outline-info btn-lg mt-3"
        >繼續選購</router-link
      >
      <router-link
        to="/orderinform"
        class="btn btn-danger btn-lg mt-3"
        :class="{ disabled: cartData.carts.length === 0 }"
        >下一步</router-link
      >
    </div>
  </div>
</template>
<script>
import emitter from '../libs/emitter'
export default {
  data () {
    return {
      cartData: {
        carts: []
      },
      isLoadingItem: '',
      isLoading: false,
      originPriceTotal: 0
    }
  },
  methods: {
    getCart () {
      this.isLoading = true
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.$http
        .get(url)
        .then((res) => {
          console.log(res)
          this.cartData = res.data.data
          // console.log(this.cartData.carts.length)
          const cartItems = this.cartData.carts
          const amount = cartItems.reduce(function (acc, item) {
            return (
              acc + parseInt(item.product.origin_price) * parseInt(item.qty)
            )
          }, 0)
          this.originalPriceTotal = amount
          console.log(amount)
          this.isLoading = false
        })
        .catch((err) => {
          alert(err.data.message)
        })
    },
    removeCartItem (id) {
      this.isLoadingItem = id
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${id}`
      this.isLoadingItem = ''
      this.$http
        .delete(url)
        .then((res) => {
          console.log(res)
          emitter.emit('get-cart')
          this.getCart()
        })
        .catch((err) => {
          alert(err.data.message)
        })
    },
    updateCartItem (item) {
      const data = {
        product_id: item.id,
        qty: item.qty
      }
      this.isLoadingItem = item.id
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${item.id}`
      this.$http
        .put(url, { data })
        .then((res) => {
          // console.log(res)
          this.getCart()
          emitter.emit('get-cart')
          this.isLoadingItem = ''
        })
        .catch((err) => {
          alert(err.data.message)
          this.isLoadingItem = ''
        })
    },
    removeAllCart () {
      this.isLoading = true
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/carts`
      this.$http
        .delete(url)
        .then((res) => {
          alert(res.data.message)
          this.getCart()
          emitter.emit('get-cart')
          this.isLoading = false
        })
        .catch((err) => {
          alert(err.data.message)
        })
    }
  },
  mounted () {
    this.getCart()
  }
}
</script>
