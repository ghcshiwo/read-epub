<template>
  <div class="ebook">
    <title-bar :isShow="isShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar :isShow="isShow"
              :fontSizeList="fontSizeList"
              :defaultFontSize="defaultFontSize"
              :themeList="themeList"
              :defaultTheme="defaultTheme"
              :bookAvailable="bookAvailable"
              :navigation="navigation"
              :progress="progress"
              @setFontSize="setFontSize"
              @setTheme="setTheme"
              @onProgressChange="onProgressChange"
              @jumpTo="jumpTo"
              ref="menuBar"></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
import Epub from 'epubjs'
// const DOWNLOAD_URL = '/static/66485.epub'
const DOWNLOAD_URL = '/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
export default {
  data () {
    return {
      isShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      defaultFontSize: 14,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000', 'background': '#fff'
            }
          }
        }, {
          name: 'eye',
          style: {
            body: {
              'color': '#000', 'background': '#ceeaba'
            }
          }
        }, {
          name: 'night',
          style: {
            body: {
              'color': '#fff', 'background': '#000'
            }
          }
        }, {
          name: 'gold',
          style: {
            body: {
              'color': '#000', 'background': 'rgb(241,236,226)'
            }
          }
        }
      ],
      defaultTheme: 0,
      bookAvailable: false,
      navigation: null,
      progress: 0
    }
  },
  components: {
    TitleBar, MenuBar
  },
  mounted () {
    this.showEpub()
  },
  methods: {
    /**
     * 电子书的解析和渲染
     */
    showEpub () {
      // 生成book对象
      this.book = new Epub(DOWNLOAD_URL)
      // 生成Rendition对象
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })
      // 通过Rendition.display渲染电子书
      this.rendition.display()
      // 获取Theme对象
      this.theme = this.rendition.themes
      // 设置默认字号
      this.setFontSize(this.defaultFontSize)
      // this.theme.register(name, style)
      this.registerTheme()
      // this.theme.select(name)
      this.setTheme(this.defaultTheme)
      // 获取Location对象
      this.book.ready.then(() => {
        this.navigation = this.book.navigation
        return this.book.locations.generate()
      }).then(result => {
        this.locations = this.book.locations
        this.bookAvailable = true
      })
    },
    /**
     * 上一页
     */
    prevPage () {
      // Rendition.prev
      if (this.rendition) {
        this.rendition.prev()
      }
      this.progress = parseInt(this.rendition.currentLocation().start.percentage * 100)
    },
    /**
     * 下一页
     */
    nextPage () {
      // Rendition.next
      if (this.rendition) {
        this.rendition.next()
      }
      // percentageFromCfi(cfi)
      this.progress = parseInt(this.rendition.currentLocation().start.percentage * 100)
    },
    toggleMenu () {
      this.isShow = !this.isShow
      if (!this.isShow) {
        this.$refs['menuBar'].hideSetting()
      }
    },
    setFontSize (fontSize) {
      this.defaultFontSize = fontSize
      if (this.theme) {
        this.theme.fontSize(fontSize + 'px')
      }
    },
    registerTheme () {
      this.themeList.forEach(theme => {
        this.theme.register(theme.name, theme.style)
      })
    },
    setTheme (index) {
      this.theme.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    onProgressChange (progress) {
      const per = progress / 100
      const location = per > 0 ? this.locations.cfiFromPercentage(per) : 0
      this.rendition.display(location)
    },
    jumpTo (href) {
      this.rendition.display(href).then(section => {
        this.progress = parseInt(this.rendition.currentLocation().start.percentage * 100)
      })
      this.hideMenu()
    },
    hideMenu () {
      this.isShow = false
      this.$refs.menuBar.hideSetting()
      this.$refs.menuBar.hideContent()
    }
  }
}
</script>

<style lang='scss' scoped>
@import 'assets/styles/global';
.ebook {
  position: relative;
  .read-wrapper {
    position: relative;
    .mask{
      position: absolute;
      top: 0;
      left: 0;
      display: flex;
      width: 100%;
      height: 100%;
      .left{
        flex: 0 0 px2rem(100);
      }
      .center{
        flex: 1;
      }
      .right{
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>
