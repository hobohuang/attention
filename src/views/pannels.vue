<template>
  <div class="pannels">
    <div class="pannels-widgets">
      <h2 class="title">请选择注意力训练模式</h2>
      <div class="grid-content">
        <el-button
          v-for="item in games"
          @click="toAttentionTrain(item['name'])"
          class="btn-background"
          >{{ item.label }}</el-button
        >
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {};
  },
  computed: {
    games() {
      const games = [
        "注意力广度训练",
        "注意力集中性训练",
        "注意力稳定性训练",
        "注意力与大脑抑制功能训练",
        "注意力与反应速度训练",
        "注意力与记忆力训练",
        "注意力与观察力训练",
        "注意力与动作控制训练",
        "注意力与手眼协调训练",
        "脑力训练",
      ];
      const paths = [
        "/span-train",
        "/concentration-train",
        "/stable-train",
        "/inhibition-train",
        "/reaction-train",
        "/memory-train",
        "/JigsawPuzzle-train",
      ];
      const name = [
        "SpanTrain",
        "concentrationTrain",
        "stable",
        "inhibition",
        "reaction",
        "memory",
      ];
      return games.map((item, index) => {
        return {
          label: item,
          name: name[index] || "",
          path: paths[index] || "",
        };
      });
    },
  },
  components: {},
  methods: {
    toAttentionTrain(path) {
      if (path == "") {
        this.$message("该功能暂时未开启");
        return;
      }
      console.log(path);
      this.$router.push({
        name: path,
        params: { ...this.$router.currentRoute.params },
      });
    },
  },
};
</script>
<style scoped>
.pannels {
  width: 100%;
  height: 100%;
  background-color: #fbd5dd;
  display: flex;
  justify-content: center;
  align-items: center;
}
.title {
  font-size: 36px;
  margin-bottom: 32px;
}
.pannels-widgets {
  width: max-content;
}

.grid-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-gap: 40px;
}
.btn-background {
  background-color: #66ccff;
  background-image: url(../assets/btn-img.png);
  background-size: cover;
  width: 350px;
  height: 56px;
  margin-left: 0px;
  color: #3d3d3d;
  font-size: 18px;
  text-align: left;
}

/* .pannels-widgets .el-button {
  width: 250px;
} */
</style>
