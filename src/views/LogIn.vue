<template>
    <div class="container">
        <div class="row justify-content-center">
          <h1 class="mb-4 h4 fw-bolder">請先登入產品後台</h1>
          <div class="col-10">
            <form class="form-login" @submit.prevent="login">
              <div class="form-floating mb-4">
                <input
                  type="email"
                  class="form-control"
                  id="usernameInput"
                  placeholder="name@example.com"
                  v-model="user.username"
                  required
                  autofocus
                />
                <label for="usernameInput">Email address</label>
              </div>
              <div class="form-floating mb-4">
                <input
                  type="password"
                  class="form-control"
                  id="passwordInput"
                  placeholder="password"
                  v-model="user.password"
                  required
                />
                <label for="passwordInput">Password</label>
              </div>
              <button
                class="btn btn-lg btn-primary w-100 mt-4"
                type="button"
                @click.prevent="login()"
              >
                登入
              </button>
            </form>
          </div>
        </div>
      </div>
</template>
<script>
export default {
  data () {
    return {
      user: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    login () {
      const api = `${process.env.VUE_APP_API}admin/signin`
      this.$http.post(api, this.user).then((res) => {
        const { token, expired } = res.data
        // console.log(res)
        // 寫入 cookie token
        // expires 設置有效時間
        document.cookie = `hexToken=${token};expires=${new Date(expired)}; path=/`
        this.$router.push('/admin')
      }).catch((err) => {
        // console.dir(err.response.data.message)
        alert(err.response.data.message)
      })
    }

  }
}
</script>
<style lang="scss" scoped>
body{
  background: #fff;
}
</style>
