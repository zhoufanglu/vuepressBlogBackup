<template>
  <div
      :class="classIndex"
      @click="$router.push(item.path)">
    <!--    https://github.com/zhoufanglu/markdownPhoto/blob/master/juRen/1.jpg?raw=true-->
    <!--动态图片 小女孩-->
    <!--    <div class="draw"><img :src="`https://github.com/zhoufanglu/markdownPhoto/blob/master/blog-img/CuteGirl0000${imgIndex}.gif?raw=true`" alt=""></div>-->
    <!--进击的巨人-->
    <!--    <div class="draw"><img :src="require(`../docs/.vuepress/public/img/juren/${imgIndex}.jpg`)" alt=""></div>-->
    <div class="draw"><img :src="imgIndex | imgPath" alt=""></div>
    <div style="margin-left: 2rem;">
      <i v-if="item.frontmatter.sticky" class="iconfont reco-sticky"></i>
      <div class="title">
        <i v-if="item.frontmatter.keys" class="iconfont reco-lock"></i>
        <router-link :to="item.path">{{item.title}}</router-link>
      </div>
      <div class="abstract" v-html="item.excerpt"></div>
      <PageInfo
          :pageInfo="item"
          :currentTag="currentTag">
      </PageInfo>
    </div>
  </div>
</template>

<script>
import PageInfo from './PageInfo'
const imgList = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
const classList = [['abstract-item', 'draw'], ['abstract-item', 'draw', 'meet'], ['abstract-item', 'center']]
let count = 0
let LCount = 0
export default {
  components: {PageInfo},
  props: ['item', 'currentPage', 'currentTag'],
  computed: {
    imgIndex: function () {
      count++
      if (count >= imgList.length) {
        count = 0
      }
      return imgList[count]
    },
    classIndex: function () {
      LCount++
      if (LCount >= classList.length) {
        LCount = 0
      }
      return classList[LCount]
    }
  },
  filters: {
    imgPath(index) {
      console.log(index)
      return require(`../../../docs/.vuepress/public/img/mishanwu/${index}.jpg`)
    }
  }
}
</script>

<style lang="stylus" scoped>
//@require '../styles/mode.styl'
.abstract-item
  position relative
  margin: 0 auto 20px;
  padding: 16px 20px;
  width 100%
  overflow: hidden;
  border-radius: $borderRadius
  box-shadow: var(--box-shadow);
  box-sizing: border-box;
  transition all .3s
  background-color var(--background-color)
  cursor: pointer;
  display: flex;
  flex-direction: row;
  flex-wrap:wrap;
  justify-content: start;
  align-items:center;
  &::before,&::after {
    box-sizing: inherit;
    content: '';
    position: absolute;
    width: 100%;
    height: 100%;
  }
  &::before,&::after {
    border: 2px solid transparent;
    width: 0;
    height: 0;
  }
  /** 图片效果 **/
  &:hover .draw img {
    transform: scale(1);
  }
  > * {
    pointer-events: auto;
  }
  .draw {
    width: 40%;
    height: 200px;
    border-radius: 10px;
    overflow: hidden;
    img{
      width:100%;
      height:100%;
      transform: scale(1.2);
      transition: transform 1s;
    }
  }
  .reco-sticky
    position absolute
    top 0
    left 0
    display inline-block
    color $accentColor
    font-size 2.4rem
  &:hover
    box-shadow: var(--box-shadow-hover)
  .title
    position: relative;
    font-size: 1.28rem;
    line-height: 46px;
    display: inline-block;
    a
      color: var(--text-color);
    .reco-lock
      font-size 1.28rem
      color $accentColor
  /* &:after
     content: "";
     position: absolute;
     width: 100%;
     height: 2px;
     bottom: 0;
     left: 0;
     background-color: $accentColor;
     visibility: hidden;
     -webkit-transform: scaleX(0);
     transform: scaleX(0);
     transition: .3s ease-in-out;
   &:hover a
     color $accentColor
   &:hover:after
     visibility visible
     -webkit-transform: scaleX(1);
     transform: scaleX(1);*/
  .tags
    .tag-item
      &.active
        color $accentColor
      &:hover
        color $accentColor
/** 效果一 **/
.draw::before{
  top: 0;
  left: 0;
}
.draw::after{
  bottom: 0;
  right: 0;
}
.draw:hover {
  color: $accentColor;
}
.draw:hover::before, &:hover::after {
  width: 100%;
  height: 100%;
}
.draw:hover::before {
  border-top-color: $accentColor;
  border-right-color: $accentColor;
  transition: width 0.25s ease-out, height 0.25s ease-out 0.25s;
}
.draw:hover::after {
  border-bottom-color: $accentColor;
  border-left-color: $accentColor;
  transition: border-color 0s ease-out 0.5s, width 0.25s ease-out 0.5s, height 0.25s ease-out 0.75s;
}
/** 效果二 **/
.meet:hover {
  color: #60daaa;
}
.meet::after {
  top: 0;
  left: 0;
}
.meet:hover::before {
  border-top-color: #60daaa;
  border-right-color: #60daaa;
}
.meet:hover::after {
  border-bottom-color: #60daaa;
  border-left-color: #60daaa;
  transition: height 0.25s ease-out, width 0.25s ease-out 0.25s;
}

/** 效果三 **/
.center:hover {
  color: #6477b9;
}
.center::before, .center::after {
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  -webkit-transform-origin: center;
  transform-origin: center;
}
.center::before {
  border-top: 2px solid #6477b9;
  border-bottom: 2px solid #6477b9;
  -webkit-transform: scale3d(0, 1, 1);
  transform: scale3d(0, 1, 1);
}
.center::after {
  border-left: 2px solid #6477b9;
  border-right: 2px solid #6477b9;
  -webkit-transform: scale3d(1, 0, 1);
  transform: scale3d(1, 0, 1);
}
.center:hover::before, .center:hover::after {
  -webkit-transform: scale3d(1, 1, 1);
  transform: scale3d(1, 1, 1);
  transition: -webkit-transform 0.5s;
  transition: transform 0.5s;
  transition: transform 0.5s, -webkit-transform 0.5s;
}
@media (max-width: $MQMobile)
  .tags
    display block
    margin-top 1rem;
    margin-left: 0!important;
  .abstract-item .draw {
    width: 100% !important;
  }
</style>
