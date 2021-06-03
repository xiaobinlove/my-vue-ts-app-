<template>
  <view class="tabs">
    <view class="tabs__title" ref="navlist">
      <view
        :class="['tabs__title-item', { 'tabs__title-item--active': activeIndex == index }]"
        v-for="(item, index) in titles"
        :key="index"
        ref="titleItem"
        @click="switchTitle(index)"
      >
        {{ item.title }}
      </view>
    </view>
    <view class="tabs__swiper" ref="tabDom">
      <view class="tabs__swiper-wrapper" :style="{ transform: `translate3d(-${translateX}px, 0px, 0px)`}">
        <slot></slot>
      </view>
    </view>
  </view>
</template>
<script lang="ts">
/**
 * tabs 标签
 * @description 该组件，是一个tabs标签组件，在标签多的时候，可以配置为左右滑动，标签少的时候，可以禁止滑动。 该组件的一个特点是配置为滚动模式时，激活的tab会自动移动到组件的中间位置。
 * @property {String Number} tabHeight 导航栏的高度，
 * @property {String Number} font-size tab文字大小，
 * @property {String} active-color 滑块和激活tab文字的颜色（默认#2979ff）
 * @property {String} inactive-color tabs文字颜色（默认#303133）
 * @property {String} bg-color tabs导航栏的背景颜色（默认#ffffff）
 * @property {Boolean} defaultIndex 激活选项的字体是否加粗（默认true）
 * @event {Function} clickItem 点击标签时触发
 * @example <u-tabs ref="tabs" :items="items" :is-scroll="false"></u-tabs>
 */
import { reactive, ref, onMounted, watch, VNode } from 'vue';
interface DataTitle {
  title?: string;
}
export default {
  name: 'tabs',
  props: {
    defaultIndex: { // 默认激活index
      type: Number,
      default: 0
    }
  },
  setup(props, ctx) {
    const titles: Array<DataTitle> = reactive([]);
    const activeIndex = ref(props.defaultIndex);
    let translateX = ref(0);
    const navlist = ref<null | HTMLElement>(null);
    const tabDom = ref<null | HTMLElement>(null);
    const titleItem = ref<null | HTMLElement>(null);
    onMounted(() => {
      initTitle();
      translateX.value = activeIndex.value * tabDom.value.offsetWidth;
    });
    watch(() => (ctx.slots.default ? ctx.slots.default() : ''), () => { initTitle(); }
    );
    // title点击后居中显示
    function centerTitle(index: number) {
      if (navlist.value) {
        const currEle = navlist.value.querySelectorAll('.tabs__title-item')[index] as HTMLElement;
        const currLeft = currEle.offsetLeft;
        const currWidth = currEle.offsetWidth;
        const tabsWidth = navlist.value.offsetWidth;
        navlist.value.scroll(currLeft - tabsWidth / 2 + currWidth / 2, 0);
      }
    }
    // 切换tab
    function switchTitle(index: number) {
      activeIndex.value = index;
      translateX.value = index * tabDom.value.offsetWidth;
      // title点击后居中显示
      centerTitle(index);
      ctx.emit('clickItem', index)
    }
    function initTitle() {
      titles.length = 0;
      if (ctx.slots.default) {
        const slots: VNode[] = ctx.slots.default().length === 1
              ? (ctx.slots.default()[0].children as VNode[])
              : ctx.slots.default();
        slots && slots.forEach((item) => {
          titles.push({
            title: item.props && item.props['title'] ? item.props['title'] : ''
          });
        });
      }
    }
    return {
      titles,
      navlist,
      activeIndex,
      switchTitle,
      tabDom,
      titleItem,
      translateX
    };
  }
};
</script>

<style lang="scss">
.tabs {
  &__title {
    width: 100%;
    height: 46px;
    overflow-x: scroll;
    display: flex;
    flex-wrap: nowrap;
    scroll-behavior: smooth;
    background: #f5f5f5;
    position: relative;
    &::-webkit-scrollbar {
      display: none;
    }
  }
  &__title-item {
    min-width: 75px;
    height: 100%;
    display: flex;
    flex: 1;
    justify-content: center;
    align-items: center;
    box-sizing: border-box;
    text-align: center;
    font-size: 14px;
    &--active {
      color: #1a1a1a;
      font-weight: bold;
      font-size: 16px;
      position: relative;
      &::after {
        content: '';
        position: absolute;
        bottom: 5px;
        left: 50%;
        transform: translateX(-50%);
        width: 24px;
        height: 3px;
        background-color: red;
        background-size: 100% 100%;
      }
    }
  }
  &__swiper {
    overflow: hidden;
    display: block;
    width: 100%;
    height: 200px;
    background: #fff;
    box-sizing: border-box;
  }
  &__swiper-wrapper {
    position: relative;
    width: 100%;
    height: 100%;
    z-index: 1;
    display: flex;
    transition-property: transform;
    box-sizing: content-box;
    transition-duration: 0ms;
  }
}
</style>
