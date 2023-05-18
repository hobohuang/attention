<template>
  <div class="wapper">
    <div class="attention_reaction_train">
      <div class="acction_span-left-right" v-if="!start">
        <div class="acction_span-left">
          <h2 class="title">注意力与反应训练</h2>
          <div class="attention_reaction_train-config">
            <el-form
              :model="formData"
              ref="ruleForm"
              label-width="100px"
              class="demo-ruleForm"
              label-position="top"
            >
              <el-form-item label="等级" prop="count">
                <el-select v-model="formData.level" placeholder="请选择">
                  <el-option label="初级" value="1" key="1"> </el-option>
                  <el-option label="中级" value="2" key="2"> </el-option>
                  <el-option label="高级" value="3" key="3"> </el-option>
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
              <!-- <el-form-item>
                <div>请根据提示，选择对应的颜色图案</div>
              </el-form-item> -->
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

      <div v-if="start">
        <span>用时: {{ timmerShow }}</span>
        <span>选中次数： {{ greenCounter }}</span>
      </div>
      <div v-if="start">
        <span>错误次数: {{ currentTrainResult.errorNumber }}</span>
      </div>
      <div v-if="start">
        <Shape @close="stopShape" :shape="currentShape" :color="currentColor" />
      </div>
      <div class="attention_reaction_train-count" v-if="timeCountShow">
        {{ count }}
      </div>
    </div>
    <img src="../../assets/children.png" class="chidren-img" />
  </div>
</template>

<script>
import { savePationData } from "../../api/index";

import Shape from "./Shape.vue";
export default {
  name: "App",
  data() {
    return {
      count: 3,
      timeCountShow: false,
      formData: {
        level: 1,
        count: 5,
      },
      currentTrainResult: {
        errorNumber: 0,
        time: 0,
      },
      trainResultTotal: [],
      currentCount: 10,
      start: false,
      showShape: false,
      colorArray: ["yellow", "green", "red", "blue"],
      shapeArray: ["circle", "rectangle", "triangular"],
      currentShape: null, //will hold one shape at a time from ShapeArray
      currentColor: null, //will hold one color at a time from colorArray
      clickOnGreenFlag: false,
      showResults: false, // show the end game screen
      intervalShape: null,
      greenCounter: 0, //how many time to player succed
      timer: null,
      timeplayed: 0,
      showIndicator: false,
      showIndicatorInterval: null,
      successIndicator: null, //will chane to sentence according to 'greenCounter'
      highestScore: 0,
    };
  },
  components: {
    Shape,
  },
  computed: {
    timmerShow() {
      const time = parseInt(this.currentTrainResult.time / 1000) || 0;
      const mSecond = this.currentTrainResult.time % 1000 || 0;
      const minute = parseInt(time / 60);
      const second = time % 60 || 0;
      return `${minute}:${second}:${mSecond}`;
    },
    instanceTime() {
      if (this.formData.level == 1) {
        return 1000;
      }

      if (this.formData.level == 2) {
        return 750;
      }

      if (this.formData.level == 3) {
        return 500;
      }
    },
  },
  methods: {
    submit() {
      console.log("this is submit click");
      this.start = true;
      this.timeCount();
      this.trainResultTotal = [];
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
        this.randomShapeAndColor();
      }, 3000);
      this.timmer = setInterval(() => {
        this.count -= 1;
      }, 1000);
    },
    startTimer() {
      this.timer = setInterval(() => {
        this.timeplayed += 20;
      }, 20);
    },
    stopTimer() {
      clearInterval(this.timer);
    },
    randomShapeAndColor() {
      // this.timerDelay = 300 + Math.random() * 400;
      this.startTimer();
      this.intervalShape = setInterval(this.addShape, this.instanceTime);
    },
    addShape() {
      this.currentColor = this.colorArray[Math.floor(Math.random() * 4)];
      this.currentShape = this.shapeArray[Math.floor(Math.random() * 3)];
      this.showResults = false;
      this.showShape = true;
    },
    toggleShowShape() {
      this.showShape = !this.showShape;
    },
    stopShape(clickOnGreen) {
      this.clickOnGreenFlag = clickOnGreen;
      if (this.clickOnGreenFlag) {
        this.showIndicator = true;
        this.showIndicatorInterval = setInterval(this.closeIndicator, 500);
        this.greenCounter++;
        return;
      }
      clearInterval(this.intervalShape);
      this.stopTimer();
      clearInterval(this.timmer);
      // this.successIndicator = this.giveSuccessIndicator(); //getting value for seccess
      // this.timeplayed = this.timeplayed / 1000; //from ms to seconds
      // this.timeplayed = parseFloat(this.timeplayed.toFixed(4)); //only 4 digit decimal
      // this.showShape = false; //stop showing the shapes on the DOM
      // if (this.greenCounter > this.highestScore) {
      //   //need to upload name and score
      //   this.highestScore = this.greenCounter;
      // }
      // this.showResults = true; //showing the end game DOM

      this.trainResultTotal.push({
        pationId: this.$router.currentRoute.params.id,
        mode: "reaction",
        currentTime: +new Date(),
        level: this.formData.level,
        time: this.currentTrainResult.time / 1000,
        errorNumber: this.currentTrainResult.errorNumber,
        goodNumber: this.greenCounter,
      });

      if (this.formData.count == this.trainResultTotal.length) {
        this.endTotalTask();
        return;
      }

      this.reStart();
    },
    async endTotalTask() {
      this.start = false;
      const res = await savePationData(this.trainResultTotal);
      this.$router.go(-1);
    },
    reStart() {
      this.currentTrainResult = {
        time: 0,
        errorNumber: 0,
      };
      this.greenCounter = 0;
      this.timeCount();
    },
    closeIndicator() {
      clearInterval(this.showIndicatorInterval);
      this.showIndicator = false;
    },
    closeEndGame() {
      this.showResults = false;
      this.greenCounter = 0;
      this.endTimer = 0;
    },
    giveSuccessIndicator() {
      //returing sentnce according to the value
      if (this.greenCounter <= 3) return "You snooze you lose";
      else if (this.greenCounter <= 6) return "Congrats on a mediocre job";
      else return "Eternal bragging rights";
    },
  },
};
</script>

<style scoped>
.welcome h1 {
  color: blueviolet;
}

.attention_reaction_train {
  box-sizing: border-box;
  min-height: 600px;
}

.attention_reaction_train-count {
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
  justify-content: space-between;
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
  font-size: 36px;
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
