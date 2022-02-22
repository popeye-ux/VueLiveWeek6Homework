<template>
    <h1>產品列表</h1>
    {{products}}
    <div class="container">
        <div class="row row-cols-1 row-cols-lg-4 g-5">
            <div class="col" v-for="item in products" :key="item.id">
                <div class="card h-100" style="width: 18rem;">
                    <img :src="item.imageUrl" class="card-img-top img-fluid" alt="...">
                    <div class="card-body">
                        <h5 class="card-title">{{item.title}}</h5>
                        <p class="card-text">{{item.price}}</p>
                        <del>{{item.origin_price}}</del>
                        <a href="#" class="btn btn-primary">Go somewhere</a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
  data () {
    return {
      products: []
    }
  },
  mounted () {
    const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products`
    this.$http
      .get(url)
      .then((response) => {
        this.products = response.data.products
      })
  }
}
</script>
