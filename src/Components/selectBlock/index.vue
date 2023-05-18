<template>
  <div class="slect-block">
    <div
      v-for="n in 3"
      :key="n"
      @click="select(n)"
      class="block-item"
      :class="{ active: selected === n }"
    >
      {{ n }}级
    </div>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      selected: this.value,
    };
  },
  methods: {
    select(n) {
      this.selected = n;
      this.$emit("input", n);
    },
  },
  watch: {
    value(val) {
      this.selected = val;
    },
  },
};
</script>

<style scoped>
.slect-block {
  width: 336px;
  height: 64px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.block-item {
  width: 33.3%;
  height: 100%;
  line-height: 64px;
  text-align: center;
  color: #000000;
  background-color: #f3f3f3;
}
.active {
  /* 激活样式 */
  background-color: #66ccff;
}
</style>
