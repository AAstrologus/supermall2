<template>
<div id="home">
  <nav-bar class="home-nav">
    <div slot="center">购物街</div>
  </nav-bar>
  <home-swiper :banners="banners"></home-swiper>
  <recommend-view :recommends="recommends"/>
  <feature-view />
  <tab-control :titles="['流行','新款','精选']" class="tab-control" @tabClick="tabClick"/>
  <goods-list :goods="showGoods"></goods-list>
</div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar";
import HomeSwiper from "./childComps/HomeSwiper";
import {getHomeMultiData,getHomeGoods} from "network/home";
import RecommendView from "./childComps/RecommendView";
import FeatureView from "./childComps/FeatureView";
import TabControl from "components/content/tabControl/TabControl";
import GoodsList from "components/content/goods/GoodsList";

export default {
  name: "Home",
  components:{
    GoodsList,
    HomeSwiper,
    NavBar,
    RecommendView,
    FeatureView,
    TabControl
  },
  data(){
    return {
      banners:[],
      recommends:[],
      goods:{
        'pop':{page:0,list:[]},
        'new':{page:0,list:[]},
        'sell':{page:0,list:[]}
      },
      goodsType:['pop','new','sell'],
      currentType:'pop'
    }
  },
  created() {
    this.getHomeMultiData()
    this.getHomeGoods('pop')
    this.getHomeGoods('new')
    this.getHomeGoods('sell')
  },
  computed:{
    //当前选择的是哪个分类，pop new sell
    showGoods(){
      return this.goods[this.currentType].list
    }
  },
  methods:{
    getHomeMultiData(){
      getHomeMultiData().then(res=>{
        this.banners=res.data.banner.list
        this.recommends=res.data.recommend.list
      })
    },
    getHomeGoods(type){
      let page=this.goods[type].page+1
      getHomeGoods(type,page).then(res=>{
        this.goods[type].list.push(...res.data.list)
        this.goods[type].page+=1
      })
    },
    tabClick(index){
      this.currentType=this.goodsType[index]
    }
  }
}
</script>

<style scoped>
  #home{
    padding-top: 44px;
  }
  .home-nav {
    background-color: var(--color-tint);
    color: #fff;

    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 9;
  }
  .tab-control {
    position: sticky;
    top: 44px;
  }
</style>
