<template>
  <view class="tabs">
    <view class="tabs__title" ref="navlist">
      <view
          :class="['tabs__title-item', { 'tabs__title-item--active': activeIndex == index }]"
          v-for="(item, index) in titles"
          :key="index"
          ref="titleItem"
          @click="switchTitle(index, $event)"
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
      ctx.emit('switchTab', index)
    }
    function initTitle() {
      titles.length = 0;
      if (ctx.slots.default) {
        const slots: VNode[] = ctx.slots.default().length === 1
              ? (ctx.slots.default()[0].children as VNode[])
              : ctx.slots.default();
        slots && slots.map((item) => {
          titles.push({
            title: item.props && item.props['tab-title'] ? item.props['tab-title'] : ''
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
