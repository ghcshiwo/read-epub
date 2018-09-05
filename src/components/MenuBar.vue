<template>
  <div class="menu-bar">
    <transition name='slide-up'>
      <div v-show="isShow" class="menu-wrapper" :class="{'hide-box-shadow': isSettingShow || !isShow}">
        <div class="icon-wrapper">
          <span class="icon-menu icon" @click="showSetting(3)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-progress icon" @click="showSetting(2)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-bright icon" @click="showSetting(1)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-a icon" @click="showSetting(0)">A</span>
        </div>
      </div>
    </transition>
    <transition name='slide-up'>
      <div v-show="isSettingShow" class="setting-wrapper">
        <div class="setting-theme" v-if="showTag === 3">
          <div class="setting-theme-item" v-for="(item, index) in themeList" :key="index" @click="setTheme(index)">
            <div class="preview" :class="{'no-border': item.style.body.background !== '#fff'}" :style="{background: item.style.body.background}"></div>
            <div class="text" :class="{'selected': index === defaultTheme}">{{item.name}}</div>
          </div>
        </div>
        <div class="setting-progress" v-if="showTag === 2">
          <div class="progress-wrapper">
            <input class="progress" type="range"
                                    max="100"
                                    min="0"
                                    step="1"
                                    @change="onProgressChange($event.target.value)"
                                    @input="onProgressInput($event.target.value)"
                                    :value="progress"
                                    :disabled="!bookAvailable"
                                    ref="progress">
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailable ? progress + '%' : '加载中...'}}</span>
          </div>
        </div>
        <div class="setting-font-size" v-if="showTag === 0">
          <div class="preview" :style="{fontSize: fontSizeList[0].fontSize + 'px'}">A</div>
          <div class="select">
            <div class="select-wrapper" v-for="(item, index) in fontSizeList" :key="index" @click="setFontSize(item.fontSize)">
              <div class="line"></div>
              <div class="point-wrapper">
                <div v-show="defaultFontSize === item.fontSize" class="point">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div class="preview" :style="{fontSize: fontSizeList[fontSizeList.length - 1].fontSize + 'px'}">A</div>
        </div>
      </div>
    </transition>
    <transition name='fade'>
      <div>
        <ContentView :isContentShow="isContentShow"
        v-show="isContentShow"
        :navigation="navigation"
        :bookAvailable="bookAvailable"
        @jumpTo="jumpTo"></ContentView>
        <div class="content-mask" v-show="isContentShow" @click="hideContent">
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import ContentView from '@/components/Content'
export default {
  props: {
    isShow: Boolean,
    fontSizeList: Array,
    defaultFontSize: Number,
    themeList: Array,
    defaultTheme: Number,
    bookAvailable: Boolean,
    navigation: Object,
    progress: Number
  },
  data () {
    return {
      isSettingShow: false,
      showTag: 0,
      isContentShow: false
    }
  },
  components: {
    ContentView
  },
  methods: {
    setTheme (index) {
      this.$emit('setTheme', index)
    },
    setFontSize (fontSize) {
      this.$emit('setFontSize', fontSize)
    },
    showSetting (index) {
      this.showTag = index
      if (this.showTag === 3) {
        this.isSettingShow = false
        this.isContentShow = true
      } else {
        this.isSettingShow = true
      }
    },
    hideSetting () {
      this.isSettingShow = false
    },
    hideContent () {
      this.isContentShow = false
    },
    onProgressInput (progress) {
      this.progress = progress
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    onProgressChange (progress) {
      this.$emit('onProgressChange', progress)
    },
    jumpTo (target) {
      this.$emit('jumpTo', target)
    }
  }
}
</script>

<style lang='scss' scoped>
@import '../assets/styles/global';
.menu-bar {
  .menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: px2rem(48);
    background: #fff;
    box-shadow: 0 px2rem(-2) px2rem(8) rgba(0,0,0,.15);
    &.hide-box-shadow {
      box-shadow: none;
    }
    .icon-wrapper{
      flex: 1;
      @include center;
    }
  }
  .setting-wrapper {
    z-index: 102;
    position: absolute;
    bottom: px2rem(48);
    left: 0;
    width: 100%;
    height: px2rem(60);
    background: #fff;
    box-shadow: 0 px2rem(-2) px2rem(2) rgba(0,0,0,.15);
    .setting-font-size {
      display: flex;
      height: 100%;
      .preview {
        flex: 0 0 px2rem(40);
        @include center;
      }
      .select{
        flex: 1;
        display: flex;
        .select-wrapper {
          flex: 1;
          display: flex;
          align-items: center;
          &:first-child {
            .line{
              &:first-child {
                border-top: none;
              }
            }
          }
          &:last-child {
            .line{
              &:last-child {
                border-top: none;
              }
            }
          }
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid #ccc;
          }
          .point-wrapper{
            position: relative;
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(1) solid #ccc;
            .point{
              position: absolute;
              top: 0;
              left: 0;
              height: px2rem(20);
              width: px2rem(20);
              background: #fff;
              margin-left: px2rem(-10);
              margin-top: px2rem(-8);
              border: #ccc px2rem(1) solid;
              border-radius: 50%;
              box-shadow: 0 px2rem(4) px2rem(4) rgba(0,0,0,.15);
              @include center;
              .small-point{
                width: px2rem(10);
                height: px2rem(10);
                background: #000;
                border-radius: 50%;
              }
            }
          }
        }
      }
    }
    .setting-theme{
      height: 100%;
      display: flex;
      .setting-theme-item{
        flex: 1;
        display: flex;
        flex-direction: column;
        padding: px2rem(5);
        box-sizing: border-box;
        .preview{
          flex: 1;
          border: px2rem(1) solid #ccc;
          box-sizing: border-box;
          &.no-border{
            border: none;
          }
        }
        .text{
          flex: 0 0 px2rem(20);
          font-size: px2rem(14);
          color: #ccc;
          @include center;
          &.selected {
            color: #333;
          }
        }
      }
    }
    .setting-progress{
      position: relative;
      width: 100%;
      height: 100%;
      .progress-wrapper{
        width: 100%;
        height: 100%;
        @include center;
        padding: 0 px2rem(30);
        box-sizing: border-box;
        .progress{
          width: 100%;
          -webkit-appearance: none;
          height: px2rem(2);
          background: -webkit-linear-gradient(#999,#999) no-repeat #ddd;
          background-size: 0 100%;
          &:focus{
            outline: none;
          }
          &::-webkit-slider-thumb{
            -webkit-appearance: none;
            height: px2rem(20);
            width: px2rem(20);
            border-radius: 50%;
            background: #ffffff;
            box-shadow: 0 px2rem(4px) px2rem(4px) 0 rgba(0,0,0,.15);
            border: px2rem(1) solid #ddd;
          }
        }
      }
      .text-wrapper{
        font-size: px2rem(12);
        text-align: center;
        margin-top: px2rem(-12);
        color: #999;
      }
    }
  }
  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 103;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(51,51,51,.8);
  }
}
</style>
