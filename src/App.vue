<template>
  <div id="app">
    <m-header></m-header>
    <tab></tab>
    <keep-alive>
      <!-- 不用每次都发送请求，起缓存功能，提高用户体验 -->
      <router-view></router-view>
    </keep-alive>
  </div>
</template>

<script>
  import MHeader from 'components/m-header/m-header.vue'
  import Tab from 'components/tab/tab.vue'
  export default {
    components: {
      MHeader,
      Tab
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "common/stylus/variable.styl"

  #app {
    color: $color-theme
  }
</style>
