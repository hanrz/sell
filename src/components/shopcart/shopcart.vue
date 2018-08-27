<template>
<div class="shopcart">
  <div class="main-wrapper">
  <div class="content" @click="toggleList">
    <div class="content-left">
      <div class="logo-wrapper">
        <div class="logo" v-bind:class="{'highlight':totalCount>0}">
          <i class="icon-shopping_cart"></i>
        </div>
        <div class="num" v-show="totalCount>0">{{totalCount}}</div>
      </div>
      <div class="price" v-bind:class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
      <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
    </div>
    <div class="content-right" @click.stop.prevent="pay">
      <div class="pay" v-bind:class="payClass">
        {{payDesc}}
      </div>
    </div>
  </div>
  <div class="ball-container">
    <transition-group name="drop" tag="div" v-on:before-enter="beforeEnter" v-on:enter="Enter" v-on:after-enter="afterEnter">
      <div v-for="(ball,index) in balls" :key="index" v-show="ball.show" class="ball">
        <div class="inner inner-hook"></div>
      </div>
    </transition-group>
  </div>
<transition name="fold">
  <div class="shopcart-list" v-show="fold">
    <div class="list-header">
      <h1 class="title">购物车</h1>
      <span class="empty" @click="empty">清空</span>
    </div>
    <div class="list-content" ref="listContent">
      <ul>
        <li class="food" v-for="(food,index) in selectFoods" :key="index">
          <span class="name">{{food.name}}</span>
          <div class="price">
            <span>￥{{food.price*food.count}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food" v-on:dropcart-add="dropcart"></cartcontrol>
          </div>
        </li>
      </ul>
    </div>
  </div>
</transition>
</div>
<transition name="fade">
  <div class="list-mask" v-show="fold" @click="hideMask"></div>
</transition>
</div>
</template>
<script>
import BScroll from 'better-scroll';
import cartcontrol from '../../components/cartcontrol/cartcontrol.vue';
export default {
  props: {
    selectFoods: {
      type: Array,
      default () {
        return [];
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      balls: [
        {
          show: false,
          el: null
        },
        {
          show: false,
          el: null
        },
        {
          show: false,
          el: null
        },
        {
          show: false,
          el: null
        },
        {
          show: false,
          el: null
        }
      ],
      dropBalls: [],
      fold: false
    };
  },
  computed: {
    totalPrice () {
      let total = 0;
      this.selectFoods.forEach((food) => {
        total += food.price * food.count;
      });
      return total;
    },
    totalCount () {
      let count = 0;
      this.selectFoods.forEach((food) => {
        count += food.count;
      });
      return count;
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`;
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice;
        return `还差￥${diff}元起送`;
      } else {
        return '去结算';
      }
    },
    payClass () {
      if (this.totalPrice < this.minPrice) {
        return 'not-enough';
      } else {
        return 'enough';
      }
    }
  },
  watch: {
    totalCount: function () {
      if (this.totalCount === 0) {
        this.fold = false;
        return false;
      }
    },
    fold: function (totalCount) {
      let show = !this.fold;
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.listContent, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      });
      return show;
    }
  },
  methods: {
    toggleList () {
      if (!this.totalCount) {
        return;
      }
      this.fold = !this.fold;
    },
    dropcart (el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i];
        if (!ball.show) {
          ball.show = true;
          ball.el = el;
          this.dropBalls.push(ball);
          return;
        }
      }
    },
    beforeEnter (el) {
      let count = this.balls.length;
      while (count--) {
        let ball = this.balls[count];
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect();
          let x = rect.left - 32;
          let y = -(window.innerHeight - rect.top - 22);
          el.style.display = '';
          el.style.webkitTransform = `translate3d(0,${y}px,0)`;
          el.style.transform = `translate3d(0,${y}px,0)`;
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
          inner.style.transform = `translate3d(${x}px,0,0)`;
        }
      }
    },
    Enter (el, done) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight;
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)';
        el.style.transform = 'translate3d(0,0,0)';
        let inner = el.getElementsByClassName('inner-hook')[0];
        inner.style.webkitTransform = 'translate3d(0,0,0)';
        inner.style.transform = 'translate3d(0,0,0)';
        el.addEventListener('transitionend', done);
      });
    },
    afterEnter (el) {
      let ball = this.dropBalls.shift();
      if (ball) {
        ball.show = false;
        ball.el = null;
        el.style.display = 'none';
      }
    },
    empty () {
      this.selectFoods.forEach((food) => {
        food.count = 0;
      });
    },
    hideMask () {
      this.fold = false;
    },
    pay () {
      if (this.totalPrice < this.minPrice) {
        return;
      }
      window.alert(`支付￥${this.totalPrice}元`);
    }
  },
  components: {
    cartcontrol
  }
};
</script>
<style lang="stylus">
@import "../../common/stylus/mixin.styl"
.main-wrapper{
  position absolute;
  width:100%;
  z-index:50;
}
.shopcart
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 50;
  width: 100%;
  height: 48px;
  .content
    display: flex;
    background: #141d27;
    font-size: 0;
    .content-left
      flex: 1;
      .logo-wrapper
        display: inline-block;
        position: relative;
        top: -10px;
        margin: 0 12px;
        padding: 6px;
        width: 56px;
        height: 56px;
        box-sizing: border-box;
        vertical-align: top;
        border-radius: 50%;
        background: #141d27;
        .logo
          width: 100%;
          height: 100%;
          border-radius: 50%;
          background: #2B343C;
          text-align: center;
          &.highlight
            background: rgb(0, 160, 220);
            i
              color: #fff;
          i
            line-height: 44px;
            font-size: 24px;
            color: #80858a;
        .num
          position: absolute;
          top: 0;
          right: 0;
          width: 24px;
          height: 16px;
          line-height: 16px;
          text-align: center;
          border-radius: 16px;
          font-size: 9px;
          font-weight: 700;
          color: #fff;
          background: rgb(240, 20, 20);
          box-shadow: 0px 4px 8px 0px rgba(0, 0, 0, 0.4);
      .price
        display: inline-block;
        line-height: 24px;
        vertical-align: top;
        margin-top: 12px;
        padding-right: 12px;
        border-right: 1px solid rgba(255, 255, 255, 0.1);
        box-sizing: border-box;
        font-size: 16px;
        font-weight: 700;
        color: rgba(255, 255, 255, 0.4);
        &.highlight
          color: #fff;
      .desc
        display: inline-block;
        vertical-align: top;
        line-height: 24px;
        margin: 12px 0 0 12px;
        font-size: 10px;
        font-weight: 700;
        color: rgba(255, 255, 255, 0.4);
    .content-right
      flex: 0 0 105px;
      width: 105px;
      .pay
        width: 100%;
        height: 48px;
        line-height: 48px;
        text-align: center;
        font-size: 12px;
        color: rgba(255, 255, 255, 0.4);
        font-weight: 700;
        background: #2b333b;
        &.not-enough
          background: #2b333b;
        &.enough
          background: #00b43c;
          color: #fff;
  .ball-container
    .ball
      position: fixed;
      left: 32px;
      bottom: 22px;
      z-index: 200;
      &.drop-enter-active
        transition:all 0.4s cubic-bezier(.56,.04,.83,.67)
      .inner
        width: 16px;
        height: 16px;
        border-radius: 50%;
        background: rgb(0, 160, 220);
        transition:all 0.4s linear
  .shopcart-list
    position :absolute
    left:0
    top: 0
    z-index :-1
    width:100%;
    transform :translate3d(0,-100%,0)
    &.fold-enter-active, &.fold-leave-active
      transition :all 0.5s
      transform :translate3d(0,-100%,0)
    &.fold-enter
      transform :translate3d(0,0,0)
    &.fold-leave-to
      transform :translate3d(0,0,0)
    .list-header
      height 40px
      line-height 40px
      padding 0 18px
      background #f3f5f7
      border-bottom 1px solid rgba(7, 17, 27, 0.1)
      .title
        float left
        font-size 14px
        color rgb(7,17,27)
      .empty
        float right
        font-size 12px
        color rgb(0,160,220)
    .list-content
      padding:0 18px;
      max-height:217px;
      overflow :hidden;
      background : #ffffff;
      .food
        position relative;
        padding 12px 0;
        box-sizing border-box;
        border-1px(rgba(7,17,27,0.1));
        .name
          line-height 24px;
          font-size 14px;
          color rgb(7,17,27);
        .price
          position absolute;
          right 90px;
          bottom 12px;
          line-height 24px;
          font-size 14px;
          font-weight 700;
          color rgb(240, 20, 20);
        .cartcontrol-wrapper
          position absolute;
          right 0;
          bottom 6px
.list-mask
  position :fixed
  top:0
  left :0
  width:100%;
  height:100%
  z-index:40
  background:rgba(7,17,27,0.6)
  backdrop-filter: blur(10px)
  &.fade-enter-active, &.fade-leave-active
    transition :all 0.5s linear
    opacity :1
  &.fade-enter, &.fade-leave-to
    opacity :0
    background : rgba(7,17,27,0)
</style>
