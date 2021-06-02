<template>
  <div class="demo full">
    <h2>基础用法</h2>
    <tabs>
      <tab tab-title="全部">
        <p class="content">这里是页签全部内容</p>
      </tab>
      <tab tab-title="待付款">
        <p class="content">这里是页签待付款内容</p>
      </tab>
      <tab tab-title="待收货">
        <p class="content">这里是页签待收货内容</p>
      </tab>
      <tab tab-title="已完成">
        <p class="content">这里是页签已完成内容</p>
      </tab>
    </tabs>

    <h2>defaultIndex设置默认显示tab</h2>
    <h2>switchTab监听切换tab返回事件</h2>
    <tabs :default-index="1" @switch-tab="switchTab">
      <tab tab-title="全部"
      ><p class="content">这里是页签全部内容</p></tab
      >
      <tab tab-title="待付款"
      ><p class="content">这里是页签待付款内容</p></tab
      >
      <tab tab-title="待收货"
      ><p class="content">这里是页签待收货内容</p></tab
      >
      <tab tab-title="已完成"
      ><p class="content">这里是页签已完成内容</p></tab
      >
    </tabs>

    <h2
    >标签数量超过 5
      个时，标签栏可以在水平方向上滚动，切换时会自动将当前标签居中。</h2
    >
    <tabs>
      <tab tab-title="全部"
      ><p class="content">这里是页签全部内容</p></tab
      >
      <tab tab-title="待付款"
      ><p class="content">这里是页签待付款内容</p></tab
      >
      <tab tab-title="待收货"
      ><p class="content">这里是页签待收货内容</p></tab
      >
      <tab tab-title="已完成"
      ><p class="content">这里是页签已完成内容</p></tab
      >
      <tab tab-title="已取消"
      ><p class="content">这里是页签已取消内容</p></tab
      >
      <tab tab-title="待评价"
      ><p class="content">这里是页签待评价内容</p></tab
      >
    </tabs>

    <h2>异步操作</h2>
    <tabs v-if="editList.length > 0">
      <tab
          :tab-title="item.title"
          v-for="(item, index) in editList"
          :key="index"
      >
        <p class="content">这里是页签{{ index }}内容</p>
      </tab>
    </tabs>
    <button type="primary" @click="changeList">改变数据</button>
  </div>
</template>

<script lang="ts">
import { reactive, toRefs } from 'vue';
import tabs from './components/tabs.vue';
import tab from './components/tab.vue';
export default {
  name: 'app',
  components: {
    tabs,
    tab
  },
  setup() {
    const resData = reactive({
      editList: [
        {
          title: '标签一'
        },
        {
          title: '标签二'
        },
        {
          title: '标签二'
        }
      ]
    });
    function changeList() {
      resData.editList.push({
        title: '标签' + resData.editList.length
      });
    }
    function switchTab(activeInddex: number) {
      console.log(activeInddex);
    }
    return {
      ...toRefs(resData),
      changeList,
      switchTab
    };
  }
};
</script>

<style lang="scss" scoped>
.content {
  padding: 10px;
}
</style>
