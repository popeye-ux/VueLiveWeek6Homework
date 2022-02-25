<template>
  <h1 class="text-center">填寫客戶資訊與確認訂單</h1>
  <div class="container">
    <div class="row">
      <div class="col justify-content-center">
        <Form
          ref="form"
          @submit="submitOrders"
          v-slot="{ errors }"
        >
          <div class="mb-3">
            <label for="email" class="form-label">Email</label>
            <Field
              id="email"
              name="email"
              type="email"
              class="form-control"
              rules="email|required"
              :class="{ 'is-invalid': errors['email'] }"
              placeholder="請輸入 Email"
              v-model="form.user.email"
            ></Field>
            <error-message
              name="email"
              class="invalid-feedback"
            ></error-message>
          </div>

          <div class="mb-3">
            <label for="name" class="form-label">收件人姓名</label>
            <Field
              id="name"
              name="姓名"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': errors['姓名'] }"
              placeholder="請輸入姓名"
              rules="required"
              v-model="form.user.name"
            ></Field>
            <error-message name="姓名" class="invalid-feedback"></error-message>
          </div>

          <div class="mb-3">
            <label for="tel" class="form-label">收件人電話</label>
            <Field
              id="tel"
              name="電話"
              type="text"
              class="form-control"
              :rules="isPhone"
              :class="{ 'is-invalid': errors['電話'] }"
              placeholder="請輸入電話"
              v-model="form.user.tel"
            ></Field>
            <error-message name="電話" class="invalid-feedback"></error-message>
          </div>

          <div class="mb-3">
            <label for="address" class="form-label">收件人地址</label>
            <Field
              id="address"
              name="地址"
              type="text"
              class="form-control"
              :class="{ 'is-invalid': errors['地址'] }"
              placeholder="請輸入地址"
              rules="required"
              v-model="form.user.address"
            ></Field>
            <error-message name="地址" class="invalid-feedback"></error-message>
          </div>
          <div class="mb-3">
            <label for="message" class="form-label">留言</label>
            <textarea
              id="message"
              class="form-control"
              cols="30"
              rows="10"
              v-model="form.user.message"
            ></textarea>
          </div>
          <div class="text-end">
            <button type="submit" class="btn btn-danger">送出訂單</button>
          </div>
        </Form>
      </div>
      <div class="col">
        <table class="table align-middle">
          <thead>
            <tr>
              <th>品名</th>
              <th style="width: 150px">數量/單位</th>
              <th class="text-center">單價</th>
            </tr>
          </thead>
          <tbody>
            <!-- 判斷購物車資料有沒有存在 -->
            <template v-if="cartData.carts">
              <tr v-for="item in cartData.carts" :key="item.id">
                <td>
                  {{ item.product.title }}
                </td>
                <td>
                  {{ item.qty }}
                </td>
                <td class="text-center">
                  {{ item.product.price }} 元 /
                  <small class="text-success">折扣價：</small>
                  {{}}
                </td>
              </tr>
            </template>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="3" class="text-end">總計</td>
              <td class="text-center">{{ cartData.total }}</td>
            </tr>
            <tr>
              <td colspan="3" class="text-end text-success">折扣價</td>
              <td class="text-end text-success">{{}}</td>
            </tr>
          </tfoot>
        </table>
      </div>
    </div>
  </div>
</template>
<script>
import emitter from '../libs/emitter'
export default {
  data () {
    return {
      cartData: {},
      isLoadingItem: '',
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: ''
        },
        message: ''
      }
    }
  },
  methods: {
    // validate
    isPhone (value) {
      const phoneNumber = /^(09)[0-9]{8}$/
      return phoneNumber.test(value) ? true : '需要正確的電話號碼'
    },
    getCart () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.$http
        .get(url)
        .then((res) => {
          console.log(res)
          this.cartData = res.data.data
        })
        .catch((err) => {
          alert(err.data.message)
        })
    },
    submitOrders () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/order`
      const order = this.form
      this.$http.post(url, { data: order })
        .then(res => {
          alert(res.data.message)
          this.isLoadingItem = ''
          this.getCart()
          this.$refs.form.resetForm()
          emitter.emit('get-cart')
        })
        .catch(err => {
          alert(err.data.message)
        })
    }
  },
  mounted () {
    this.getCart()
  }
}
</script>
