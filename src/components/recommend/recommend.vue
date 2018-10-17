<template lang="html">
  <div class="recommend">
    <scroll ref="scroll" class="recommend-content" :data="discList">
      <div class="">
        <div v-if="swiperList.length" class="slider-wrapper">
          <!-- <Swiper :list="swiperList"></Swiper> -->
          <Slider>
            <div class="" v-for="item in swiperList">
              <a :href="item.linkUrl">
                <img class="needsclick" @onload="loadImage" :src="item.picUrl" alt="">
              </a>
            </div>
          </Slider>
        </div>
        <div class="recommend-list" v-show="discList.length">
          <h1 class="list-title">热门歌单推荐</h1>
          <ul>
            <li @click="selectItem(item)" v-for="item in discList" class="item">
              <div class="icon">
                <img width="100" height="100" v-lazy="item.imgurl">
              </div>
              <div class="text">
                <h2 class="name" v-html="item.creator.name"></h2>
                <p class="desc" v-html="item.dissname"></p>
              </div>
            </li>
          </ul>
        </div>
        <div class="loading-container" v-show="!discList.length">
          <loading></loading>
        </div>
      </div>
    </scroll>
  </div>
</template>

<script>
import Scroll from 'base/scroll/scroll'
import Loading from 'base/loading/loading'
import Swiper from './Swiper.vue'
import Slider from 'base/slider/slider'
import {getRecommend,getDiscList} from 'api/recommend'
import {ERR_OK} from 'api/config'
export default {
  data() {
    return{
      swiperList: [],
      discList: []
    }
  },
  created(){
    this._getRcommend()
    this._getDiscList()
  },
  components: {
    Swiper,
    Slider,
    Scroll,
    Loading
  },
  methods: {
    _getDiscList() {
      getDiscList().then((res)=>{
        if(res.code === ERR_OK)
          this.discList = res.data.list
          // console.log(this.discList);
      })
    },
    _getRcommend() {
      getRecommend().then((res)=>{
        if(res.code === ERR_OK)
         this.swiperList = res.data.slider
      })
    },
    loadImage() {
      if(!this.checkLoaded){
        this.$refs.scroll.refresh()
        this.checkLoaded = true
      }
    }
  }
}
</script>

<style lang="stylus">
@import '~common/stylus/variable';

.recommend
  position: fixed;
  width: 100%;
  top: 88px;
  bottom: 0
  .recommend-content
    background: rgb(232, 232, 232)
    height: 100%;
    overflow: hidden;
    .slider-wrapper
      position: relative;
      width: 100%;
      overflow: hidden;
    .recommend-list
        .list-title
          height: 30px;
          line-height: 30px
          text-align: center;
          background: #fff
          font-size: $font-size-medium
          color: rgb(83, 83, 83)
          border-bottom: 10px solid rgb(232, 232, 232)
        ul
          background: rgb(232, 232, 232)
        .item
          background: #fff
          display: flex
          box-sizing: border-box
          align-items: center
          // padding: 5px 20px 5px 20px;
          margin: 0 8px 10px 8px
          overflow: hidden;
          border-radius: 15px
          height: 100px;
          &.fristchild
            margin: 10px 0 10px 0
          .icon
            flex: 0 0 100px
            width: 60px
            padding-right: 20px
          .text
            text-align: center;
            display: flex
            flex-direction: column
            justify-content: center
            flex: 1
            line-height: 20px
            overflow: hidden
            font-size: $font-size-medium
            .name
              margin-bottom: 10px
              color: rgb(50, 50, 50)
            .desc
              color: rgb(177, 177, 177)
    .loading-container
      position: absolute
      width: 100%
      top: 100%
</style>
