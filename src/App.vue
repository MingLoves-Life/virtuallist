<template>
  <div id="app">
    <div class="container" ref="container" @scroll="scrollList">
      <div class="scrollBar-box" :style="{ height: `${listHeight}px` }"></div>
      <div class="list-box" :style="{ transform }">
        <div
          class="list-item"
          v-for="data in visibleData"
          :key="data"
          :style="{ height: listItemSize + 'px' }"
        >
          {{ data }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
const container = ref(null);
const dataList = ref([]);
const startIndex = ref(0);
const endIndex = ref(0);
const startOffset = ref(0);
const listItemSize = ref(100);
const screenHeight = ref(0);

//列表总高度
const listHeight = computed(() => dataList.value.length * listItemSize.value);

// 可显示列数
const visibleCount = computed(() =>
  Math.ceil(screenHeight.value / listItemSize.value)
);

// 偏移量对应的style
const transform = computed(() => `translate3d(0,${startOffset.value}px,0)`);

// 可显示数据
const visibleData = computed(() =>
  dataList.value.slice(
    startIndex.value,
    Math.min(endIndex.value, dataList.value.length)
  )
);

const getData = () =>
  (dataList.value = new Array(1000).fill(0).map((_, index) => index + 1));

const scrollList = () => {
  let scrollTop = container.value.scrollTop;
  startIndex.value = Math.floor(scrollTop / listItemSize.value);
  endIndex.value = startIndex.value + visibleCount.value;
  // list-box跟着滚动条向下走，每走过一格向下移动一个单位
  startOffset.value = scrollTop - (scrollTop % listItemSize.value);
};
onMounted(() => {
  getData();
  screenHeight.value = window.screen.availHeight;
  endIndex.value = startIndex.value + visibleCount.value;
});
</script>

<style>
html {
  height: 100%;
}
body {
  height: 100%;
  margin: 0;
}
#app {
  height: 100%;
}
.container {
  height: 100%;
  overflow: auto;
  position: relative;
}
.scrollBar-box {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  z-index: -1;
}
.list-box {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
}
.list-item {
  width: 100%;
  border: 1px solid black;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
