<template>
  <div :class="classObj" class="app-wrapper">
    <div v-if="device==='mobile'&&sidebar.opened" class="drawer-bg" @click="handleClickOutside" />
    <sidebar class="sidebar-container" />
    <div :class="{hasTagsView:needTagsView}" class="main-container">
      <div :class="{'fixed-header':fixedHeader}">
        <navbar />
        <tags-view v-if="needTagsView" />
      </div>
      <app-main />
      <right-panel v-if="showSettings">
        <settings />
      </right-panel>
    </div>
  </div>
</template>

<script>
import RightPanel from '@/components/RightPanel'
import { Navbar, Sidebar, AppMain, TagsView, Settings } from './components'
import ResizeMixin from './mixin/ResizeHandler'
import { mapState } from 'vuex'

export default {
  name: 'Layout',
  components: {
    RightPanel,
    Navbar,
    Sidebar,
    AppMain,
    TagsView,
    Settings
  },
  mixins: [ResizeMixin],
  computed: {
    ...mapState({
      // 边缘工具条
      sidebar: state => state.app.sidebar,
      // device: 'desktop',
      device: state => state.app.device,
      // 是否展示右边的仪表盘 就是页面右边的设置框
      showSettings: state => state.settings.showSettings,
      // 是否标记视图 就是页面的tab标签 默认为true
      needTagsView: state => state.settings.tagsView,
      // 是否固定头 不知道什么作用
      fixedHeader: state => state.settings.fixedHeader
    }),
    classObj() {
      return {
        // 打开/隐藏工具条
        hideSidebar: !this.sidebar.opened,
        openSidebar: this.sidebar.opened,
        // 工具条是否带有动画 默认为false
        withoutAnimation: this.sidebar.withoutAnimation,
        // 移动端还是pc端
        mobile: this.device === 'mobile'
      }
    }
  },
  methods: {
    handleClickOutside() {
      // 调用store里app.js的mutations里边的方法
      this.$store.dispatch('app/closeSideBar', { withoutAnimation: false })
    }
  }
}
</script>

<style lang="scss" scoped>
  @import "~@/styles/mixin.scss";
  @import "~@/styles/variables.scss";

  .app-wrapper {
    @include clearfix;
    position: relative;
    height: 100%;
    width: 100%;

    &.mobile.openSidebar {
      position: fixed;
      top: 0;
    }
  }

  .drawer-bg {
    background: #000;
    opacity: 0.3;
    width: 100%;
    top: 0;
    height: 100%;
    position: absolute;
    z-index: 999;
  }

  .fixed-header {
    position: fixed;
    top: 0;
    right: 0;
    z-index: 9;
    width: calc(100% - #{$sideBarWidth});
    transition: width 0.28s;
  }

  .hideSidebar .fixed-header {
    width: calc(100% - 54px)
  }

  .mobile .fixed-header {
    width: 100%;
  }
</style>
