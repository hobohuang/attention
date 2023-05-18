<template>
  <div class="wapper">
    <div class="attention_inhibition-widget">
      <div class="acction_span-left-right" v-if="!start">
        <div class="acction_span-left">
          <h2 class="title">注意力与大脑抑制功能训练</h2>
          <div class="attention_stable-config">
            <el-form
              :model="formData"
              ref="ruleForm"
              label-width="100px"
              class="demo-ruleForm"
              label-position="top"
            >
              <el-form-item label="等级" prop="count">
                <el-select v-model="formData.level" placeholder="请选择">
                  <el-option label="初级" value="easy" key="1"> </el-option>
                  <el-option label="中级" value="normal" key="2"> </el-option>
                  <el-option label="高级" value="hard" key="3"> </el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="次数" prop="count">
                <el-select v-model="formData.count" placeholder="请选择">
                  <el-option
                    v-for="(item, index) in new Array(10).fill(0)"
                    :label="index + 1"
                    :value="index + 1"
                    :key="'key' + index"
                  >
                  </el-option>
                </el-select>
              </el-form-item>
              <el-form-item>
                <div class="btn-wapper">
                  <el-button
                    size="large"
                    @click="$router.go(-1)"
                    class="start-btn"
                  >
                    返回
                  </el-button>
                  <el-button
                    size="large"
                    @click="submit"
                    class="start-btn blue"
                  >
                    开始
                  </el-button>
                </div>
              </el-form-item>
            </el-form>
          </div>
        </div>
        <div class="acction_span_right">
          <div class="acction_span_right-top">
            <div class="green-wrapper">
              <img src="../../assets/top.png" class="arrow-top" />
              <div class="green"></div>
            </div>
            <div class="green-wrapper">
              <img src="../../assets/top.png" class="arrow-bottom" />
              <div class="green padding-lr"></div>
            </div>
            <div class="green-wrapper">
              <img src="../../assets/top.png" class="arrow-top" />
              <div class="green"></div>
            </div>
          </div>
          <div class="acction_span_right-bottom"></div>
        </div>
      </div>
      <div class="acction_span_start" v-if="start">
        <div class="maze-container">
          <Maze
            :strategy="strategy"
            :difficulty="difficulty"
            @start="onStart"
            @finish="onFinish"
            @init="onInit"
            :style="mazeStyle"
          ></Maze>
        </div>
        <div class="choose-info">
          <p>用时: {{ timmerShow }}</p>
          <p>总共： {{ formData.count }}</p>
          <p>剩余： {{ formData.count - trainResultTotal.length }}</p>
        </div>
      </div>
      <div class="attention_inhibition-widget-count" v-if="timeCountShow">
        {{ count }}
      </div>
      <img src="../../assets/children.png" class="chidren-img" />
    </div>
  </div>
</template>

<script>
import Maze from "./Maze.vue";
import { savePationData } from "../../api/index";

// type Difficulty = "easy" | "normal" | "hard";
// type Strategy = "cluster" | "dig";
export default {
  data() {
    return {
      count: 3,
      timeCountShow: false,
      strategy: "dig",
      difficulty: "easy",
      mazeStyle: {
        width: "100%",
        height: "100%",
      },
      formData: {
        level: 1,
        count: 2,
      },
      currentTrainResult: {
        errorNumber: 0,
        time: 0,
      },
      trainResultTotal: [],

      start: false,
    };
  },
  components: {
    Maze,
  },
  mounted() {},
  computed: {
    timmerShow() {
      const time = parseInt(this.currentTrainResult.time / 1000) || 0;
      const mSecond = this.currentTrainResult.time % 1000 || 0;
      const minute = parseInt(time / 60);
      const second = time % 60 || 0;
      return `${minute}:${second}:${mSecond}`;
    },
  },
  methods: {
    submit() {
      this.start = true;
      // this.timeCount();
    },
    onStart() {
      console.log("this is on onStart");
    },
    async endTotalTask() {
      console.log("this is place end totalTask");
      console.log(this.trainResultTotal);
      this.start = false;
      const res = await savePationData(this.trainResultTotal);
      this.$router.go(-1);
    },
    onFinish() {
      console.log("this is on finish");
      this.trainResultTotal.push({
        pationId: this.$router.currentRoute.params.id,
        mode: "inhibition",
        currentTime: +new Date(),
        level: this.difficulty,
        time: this.currentTrainResult.time / 1000,
        errorNumber: this.currentTrainResult.errorNumber,
      });
      (this.currentTrainResult = {
        errorNumber: 0,
        time: 0,
      }),
        clearInterval(this.timmer);
      if (this.trainResultTotal.length < this.formData.count) {
        this.timeCount();
      } else {
        this.endTotalTask();
      }
    },
    onInit() {
      console.log("this is on onInit");
    },
    timerRuning() {
      this.timmer = setInterval(() => {
        this.currentTrainResult.time += 100;
      }, 100);
    },
    timeCount() {
      this.count = 3;
      this.timeCountShow = true;
      clearInterval(this.timmer);
      setTimeout(() => {
        clearInterval(this.timmer);
        this.timeCountShow = false;
        this.timerRuning();
      }, 3000);

      this.timmer = setInterval(() => {
        this.count -= 1;
      }, 1000);
    },
  },
};
</script>
<style scoped>
.attention_inhibition-widget {
  box-sizing: border-box;
}

.maze-container {
  width: 600px;
  height: 600px;
}

.attention_inhibition-widget-count {
  position: fixed;
  z-index: 100;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.2);
  text-align: center;
  top: 0;
  left: 0;
  color: red;
  line-height: 500px;
  font-size: 100px;
}
/* 

新增css

*/
.wapper {
  width: 100%;
  height: 100%;
  background: #fbd5dd;
  display: flex;
  justify-content: center;
  align-items: center;
}
.green-wrapper {
  height: max-content;
  width: max-content;
  position: relative;
}
.arrow-top {
  position: absolute;
  top: -5px;
  left: 20px;
  height: 80px;
  width: 80px;
  z-index: 0;
}
.arrow-bottom {
  position: absolute;
  bottom: -5px;
  left: 25px;
  height: 80px;
  width: 80px;
  z-index: 0;
  display: inline-block;
  transform: rotate(180deg);
}
.green {
  position: inherit;
  top: 0;
  left: 0;
  height: 152px;
  width: 104px;
  background-color: #b4da85;
  border-radius: 6px;
  border: #fff solid 8px;
  margin: 45px 0;
  z-index: 1;
}
.padding-lr {
  margin-left: 10px;
  margin-right: 10px;
}
.acction_span_right-top {
  display: flex;
  margin-bottom: 20px;
}
.acction_span_right-bottom {
  width: 380px;
  height: 255px;
  background: #ffffff;
  border-radius: 6px 6px 6px 6px;
  opacity: 1;
}
.chidren-img {
  position: fixed;
  left: 330px;
  bottom: -30px;
}
.title {
  font-size: 28px;
  color: #262626;
  margin-bottom: 30px;
}
::v-deep .el-form-item__label {
  color: #3d3d3d !important;
  font-size: 18px !important;
}
.acction_span-left-right {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
.btn-wapper {
  margin-top: 20px;
}
.start-btn {
  width: 156px;
  height: 56px;
}
.blue {
  color: #fff;
  background-color: #2868c9;
}
.el-select {
  width: 336px;
}
.el-button + .el-button,
.el-checkbox.is-bordered + .el-checkbox.is-bordered {
  margin-left: 25px !important;
}
</style>
