<template>
  <div class="app-container">
    <Header title="购物车案例"></Header>
    <div>
      <Goods v-for="item in list" :key="item.id" :title="item.goods_name" :pic="item.goods_img" :id="item.id"
        :price="item.goods_price" :state="item.goods_state" :count="item.goods_count" @state-change="getNewState"></Goods>
      <Footer :isfull="fullName" :amount="amt" :all='total' @full-change="getFullState"></Footer>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Header from '@/components/Header/Header.vue';
import Goods from '@/components/Goods/Goods.vue';
import Footer from '@/components/Footer/Footer.vue';
import bus from '@/components/eventBus.js';
export default {
  data() {
    return {
      list: []
    }
  },
  computed: {
    fullName(){
      return this.list.every(item => item.goods_state);
    },
    amt() {
      return this.list.filter(item => item.goods_state).reduce((total, item) => total += item.goods_price * item.goods_count, 0)
    },
    total() {
      return this.list.filter(item => item.goods_state).reduce((t, item) => t += item.goods_count, 0)
    }
  },
  created() {
    this.initCartList();
    bus.$on('share', val => {
      this.list.some(item => {
        if(item.id === val.id) {
          item.goods_count = val.value;
          return true;
        }
      })
    })
  },
  methods: {
    async initCartList() {
      let { data: res } = await axios.get('https://www.escook.cn/api/cart');
      if (res.status === 200) {
        this.list = res.list;
      }
    },
    getNewState(obj) {
      this.list.some(item => {
        if(item.id === obj.id){
          item.goods_state = obj.value;
          return true;
        }
      })
    },
    getFullState(val) {
      this.list.forEach(item => item.goods_state = val);
    }
  },
  components: {
    Header,
    Goods,
    Footer
  }
}
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
