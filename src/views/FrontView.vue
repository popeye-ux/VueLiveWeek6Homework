<template>
<FrontNavbar></FrontNavbar>
  <h2 class="text-center">前台的畫面</h2>
  <router-view />
</template>
<script>
import FrontNavbar from '@/components/FrontNavbar.vue'
export default {
  components: {
    FrontNavbar
  }
}
</script>
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
