<!--底部通用导航栏-->
<template>
  <div class="navbar">
    <div class="seat"></div>
    <div class="navbar_cont">
      <div class="item" v-for="(item, index) in tabIcons" :key="index" @click="tabClick(index)">
        <span class="num" v-if="index==3&&goodsCount">{{goodsCount}}</span>
        <img :src="require(`${tabImage(index)}`)" :class="{'oil':index==2}" class="img">
        <p class="txt" :class="{'on':getTabbarActive==index}">{{item}}</p>
      </div>
    </div>
    <!-- <y-download v-model="showDownload" /> -->
    <!-- <van-dialog
      v-model="showDownload"
      message="暂不支持此功能，请下载原生APP"
      show-cancel-button
      @confirm="toDwonload"
    >
    </van-dialog> -->
  </div>
</template>

<script>
// import {mapMutations, mapGetters} from 'vuex'
import { Dialog } from 'vant'
export default {


  data() {
    return {
      tabIcons: ['首页','分类','一键购物','购物车','我的'],
      showDownload: false,
			getTabbarActive: 1
    }
  },

  computed: {
    tabImage(index) {
      return (index) => {
        if (index == this.getTabbarActive) {
          if (index == 2) {
            return `./images/tabicon${index+1}.png`
          }
          return `./images/tabicon${index+1}s.png`
        }
        return `./images/tabicon${index+1}.png`
      }
    },

    // ...mapGetters(['getTabbarActive','getGoodsCount']),
    goodsCount() {
      let count = this.getGoodsCount || parseInt(localStorage.goodsDetailCount)
      return count > 99 ? '99+' : count
    }
  },

  methods: {
    tabClick(index) {
      
      switch(index) {
        case 0: 
          //埋点
          this.$router.push('/index')
          break
        case 1: 
          this.$router.push('/classify')
          break
        case 2:
          break
        case 3: 
          this.$router.push({
            name: 'shopCart',
            params: {
              from: 'other'
            }
          })
          break
        case 4:
          this.$router.push('/mine')
          break        
      }
    },

    /* ...mapMutations({
      'setTabActive': 'SET_TABBARACTIVE'
    }) */
  }
}
</script>

<style scoped lang="stylus">
@import '~common/stylus/index'
.navbar
  // >>>.van-dialog
  //   width 5.8rem
  //   .van-button__text
  //     font-size .28rem
  //   .van-dialog__confirm
  //     background #DC2828
  //     color #fff
  .seat
    aspect(100%,1rem)
  &_cont 
    aspect(100%,1rem)
    background #fff 
    position fixed
    left 0
    bottom 0
    dis-flex()
    &:after
      content ''
      aspect(.86rem)
      background #fff 
      border-radius 50%
      display block
      position absolute
      left 50% 
      transform translateX(-50%)
      top -.27rem
      z-index 10
    .item
      flex 1
      dis-flex(flex,column)
      align()
      z-index 11
      position relative
      .num
        position absolute
        height .3rem
        display flex
        background #dc2828
        color #fff
        font-size .2rem
        top .05rem
        left .8rem
        min-width .3rem
        border-radius .15rem
        padding 0 .06rem
        align()
        z-index 20
      .img
        aspect(.48rem)
        &.oil
          aspect(.66rem)
          transform translateY(-.18rem)
          +.txt
            transform translateY(-.09rem)
      .txt
        font-size .2rem
        color #333
        &.on
          color #DC2828
</style>