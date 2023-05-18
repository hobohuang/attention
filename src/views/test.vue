<template>
  <div class="test">
    {{ myData }}
    <select-block v-model="myData"></select-block>
  </div>
</template>
<script>
import selectBlock from "../Components/selectBlock/index.vue";
export default {
  data() {
    return {
      myData: 1,
    };
  },
  components: {
    selectBlock,
  },
  methods: {},
};
</script>

<style scoped></style>
