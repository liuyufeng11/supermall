<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物中心</div></nav-bar>
         <scroll class="conterss" ref="scroll"
                 :probe="3"
                 @positions="positions"
                 :pullupload="true"
                 @pulling="pulling">
           <HomeSwiper :banners="banners"/>
           <recommends-view :recommends="recommends"></recommends-view>
           <feature-view></feature-view>
           <tab-conterol class="conterol" :title="['流行','新款','精选'] " @tabclick="tabclick"></tab-conterol>
           <commodity :cdity="goods[defaultgoods].list"></commodity>
         </scroll>
    <back-top @click.native="backtop" v-show="isshow"></back-top>
  </div>
</template>

<script>
   import NavBar from "components/common/navbar/navbar";
   import HomeSwiper from "./child/HomeSwiper";
   import TabConterol from "../../components/conent/TabConter/TabConterol";

   import commodity from "../../components/conent/commodity/commodity";
   import recommendsView from "./child/recommends-view"
   import FeatureView from "./child/FeatureView";

    import BackTop from "../../components/conent/BackTop/BackTop";
    import {getHomeMultiData,getHomeGoods} from "newwork/home";
    import scroll from "../../components/common/scroll/scroll";


  export default {
    name: "Home",
    components: {
      NavBar,
      HomeSwiper,
      recommendsView,
      FeatureView,
      TabConterol,
      commodity,
      scroll,
      BackTop
    },
    data() {
      return {
        banners: [],
        recommends: [],
        goods:{
          pop: {page :0 ,list:[]},
          new: {page :0 ,list:[]  },
          sell: {page :0 ,list:[]}
        },
        defaultgoods:'pop',
        scroll: null,
        isshow:false
      }
    },
    created() {
      this.getHomeMultiData()

      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')
    },
    methods:{
      //事件监听
      tabclick(index){
           switch (index) {
               case 0:
                 this.defaultgoods='pop'
                 break
               case 1:
                 this.defaultgoods='new'
                  break
               case 2:
                 this.defaultgoods='sell'
                  break
           }
      },
      //回到顶部
      backtop(){
         this.$refs.scroll.backtop(0,0)
      },
      //显示隐藏回到顶部
      positions(postiont){
         this.isshow=(-postiont.y)>900;
      },
      //下拉加载更多
      pulling(){
        console.log("jiaz")
         this.getHomeGoods(this.defaultgoods)
      },
      //网络请求
      getHomeMultiData(){
        // 1.请求多个数据
        getHomeMultiData().then(res => {
          this.banners = res.data.banner.list;
          this.recommends = res.data.recommend.list;
        })
      },
      //流行，精款。。。。。。
      getHomeGoods(type){
            let page=this.goods[type].page+1;
               getHomeGoods(type,page).then(res =>{
                   this.goods[type].list.push(...res.data.list);
                   this.goods[type].page+=1;
                  this.$refs.scroll.finishPullUp()
                })
      }
    }
  }
</script>

<style scoped>
  #home{
    height: 100vh;
    position: relative;
  }
  .home-nav {
    background-color: var(--color-tint);
    color: #fff;
    position: fixed;
    z-index: 9;
    top: 0px;
    left: 0px;
    right: 0px;
  }
 .conterol{
     position: sticky;
     top: 44px;
     z-index: 9;
  }
  .conterss{
    overflow: hidden;
    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;

  }
</style>
