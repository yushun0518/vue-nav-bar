<template>
  <div class="pannel">
    <span class="title">{{datas.name}}</span>
    <div class="list">
      <div class="item" @click="toClassify(item)" v-for="(item, index) in datas.childCategory" :key="index">
        <div class="image">
          <img :src="item.images" mode="aspectFit" />
        </div>
        <span>{{item.name}}</span>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      datas: {
        type: Object,
        default: () => ({})
      }
    },
    methods: {
      toClassify(obj) {
        this.$emit('classify', obj)
      }
    }
  }
</script>

<style scoped lang="stylus">
  @import "~common/stylus/index"

  .pannel
    .title
      font-size .28rem
      color #333
      height .8rem
      line-height .8rem
      border-bottom .01rem solid #f4f4f4
      display block
      margin .12rem 0 .16rem
      font-weight bold
    .list  
      dis-flex(flex,,wrap)
      .item
        width 33.333%
        height 2.5rem
        >span 
          display block
          width 100%
          text-align center
          line-height .6rem
          font-size 0.24rem
          color #000
        .image
          height 1.9rem
          dis-flex()
          align-items center
          justify-content center
          >img 
            max-width 80%
            max-height 80%
</style>