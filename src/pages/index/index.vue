<template>
  <virtual-waterfall
    unlimitedSize
    class="List"
    :height="500"
    :item-data="list"
    :item-count="dataLen"
    :item-size="itemHeight"
    :item="Row"
    width="100%"
    @scroll="onScroll"
  />
</template>

<script>
import { markRaw } from "vue";
import Row from "./Row.vue";

function buildData(offset = 0) {
  return Array(100)
    .fill(0)
    .map((_, i) => i + offset);
}

export default {
  data() {
    return {
      Row: markRaw(Row),
      list: buildData(0),
      loading: false,
      itemHeight: 100,
    };
  },
  computed: {
    dataLen() {
      return this.list.length;
    },
  },
  methods: {
    listReachBottom() {
      Taro.showLoading();
      this.loading = true;
      setTimeout(() => {
        const { data } = this.state;
        this.data = this.data.concat(buildData(data.length));
        this.loading = false;
        Taro.hideLoading();
        // this.setState(
        //   {
        //     data: data.concat(buildData(data.length)),
        //   },
        //   () => {
        //     this.loading = false;
        //     Taro.hideLoading();
        //   }
        // );
      }, 1000);
    },
    onScroll({ scrollDirection, scrollOffset }) {
      if (
        // 避免重复加载数据
        !this.loading &&
        // 只有往前滚动我们才触发
        scrollDirection === "forward" &&
        // 5 = (列表高度 / 单项列表高度)
        // 100 = 滚动提前加载量，可根据样式情况调整
        scrollOffset > (this.dataLen - 5) * this.itemHeight + 100
      ) {
        this.listReachBottom();
      }
    },
  },
};
</script>
