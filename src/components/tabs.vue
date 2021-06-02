<template>
  <view class="tab">
    <view class="tab-title" ref="navlist">
      <view
          :class="['tab-title-box', { 'tab-active': activeIndex == index }]"
          v-for="(item, index) in titles"
          :key="index"
          @click="switchTitle(index, $event)"
      >
        {{ item.title }}
        <TabTitle v-bind:slots="item.content" v-if="item.content"></TabTitle>
      </view>
    </view>
    <view class="tab-swiper" ref="tabDom">
      <view class="swiper-wrapper" :style="{ transform: `translate3d(-${translateX}px, 0px, 0px)`}">
        <slot></slot>
      </view>
    </view>
  </view>
</template>
<script lang="ts">
import { reactive, ref, onMounted, watch, VNode } from 'vue';
import TabTitle from './tabTitle';

interface DataTitle {
  title?: string;
  content?: VNode[];
}

type currChild = {
  header: Function;
} & VNode[];

export default {
  name: 'tabs',
  components: {
    TabTitle
  },
  props: {
    defaultIndex: {
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
    //title点击后居中显示
    function centerTitle(index: number) {
      if (navlist.value) {
        const currEle = navlist.value.querySelectorAll('.tab-title-box')[
            index
            ] as HTMLElement;
        const currLeft = currEle.offsetLeft;
        const currWidth = currEle.offsetWidth;
        const tapWidth = navlist.value.offsetWidth;
        navlist.value.scroll(currLeft - tapWidth / 2 + currWidth / 2, 0);
      }
    }
    //切换tab
    function switchTitle(index: number) {
      activeIndex.value = index;
      centerTitle(index);
      translateX.value = index * tabDom.value.offsetWidth;
    }
    function initTitle() {
      titles.length = 0;
      if (ctx.slots.default) {
        const slots: VNode[] =
            ctx.slots.default().length === 1
                ? (ctx.slots.default()[0].children as VNode[])
                : ctx.slots.default();
        slots &&
        slots.map((item, index) => {
          if (typeof item.children == 'string') return;
          titles.push({
            title:
                item.props && item.props['tab-title']
                    ? item.props['tab-title']
                    : '',
            content:
                item.children && (item.children as currChild).header
                    ? (item.children as currChild).header()
                    : null
          });
        });
      }
    }
    onMounted(() => {
      initTitle();
      translateX.value = activeIndex.value * tabDom.value.offsetWidth;
    });
    watch(
        () => (ctx.slots.default ? ctx.slots.default() : ''),
        () => {
          initTitle();
        }
    );
    return {
      titles,
      navlist,
      activeIndex,
      switchTitle,
      tabDom,
      translateX
    };
  }
};
</script>

<style lang="scss">
.tab {
  .tab-title {
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
    .tab-title-box {
      min-width: 75px;
      height: 100%;
      display: flex;
      flex: 1;
      justify-content: center;
      align-items: center;
      box-sizing: border-box;
      text-align: center;
      font-size: 14px;
    }
    .tab-active {
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
  .tab-swiper {
    overflow: hidden;
    display: block;
    width: 100%;
    height: 200px;
    background: #fff;
    box-sizing: border-box;
  }
  .swiper-wrapper {
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
