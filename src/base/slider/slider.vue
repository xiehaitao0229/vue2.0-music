<template>
  <div class="slider" ref="slider">
    <div class="slider-group" ref="sliderGroup">
      <slot>
      </slot>
    </div>
    <div class="dots">
      <span class="dot" :class="{active: currentPageIndex === (index) }" v-for="(item, index) in dots"></span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
 /* import {addClass} from 'common/js/dom'*/
  import BScroll from 'better-scroll'
  import {addClass}  from 'common/js/dom'

  export default {
      name:'slider',
      data(){
          return {
              dots:[],
              currentPageIndex:0
          }
      },
    props:{
        //  循环轮播
      loop:{
          type:Boolean,
          default:true
      },
      //  自动轮播
      autoPlay:{
          type:Boolean,
          default:true
      },
      //  轮播时间间隔
      interval:{
          type:Number,
          default:4000
      }
    },
    mounted(){
        setTimeout(()=>{
            // 这里的20ms是因为浏览器刷新的时候一般是17ms
            this._setSliderWidth();
            this._initDots();
            this._initSlider();
            if(this.autoPlay){
                this._play();
            }
        },20)
      //  监听 一个resize事件，当窗口大小改变的时候
      window.addEventListener('resize',()=>{
          if(!this.slider){
              //  当slider没有初始化的时候，什么都不做
              return ;
          }
          this._setSliderWidth(true);
      })
    },
    methods:{
        _setSliderWidth(isResize){
          //  拿到有几个滚动图片
          this.children = this.$refs.sliderGroup.children;
        /*  console.log(this.children.length)*/
          let width = 0;
          let sliderWidth = this.$refs.slider.clientWidth;
          for( var i=0;i<this.children.length;i++ ){
            let child = this.children[i];
            addClass(child,'slider-item');
            child.style.width = sliderWidth + 'px';
            width+=sliderWidth;
          }
          if(this.loop &&!isResize){
              //  如果不是Resize的话就要设置
              width+=2*sliderWidth;
          }
          this.$refs.sliderGroup.style.width = width + 'px'
        },
      _initSlider(){
        this.slider = new BScroll(this.$refs.slider,{
            scrollX:true,
            scrollY:false,
            momentum:false,
            snap:true,
           snapLoop: this.loop,
            snapThreshold:0.3,
            snapSpeed:400,
        })
        this.slider.on('scrollEnd',()=>{
            let pageIndex = this.slider.getCurrentPage().pageX;
            if(this.loop){
                pageIndex -= 1;
            }
          this.currentPageIndex = pageIndex
          if(this.autoPlay){
            clearInterval(this.timer)
            this._play();
          }
        })
      },
      _initDots(){
        this.dots = new Array(this.children.length);
      },
      _play(){
          let pageIndex = this.currentPageIndex+ 1;
          if(this.loop){
              pageIndex +=1;
          }
          this.timer = setTimeout(()=>{
            this.slider.goToPage(pageIndex,0,400)
          },this.interval)
      }
    },
    destroyed(){
        clearInterval(this.timer);
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .slider
    min-height: 1px
    .slider-group
      position: relative
      overflow: hidden
      white-space: nowrap
      .slider-item
        float: left
        box-sizing: border-box
        overflow: hidden
        text-align: center
        a
          display: block
          width: 100%
          overflow: hidden
          text-decoration: none
        img
          display: block
          width: 100%
    .dots
      position: absolute
      right: 0
      left: 0
      bottom: 12px
      text-align: center
      font-size: 0
      .dot
        display: inline-block
        margin: 0 4px
        width: 8px
        height: 8px
        border-radius: 50%
        background: $color-text-l
        &.active
          width: 20px
          border-radius: 5px
          background: $color-text-ll
</style>
