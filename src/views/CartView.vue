<template>
  <h1 class="text-center">購物車</h1>
  <div class="container">
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
    <table class="table align-middle">
      <thead>
        <tr>
          <th></th>
          <th>品名</th>
          <th style="width: 150px">數量/單位</th>
          <th class="text-center">單價</th>
        </tr>
      </thead>
      <tbody>
        <!-- 判斷購物車資料有沒有存在 -->
        <template v-if="cartData.carts.length">
          <tr v-for="item in cartData.carts" :key="item.id">
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
              {{ $filters.currency(item.product.price) }} 元 /
              <small class="text-success">折扣價：</small>
              {{}}
            </td>
          </tr>
        </template>
      </tbody>
      <tfoot>
        <tr>
          <td colspan="3" class="text-end">總計</td>
          <td class="text-center">{{ $filters.currency(cartData.total) }}</td>
        </tr>
        <tr>
          <td colspan="3" class="text-end text-success">折扣價</td>
          <td class="text-end text-success">{{}}</td>
        </tr>
      </tfoot>
    </table>
    <router-link to="/orderinform" class="btn btn-primary d-flex justify-content-center mt-4" :class="{'disabled':cartData.carts.length === 0 }">下一步</router-link>
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
      isLoadingItem: ''
    }
  },
  methods: {
    getCart () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.$http
        .get(url)
        .then((res) => {
          console.log(res)
          this.cartData = res.data.data
          console.log(this.cartData.carts.length)
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
      this.$http.put(url, { data })
        .then(res => {
          console.log(res)
          this.getCart()
          emitter.emit('get-cart')
          this.isLoadingItem = ''
        })
        .catch(err => {
          alert(err.data.message)
          this.isLoadingItem = ''
        })
    },
    removeAllCart () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/carts`
      this.$http.delete(url)
        .then(res => {
          alert(res.data.message)
          this.getCart()
          emitter.emit('get-cart')
          this.isLoadingItem = ''
        })
        .catch(err => {
          alert(err.data.message)
          this.isLoadingItem = ''
        })
    }
  },
  mounted () {
    this.getCart()
  }
}
</script>
