<template>
    <div class="star-title">
        <div class="line"></div>
        <div class="text">{{titleName}}</div>
        <div class="line"></div>
    </div>
</template>
<script>
export default {
  props: {
    titleName: String
  }
};
</script>
<style lang="stylus">
      .star-title
        display: flex
        width:80%
        margin:28px auto 24px auto
        .line
          flex:1
          position: relative
          top:-6px
          border-bottom:1px solid rgba(255,255,255,0.2)
        .text
          font-size:14px
          font-weight :700
          padding: 0 12px
</style>
