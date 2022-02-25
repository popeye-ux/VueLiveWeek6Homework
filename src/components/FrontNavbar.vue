<template>
    <nav class="navbar navbar-expand-lg navbar-light bg-light" id="nav">
      <div class="container">
        <router-link to="/" class="navbar-brand"
          ><svg
            version="1.1"
            id="Layer_1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            x="0px"
            y="0px"
            viewBox="0 0 1920 1080"
            style="enable-background: new 0 0 1920 1080"
            xml:space="preserve"
          >
            <g>
              <path
                class="st0"
                d="M561.83,453.43V625.2h-32.9v-86.94l-53.81,64.62l-53.58-64.62v86.94h-32.9V453.43h2.11l84.36,99.16
           l84.59-99.16H561.83z"
              />
              <path
                class="st0"
                d="M995.14,453.43V625.2h-32.9v-86.94l-53.81,64.62l-53.58-64.62v86.94h-32.9V453.43h2.11l84.36,99.16
           l84.59-99.16H995.14z"
              />
              <path
                class="st0"
                d="M1077.62,482.33v43h83.18v29.84h-83.18v40.42h93.76v29.61h-126.66V453.19h126.66v29.14H1077.62z"
              />
              <path
                class="st0"
                d="M1362.9,625.2l-113.04-102.22V625.2h-32.9V453.43h3.52l113.03,103.86V453.43h32.9V625.2H1362.9z"
              />
              <path
                class="st0"
                d="M1532.08,482.33h-49.11V625.2h-32.9V482.33h-49.35v-29.14h131.36V482.33z"
              />
            </g>
            <g>
              <path
                class="st1"
                d="M692.09,450.79c-49.15,0-89,39.85-89,89c0,49.15,39.85,89,89,89s89-39.85,89-89
           C781.09,490.63,741.25,450.79,692.09,450.79z M692.09,602.79c-34.79,0-63-28.21-63-63s28.21-63,63-63s63,28.21,63,63
           S726.89,602.79,692.09,602.79z"
              />
            </g>
            <path
              class="st1"
              d="M686.94,539.59l-8.14,6.97c-0.79,0.68-0.88,1.86-0.21,2.65l2.11,2.46c0.68,0.79,1.86,0.88,2.65,0.21l8.55-7.32
         l7.4,7.4c0.73,0.73,1.9,0.73,2.63,0l2.32-2.32c0.73-0.73,0.73-1.9,0-2.63l-7.01-7.01l37.48-32.08c0.79-0.68,0.88-1.86,0.21-2.65
         l-2.11-2.46c-0.68-0.79-1.86-0.88-2.65-0.21l-37.89,32.44l-26.9-26.9c-0.73-0.73-1.9-0.73-2.63,0l-2.32,2.32
         c-0.73,0.73-0.73,1.9,0,2.63L686.94,539.59z"
            /></svg
        ></router-link>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarNav"
          aria-controls="navbarNav"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div
          class="collapse navbar-collapse justify-content-end"
          id="navbarNav"
        >
          <ul class="navbar-nav">
            <li class="nav-item me-5">
              <router-link to="/" class="nav-link">Home</router-link>
            </li>
            <li class="nav-item me-5">
              <router-link to="/about" class="nav-link">About</router-link>
            </li>
            <li class="nav-item me-5">
              <router-link to="/products" class="nav-link">產品列表</router-link>
            </li>
            <li class="nav-item me-5">
              <router-link to="/cart" class="btn btn-primary">購物車<span class="badge rounded-pill bg-danger">{{cartData.carts.length}}</span></router-link>
            </li>
            <li class="nav-item me-5">
              <router-link to="/admin" class="nav-link">前往管理後台</router-link>
            </li>
          </ul>
        </div>
      </div>
    </nav>
</template>
<style lang="scss" scoped>
  body {
    background: #090043;
    color: #fff;
  }
  svg{
    width: 300px;
  }
  .st0 {
    fill: #ffffff;
  }
  .st1 {
    fill: #ff0000;
  }
</style>
<script>
import emitter from '@/libs/emitter'
export default {
  data () {
    return {
      cartData: {
        carts: []
      }
    }
  },
  methods: {
    getCart () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`
      this.$http
        .get(url)
        .then((res) => {
          // console.log(res)
          // 取得購物車
          this.cartData = res.data.data
        })
        .catch((err) => {
          alert(err.data.message)
        })
    }
  },
  mounted () {
    this.getCart()
    emitter.on('get-cart', () => {
      this.getCart()
    })
  }
}
</script>
