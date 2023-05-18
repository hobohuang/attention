<template>
  <div class="memory-card">
    <img class="front-face" draggable="false" :src="front" :alt="`${alt}`" />
    <img
      class="back-face"
      draggable="false"
      :src="require(`./img/js-badge.svg`)"
      alt="memory-card"
    />
  </div>
</template>
<script>
export default {
  name: "CardComponent",
  props: {
    imgPwd: "",
    front: "",
    alt: "",
  },
};
</script>
<style scoped>
.front-face {
  width: 100%;
  height: 100%;
  padding: 20px;
  position: absolute;
  border-radius: 5px;
  background-color: #527089;
  backface-visibility: hidden;
  box-sizing: border-box;
  transform: rotateY(180deg);
}
</style>
