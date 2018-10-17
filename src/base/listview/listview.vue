<template lang="html">
  <scroll class="Listview"
          :data="data"
          :listenScroll = "listenScroll"
          ref="listview"
          @scroll = 'scroll'
          >
    <ul>
      <li v-for="group in data" class="list-group" ref="listGroup">
        <h2 class="list-group-title">{{group.title}}</h2>
        <ul>
          <li v-for="item in group.items" class="list-group-item">
            <img v-lazy="item.avatar" alt="" class="avatar">
            <span class="name">{{item.name}}</span>
          </li>
        </ul>
      </li>
    </ul>
    <div class="list-shorcut" @touchstart = "onShortcutTouchStart" @touchmove.stop.prevent = "onShortcutTouchMove">
      <ul>
        <li v-for = '(item,index) in shortcutList' class="item" :data-index="index">
          {{item}}
        </li>
      </ul>
    </div>
  </scroll>
</template>

<script>
import Scroll from 'base/scroll/scroll'
import {getData} from 'common/js/dom'
import Loading from 'base/loading/loading'

const ANCHOR_HEIGHT = 18;
export default {
  props:{
    data:{
      type: Array,
      default: []
    }
  },
  data(){
    return{
      scrollY: -1,
      currentIndex: 0
    }
  },
  created(){
    this.touch = {},
    this.listenScroll = true
  },
  components:{
    Scroll,
    Loading
  },
  computed: {
    shortcutList() {
      return this.data.map((group)=>{//map()方法返回一个新数组，数组中的元素为原始数组元素调用函数处理的后值。
        return group.title.substr(0, 1)
      })
    }
  },
  methods: {
    onShortcutTouchStart(e) {
      let anchorIndex = getData(e.target,'index')
      let firstTouch = e.touches[0];
      this.touch.y1 = firstTouch.pageY
      this.touch.anchorIndex = anchorIndex
      console.log(this.touch.anchorIndex);
      this._scrollTo(anchorIndex)
    },
    onShortcutTouchMove(e) {
      let firstTouch = e.touches[0]
      this.touch.y2 = firstTouch.pageY
      let delta = (this.touch.y2 - this.touch.y1) / ANCHOR_HEIGHT | 0
      let anchorIndex = parseInt(this.touch.anchorIndex) + delta

      this._scrollTo(anchorIndex)
    },
    scroll(pos){
      this.scrollY = pos.y;
    },
    _scrollTo(index) {
      this.$refs.listview.scrollToElement(this.$refs.listGroup[index],800)
      console.log('succ');
    }
  }
}
</script>

<style lang="stylus">
@import "~common/stylus/variable"

  .Listview
    position: relative
    width: 100%
    height: 100%
    overflow: hidden
    background: $color-background
    .list-group
      // padding-bottom: 30px
      .list-group-title
        height: 30px
        line-height: 30px
        padding-left: 20px
        font-size: $font-size-small
        color: $color-text-l
        background: rgb(170, 3, 3)
      .list-group-item
        display: flex
        align-items: center
        padding: 10px 0 10px 30px
        .avatar
          width: 50px
          height: 50px
          border-radius: 50%
        .name
          margin-left: 20px
          color: $color-text-l
          font-size: $font-size-medium
    .list-shorcut
      position: absolute;
      z-index: 30
      right: 10px;
      top: 50%;
      transform: translateY(-50%);
      width: 20px;
      padding: 20px 0;
      border-radius: 10px
      text-align: center
      background: $color-background-d
      font-family: Helvetica
      .item
        padding: 3px
        line-height: 1
        color: $color-text-l
        font-size: $font-size-small
        &.current
          color: $color-theme
</style>
