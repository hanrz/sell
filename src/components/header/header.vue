<template>
    <div class="header">
       <div class="content-wrapper">
           <div class="avatar">
               <img width="64" height="64" v-bind:src="seller.avatar">
           </div>
          <div class="content">
                <div class="title">
                  <span class="brand"></span>
                  <span class="name">{{seller.name}}</span>
                </div>
                <div class="description">
                  {{seller.description}}/{{seller.deliveryTime}}分钟送达
                </div>
                <div v-if="seller.supports" class="support">
                    <span class="icon" v-bind:class="classMap[seller.supports[0].type]"></span>
                    <span class="text">{{seller.supports[0].description}}</span>
                </div>
          </div>
          <div class="support-count" v-if="seller.supports" v-on:click="showDetail()">
              <span class="count">{{seller.supports.length}}个</span>
              <i class="icon-keyboard_arrow_right"></i>
          </div>
       </div>
       <div class="bulletin-wrapper" v-on:click="showDetail()">
         <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
         <i class="icon-keyboard_arrow_right"></i>
       </div>
       <div class="background">
          <img width="100%" height="100%" v-bind:src="seller.avatar">
       </div>
       <transition name="fade">
       <div v-show="detailShow" class="detail" transition="">
         <div class="detail-wrapper clearfix">
           <div class="detail-main">
             <h1 class="name">{{seller.name}}</h1>
             <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
             </div>
             <titles :titleName='youhui'></titles>
             <ul v-if="seller.supports" class="suppotrs">
               <li class="support-item" v-for="(item,$index) in seller.supports" :key="item.num">
                 <span class="icon" :class="classMap[seller.supports[$index].type]"></span>
                 <span class="text">{{seller.supports[$index].description}}</span>
               </li>
             </ul>
             <titles :titleName='gonggao'></titles>
             <star></star>
             <div class="bulletin">
               <p class="content">{{seller.bulletin}}</p>
             </div>
           </div>
         </div>
         <div class="detail-close" v-on:click="hideDetail()"><i class="icon-close"></i></div>
       </div>
       </transition>
    </div>
</template>

<script>
import star from '../../components/star/star.vue';
import titles from '../../components/title/title.vue';

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      detailShow: false,
      youhui: '优惠信息',
      gonggao: '商家公告'
    };
  },
  methods: {
    showDetail () {
      this.detailShow = true;
    },
    hideDetail () {
      this.detailShow = false;
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  components: {
    star, titles
  }
};
</script>

<style lang="stylus">
@import "../../common/stylus/mixin.styl"
.header
  color: #fff
  background-color :rgba(7,17,27,0.5)
  position :relative
  overflow :hidden
  .content-wrapper
    position :relative
    padding:24px 12px 18px 24px
    font-size:0
    .avatar
      display :inline-block
      vertical-align: top
      img
        border-radius: 2px
    .content
      display :inline-block
      margin-left :14px
      .title
        margin: 2px 0 8px 0
        .brand
          display :inline-block
          vertical-align : top
          width: 30px
          height: 18px
          bg-img('brand')
          background-size: 30px 18px
          background-repeat : no-repeat
        .name
          margin-left :6px
          font-size:14px
          line-height:18px
          font-weight:bold
      .description
        margin-bottom :10px
        line-height :12px
        font-size:12px
      .support
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height:12px
          margin-right:4px
          background-size :12px 12px
          background-repeat : no-repeat
          &.decrease
            bg-img('decrease_1')
          &.discount
            bg-img('discount_1')
          &.guarantee
            bg-img('guarantee_1')
          &.special
            bg-img('special_1')
          &.invoice
            bg-img('invoice_1')
        .text
          line-height :12px
          font-size :10px
    .support-count
      position :absolute
      right :12px
      bottom :14px
      padding: 0 8px
      height: 24px
      line-height :24px
      border-radius :14px
      background:rgba(0,0,0,0.2)
      text-align :center
      .count
        vertical-align :top
        font-size:10px
      i
        margin-left :2px
        line-height :24px
        font-size:10px
  .bulletin-wrapper
    position :relative
    height :28px
    line-height :28px
    padding :0 22px 0 12px;
    white-space :nowrap
    overflow: hidden
    text-overflow: ellipsis
    background :rgba(7,17,27,0.2)
    .bulletin-title
      display:inline-block
      vertical-align :top
      margin-top:8px
      width :22px
      height :12px
      bg-img('bulletin')
      background-size:22px 12px
    .bulletin-text
      vertical-align :top
      margin:0 4px
      font-size:10px
    i
      position: absolute
      font-size:10px
      right:12px
      top :8px
  .background
    position :absolute
    top:0
    left :0
    width :100%
    height :100%
    z-index :-1
    filter :blur(10px)
  .detail
    position:fixed
    z-index :100
    top:0
    left:0
    width:100%
    height :100%
    overflow :auto
    background : rgba(7,17,27,0.8)
    transition :all 0.5s
    backdrop-filter: blur(10px)
    &.fade-enter-active, &.fade-leave-active
      opacity :1
    &.fade-enter, &.fade-leave-to
      opacity :0
      background : rgba(7,17,27,0)
  .detail-wrapper
    width:100%
    min-height :100%
    .detail-main
      margin-top :64px
      padding-bottom: 64px
      .name
        font-size:16px
        font-weight :700
        line-height :16px
        text-align :center
      .star-wrapper
        margin-top:16px
        padding:2px 0
        text-align :center
      .suppotrs
        width:80%
        margin:0 auto
        .support-item
          padding:0 12px
          margin-bottom : 12px
          font-size:0
          &:last-child
            margin-bottom:0
          .icon
            display :inline-block
            width:16px
            height:16px
            vertical-align:top
            margin-right:6px
            background-size:16px 16px
            background-repeat:no-repeat
            &.decrease
              bg-img('decrease_2')
            &.discount
              bg-img('discount_2')
            &.guarantee
              bg-img('guarantee_2')
            &.special
              bg-img('special_2')
            &.invoice
              bg-img('invoice_2')
          .text
            font-size:12px
            font-weight:200
            line-height:16px
      .bulletin
        width:80%;
        margin: 0 auto;
        .content
          padding: 0 24px;
          line-height :24px;
          font-size:12px;
  .detail-close
    position :relative
    width:32px
    height :32px
    margin: -64px auto 0 auto
    font-size:32px
    clear: both
</style>
