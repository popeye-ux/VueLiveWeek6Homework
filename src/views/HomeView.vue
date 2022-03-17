<template>
  <div class="container mb-5 mt-0">
    <div class="row">
      <div class="col col-sm-3"></div>
      <div class="col col-sm-6 main-bg">
        <swiper :slides-per-view="1" :space-between="1000" :pagination="pagination" :loop="true" :modules="modules" :autoplay="{
      delay: 8000,
      disableOnInteraction: false,
    }">
          <swiper-slide v-for="item in recommendLike" :key="item.id">
            <img :src="item.imagesUrl[1]" alt="item.title" id=“myCircle” />
          </swiper-slide>
        </swiper>
      </div>
      <div class="col col-sm-3"></div>
    </div>
  </div>
  <div class="container">
    <div class="row">
      <div class="col col-sm-6"><h1>img</h1></div>
      <div class="col col-sm-6">
        <small>CONCEPT</small>
        <h2>在乎您對時間的珍惜</h2>
        <p></p>
      </div>
      <div class="col col-sm-6">
        <h2>在乎您對時間的珍惜</h2>
        <p></p>
      </div>
      <div class="col col-sm-6">
        <h1>img</h1>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import { Swiper, SwiperSlide } from 'swiper/vue/swiper-vue'
import { Pagination, Autoplay } from 'swiper'

// import 'swiper/swiper.scss'

// import 'swiper/modules/pagination/pagination.min.css'
export default {
  // name: 'Home',
  // components: {
  //   HelloWorld
  // },
  data () {
    return {
      products: [],
      recommendLike: [],
      modules: [Pagination, Autoplay],
      pagination: {
        clickable: true,
        renderBullet: function (index, className) {
          // return '<span class="' + className + '">' + (index + 1) + '</span>'
          return `<div class="list__item">
        <div class="circle ">
         
          <svg viewBox="0 0 80 80" class="${className}">
            <circle cx="36" cy="40" r="25"/>
          </svg>
          <svg viewBox="0 0 80 80" class="circle-back">
            <circle cx="36" cy="40" r="25"/>
          </svg>
          <span class="dot" />
        </div>
      </div>`
        }
      },
      circleWatch: ''
    }
  },
  components: {
    Swiper,
    SwiperSlide
  },
  methods: {
    getData () {
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products`
      this.$http.get(url).then((response) => {
        this.products = response.data.products
        // console.log(this.productDetail.id)
        this.recommendLike = this.products.filter(function (item) {
          if (item.recommend === 1) {
            return item
          }
        })
        console.log(this.recommendLike)
      })
    }
  },
  mounted () {
    // axios -> this.$http
    // console.log(this.$http.get)
    console.log(process.env.VUE_APP_API, process.env.VUE_APP_PATH)
    this.getData()
    // this.circleWatch = document.getElementById('myCircle')
    // this.circleAnimation()
  }
}
</script>
<style lang="scss">
@import '@/assets/stylesheets/helpers/swiper.scss';
@import '@/assets/stylesheets/helpers/pagination.scss';
@import '@/assets/stylesheets/helpers/swiper-vars.scss';
:root {
  --color: #ff5a5e;
  --inactive-color: rgb(211, 211, 211);
  --stroke-width: 1;
}
.list {
  display: flex;
}
.list__item {
  margin-right: 3px;
}
.list__item:last-child {
  margin-right: 0;
}
.circle {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  width: 80px;
  height: 80px;
}
.circle-back {
  position: absolute;
  inset: 0;
  color: var(--inactive-color);
  stroke: yellow;
  stroke-width: 2px;
  z-index: -5;
  fill: none;
}
.circle-front {
  position: absolute;
  inset: 0;
  color: var(--color);
  stroke: currentColor;
  stroke-width: var(--stroke-width);
  fill: none;
  /* Make the start of the animation visible from above. */
  transform: rotate(-90deg);
  /*  */
  stroke-dasharray: 157 157;
  stroke-dashoffset: -157;
  transition: stroke-dashoffset ease-in-out 500ms;
}
.swiper-pagination-bullet-active .circle-front {
  /* Animate with keyframes instead of transition. */
  transition: none;
  /* Indication of 0% progress. */
  stroke-dasharray: 0 157;
  stroke-dashoffset: 0;
  animation: spin 3s linear;
  animation-fill-mode: forwards;
}

.dot {
  display: block;
  width: 8px;
  height: 8px;
  background: var(--inactive-color);
  border-radius: 8px;
}

.circle.active .dot {
  background: var(--color);
}

@keyframes spin {
  from {
    stroke-dasharray: 0 157;
  }

  to {
    stroke-dasharray: 157 157;
  }
}

.swiper {
  width: 100%;
  height: 100%;
}

.swiper-slide {
  text-align: center;
  font-size: 18px;
  background: #fff;

  /* Center slide text vertically */
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  -webkit-justify-content: center;
  justify-content: center;
  -webkit-box-align: center;
  -ms-flex-align: center;
  -webkit-align-items: center;
  align-items: center;
}

.swiper-slide img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

// .swiper-pagination-bullet {
//   width: 20px;
//   height: 20px;
//   text-align: center;
//   line-height: 20px;
//   font-size: 12px;
//   color: #000;
//   opacity: 1;
//   background: rgba(0, 0, 0, 0.2);
// }

// .swiper-pagination-bullet-active {
//   color: #fff;
//   background: #007aff;
// }
// .swiper-pagination, swiper-pagination-horizontal {
//   background: #000;
//   width: 200px!important;
//   height: 60px;
//   right: -100px!important;
// }

</style>
