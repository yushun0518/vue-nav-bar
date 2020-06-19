<template>
  <div class="classify">
    <div class="search" @click="toSearch">
      <!-- <y-search></y-search> -->
      <div class="search_box">
        <img src="../../common/images/search.png" />
        <input type="text" placeholder="请输入搜索内容" />
      </div>
    </div>
    <div class="content">
      <div class="nav" ref="nav" style="transition:1s">
        <!--:style="{transform: `translateY(-${transform}rem)`}"-->
        <div class="nav_wrap" ref="navWrap" :style="{transform: `translateY(-${transform}rem)`}">
          <div
            class="item"
            :class="{'on' : navIndex === index}"
            v-for="(item, index) in navList"
            @click="tabNav(index, $event)"
            :key="index"
          >{{item.name}}</div>
        </div>
      </div>
      <div
        class="list"
        ref="list"
        @touchstart="listStart"
        @touchmove="listMove"
        @touchend="listEnd"
      >
        <div class="swiper" v-if="bannerImg.length">
          <van-swipe :autoplay="5000" indicator-color="red">
            <van-swipe-item
              v-for="(item, index) in bannerImg"
              :key="index"
              @click="swiperClick(item.id)"
            >
              <img :src="item.imageUrl" alt />
            </van-swipe-item>
          </van-swipe>
        </div>
        <y-pannel
          @classify="itemClassify"
          v-for="(item, index) in classifyList"
          :datas="item"
          :key="index"
        />
      </div>
    </div>
    <y-nav-bar />
  </div>
</template>

<script>
import yPannel from './pannel'
import yNavBar from 'components/navBar'
import {list1, list2} from './data' // 模拟数据

export default {
  components: {
    yPannel,
    yNavBar
  },

  data() {
    return {
      navIndex: 0,
      navList: [],
      bannerImg: [],
      classifyList: [],
      startX: 0,
      startY: 0,
      isLowest: false,
      isUp: true,
      isMove: false,
      transform: 0,
      navStartX: 0,
      navStartY: 0,
      getClassifyActive: 0
    }
  },

  created() {
    this.navIndex = this.getClassifyActive
    // this.setTabActive(1)
    this.categoryAll()
  },

  mounted() {
    let $list = this.$refs.list
    $list.addEventListener('scroll', this.handleScroll, true)
  },

  methods: {
    listStart(event) {
      this.startX = event.changedTouches[0].pageX
      this.startY = event.changedTouches[0].pageY
    },
    listMove(event) {
      const { pageX: moveEndX, pageY: moveEndY } = event.changedTouches[0]
      const { scrollTop } = this.$refs.list
      const X = moveEndX - this.startX
      const Y = moveEndY - this.startY
      if (Math.abs(Y) > Math.abs(X) && Y > 0) {
        this.isUp = false
        this.isMove = true
        if (scrollTop == 0) event.preventDefault()
      } else if (Math.abs(Y) > Math.abs(X) && Y < 0) {
        this.isUp = true
        this.isMove = true
      }
    },
    listEnd(event) {
      const { scrollHeight, clientHeight, scrollTop } = this.$refs.list
      if (scrollHeight == clientHeight || scrollTop == 0) {
        this.isLowest = true
      }
      let { navIndex, isUp, isMove, navList, isLowest } = this
      if (isLowest && isMove) {
        if (isUp) {
          navIndex < navList.length - 1 && this.tabNav(navIndex + 1)
        } else {
          navIndex != 0 && this.tabNav(navIndex - 1)
        }
      }
      this.isMove = false
    },
    handleScroll(event) {
      const { scrollTop, clientHeight, scrollHeight } = event.target
      if (scrollTop + clientHeight >= scrollHeight-1) {
        this.isLowest = true
      }
    },
    tabNav(index, event) {
      const $nav = this.$refs.nav
      let { scrollTop, clientHeight, scrollHeight } = $nav
      const { clientHeight: itemHeight } = $nav.querySelector('.item'),
        len = Math.floor(clientHeight / itemHeight),
        middle = Math.ceil(len / 2) - 1,
        currentScrollHeight = (index - middle) * itemHeight,
        { navIndex } = this,
        prevScroll = Math.abs(index - navIndex) * itemHeight
      let timer = setInterval(() => {
        if (navIndex < index) {
          scrollTop += prevScroll / 16
          $nav.scrollTop = scrollTop
          if (scrollTop >= currentScrollHeight) {
            clearInterval(timer)
            timer = null
          }
        } else {
          scrollTop -= prevScroll / 16
          $nav.scrollTop = scrollTop
          if (scrollTop <= currentScrollHeight) {
            clearInterval(timer)
            timer = null
          }
        }
      }, 20)
			this.$refs.list.scrollTo(0,0)
      this.isLowest = false
      this.navIndex = index
      this.categoryList(this.navList[index].id)
      this.swiperPicture(this.navList[index].id)
      // this.setClassifyActive(index)
      this.getClassifyActive = index 
    },

    // 一级分类
    categoryAll() {
      const res = list1
      this.navList = res.data
      let { getClassifyActive } = this
      this.categoryList(res.data[getClassifyActive].id)
      this.swiperPicture(res.data[getClassifyActive].id)
    },

    // 子分类列表
    categoryList(id) {
      const res = list2
      const num = Math.ceil(Math.random() * 5)
      console.log(num)
      this.classifyList = getRandom(res.data.slice(0, num)) 

      // 注由于这里子分类数据只有一个,所有采用打乱顺序的方式显示,实际可根据请求回来的数据来显示
      function getRandom(arr) {
        let [newArr, selItem] = [[], null]
        let len = arr.length 
        while(newArr.length < len) {
          selItem = arr[Math.floor(Math.random() * len)]
          if (!newArr.find(item => item.id == selItem.id)) {
            newArr.push(selItem)
          }
        }
        return newArr
      } 
    },

    // 轮播图
    swiperPicture(id) {
      /* apiServer.swiperPicture(id).then(res => {
				  this.bannerImg = res.data
				}) */
      this.bannerImg = []
    },

    // 搜索
    toSearch() {
      this.$router.push('/search')
    },

    itemClassify(obj) {
      console.log(obj)
      this.$router.push({
        name: 'searchLists',
        query: {
          id: obj.id,
          from: 'classfiy'
        }
      })
    },
    //点击轮播图
    swiperClick(id) {
      //分类 banner埋点
      /* this.LogClick({
        type: 'homebanner_click',
        categoryId: this.navList[index].id,
        bannerId: id
      }) */
    }
  }
}
</script>

<style scoped lang="stylus">
@import '~common/stylus/index'
.classify
  padding-top 1rem
  .search
    dis-flex()
    aspect(100%, 1rem)
    align()
    left 0
    top 0
    border-1px(rgba(0, 0, 0, 0.25))
    position fixed
    z-index 200
    background #fff
    &_box
      aspect(6.9rem, 0.64rem)
      border-radius 0.04rem
      background #F4F4F4
      dis-flex()
      align-items center
      border-radius 0.5rem
      input
        flex 1
        border none
        outline none
        background none
        height 0.48rem
        font-size 0.3rem
        color #333
      img
        aspect(0.4rem)
        margin 0 0.18rem 0 0.16rem
        &.clear
          aspect(0.44rem)
          margin-right 0.1rem
  .content
    background #fff
    bottom 0
    width 100%
    dis-flex()
    .nav
      width 2rem
      background #F4F4F4
      height calc(100vh - 2rem)
      overflow-y scroll
      .nav_wrap
        transition 0.8s
      .item
        aspect(100%, 1rem)
        dis-flex()
        align-items center
        justify-content center
        font-size 0.26rem
        color #666
        &.on
          background #fff
          color #DC2828
          font-size 0.3rem
          position relative
          text-align center
          &:before
            content ''
            aspect(0.04rem, 0.4rem)
            background #DC2828
            top 50%
            transform translateY(-50%)
            left 0
            position absolute
    .list
      flex 1
      height calc(100vh - 2rem)
      overflow-y scroll
      padding 0.2rem
      .swiper
        aspect(100%, 2.1rem)
        image
          aspect(100%, 100%)
        >>>.van-swipe
          height 100%
          .van-swipe-item >img
            aspect(100%)
</style>