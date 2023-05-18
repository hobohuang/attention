<template>
  <div class="wapper">
    <div class="attention_span_train-widget">
      <div class="acction_span-left-right" v-if="!start">
        <div class="acction_span-left">
          <h2 class="title">注意力广度训练</h2>
          <div class="attention_span_train-config">
            <el-form
              :model="formData"
              ref="ruleForm"
              label-width="100px"
              class="demo-ruleForm"
              label-position="top"
            >
              <el-form-item label="等级" prop="count">
                <!-- <el-select v-model="formData.level" placeholder="请选择等级">
                  <el-option label="初级" value="1" key="1"> </el-option>
                  <el-option label="中级" value="2" key="2"> </el-option>
                  <el-option label="高级" value="3" key="3"> </el-option>
                </el-select> -->
                <select-block v-model="formData.level"></select-block>
              </el-form-item>

              <el-form-item label="次数" prop="count">
                <el-select
                  v-model="formData.count"
                  placeholder="请选择次数"
                  size="medium"
                >
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
                <el-button
                  size="large"
                  @click="$router.go(-1)"
                  class="start-btn"
                >
                  返回
                </el-button>
                <el-button size="large" @click="submit" class="start-btn blue">
                  开始
                </el-button>
              </el-form-item>
              <el-form-item>
                <div class="tips">
                  <p class="tips-title">训练说明</p>
                  <p>训练时，练习者用鼠标按1，2，3，4，5，6</p>
                  <p>的顺序依次点击其位置，每次训练至少5次以上，</p>
                  <p>用时越短则说明注意力水平越高，当训练成绩</p>
                  <p>连续5天都无法得到提升，请调高训练级别。</p>
                </div>
              </el-form-item>
            </el-form>
          </div>
        </div>
        <div class="acction_span_right">
          <div class="acction_span_right-top">
            <div class="green-wrapper">
              <img src="../assets/top.png" class="arrow-top" />
              <div class="green"></div>
            </div>
            <div class="green-wrapper">
              <img src="../assets/top.png" class="arrow-bottom" />
              <div class="green padding-lr"></div>
            </div>
            <div class="green-wrapper">
              <img src="../assets/top.png" class="arrow-top" />
              <div class="green"></div>
            </div>
          </div>
          <div class="acction_span_right-bottom"></div>
        </div>
      </div>
      <div class="acction-span-start" v-if="start">
        <el-button size="normal" @click="$router.go(0)" class="goback-btn">
          返回
        </el-button>
        <div class="attention_span_train-content">
          <div
            v-for="item in cards"
            :class="['card', rightArray.indexOf(item) >= 0 ? 'active' : '']"
            :style="cardStyle"
            @click="choose(item)"
          >
            {{ item }}
          </div>
        </div>
        <div class="plate-chart">
          <div class="display-plate">
            <p class="plate-font">用时： {{ timmerShow }}</p>
            <p class="plate-font">
              次数：
              <span style="color: #2868c9">{{ trainResultTotal.length }}</span>
              /
              {{ formData.count }}
            </p>
            <p class="plate-font">
              错误次数：
              <span style="color: #ea0000">{{
                currentTrainResult.errorNumber
              }}</span>
            </p>
          </div>
          <div class="start_span_right">
            <div class="start_span_right-top">
              <div class="start-green-wrapper">
                <img src="../assets/top.png" class="start-arrow-top" />
                <div class="start-green"></div>
              </div>
              <div class="start-green-wrapper">
                <img src="../assets/top.png" class="start-arrow-bottom" />
                <div class="start-green"></div>
              </div>
              <div class="start-green-wrapper">
                <img src="../assets/top.png" class="start-arrow-top" />
                <div class="start-green"></div>
              </div>
            </div>
            <div class="start_span_right-bottom"></div>
          </div>
        </div>
      </div>
      <div class="attention_span_train-time-count" v-if="timeCountShow">
        {{ count }}
      </div>
    </div>
    <img src="../assets/children.png" class="chidren-img" />
  </div>
</template>

<script>
import { savePationData } from "../api/index";
import selectBlock from "../Components/selectBlock/index.vue";

export default {
  data() {
    return {
      time: "00: 00: 00",
      start: false,
      currentIndex: 1,
      rightArray: [],
      cards: [],
      timeCountShow: false,
      count: 3,
      formData: {
        level: 1,
        count: 3,
      },
      currentTrainResult: {
        errorNumber: 0,
        time: 0,
      },
      trainResultTotal: [],
    };
  },
  components: {
    selectBlock,
  },
  computed: {
    numberCards() {
      if (this.formData.level == 1) {
        return 5;
      }

      if (this.formData.level == 2) {
        return 7;
      }

      if (this.formData.level == 3) {
        return 10;
      }

      if (this.formData.level == 4) {
        return 15;
      }
    },

    cardStyle() {
      let number = parseInt(600 / Math.sqrt(this.cards.length));
      return `width:${number}px;height:${number}px; line-height: ${number}px;`;
    },

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
      this.cards = new Array(this.numberCards * this.numberCards)
        .fill(15)
        .map((item, index) => index + 1)
        .sort(() => Math.random() - 0.5);
      this.timeCount();
    },
    goBack() {
      this.$router.go(-1);
    },
    async endTotalTask() {
      this.start = false;
      console.log(this.trainResultTotal);
      // this is place to upload the total result
      const res = await savePationData(this.trainResultTotal);
      this.$router.go(-1);
    },

    timerRuning() {
      this.timmer = setInterval(() => {
        this.currentTrainResult.time += 100;
      }, 100);
    },
    reStart() {
      if (this.timmer) {
        clearInterval(this.timmer);
      }
      this.currentTrainResult = {
        time: 0,
        errorNumber: 0,
      };
      this.currentIndex = 1;
      this.rightArray = [];
      this.cards = new Array(this.numberCards * this.numberCards)
        .fill(15)
        .map((item, index) => index + 1)
        .sort(() => Math.random() - 0.5);
      this.timeCount();
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
    choose(item) {
      if (this.currentIndex == item) {
        this.rightArray.push(item);
        this.currentIndex += 1;
        if (this.numberCards * this.numberCards == this.rightArray.length) {
          clearInterval(this.timmer);
          this.trainResultTotal.push({
            pationId: this.$router.currentRoute.params.id,
            mode: "span",
            currentTime: +new Date(),
            level: this.formData.level,
            time: this.currentTrainResult.time / 1000,
            errorNumber: this.currentTrainResult.errorNumber,
          });
          if (this.trainResultTotal.length >= this.formData.count) {
            this.endTotalTask();
            return;
          }
          this.reStart();
        }
        return;
      }
      this.currentTrainResult.errorNumber += 1;
    },
  },
  mounted() {
    console.log(this.$router.currentRoute.params);
  },
};
</script>

<style scoped>
.wapper {
  width: 100%;
  height: 100%;
  background: #fbd5dd;
  display: flex;
  justify-content: center;
  align-items: center;
}

.attention_span_train-widget {
  box-sizing: border-box;
}

.attention_span_train-content {
  width: 600px;
  height: 600px;
  /* border-right: 1px solid #ddd;
  border-bottom: 1px solid #ddd; */
  background-color: #fff;
  border: 17px solid #f3a4b4;
  border-radius: 12px;
}

.card {
  display: inline-block;
  text-align: center;
  /* border-left: 1px solid #ddd;
  border-top: 1px solid #ddd; */
  box-sizing: border-box;
  cursor: pointer;
}

.card.active {
  background: gray;
}

.card:hover {
  background: #ddd;
}
.attention_span_train-time-count {
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
.tips {
  color: #3d3d3d;
  background: #fff;
  width: 336px;
  padding: 16px;
  box-sizing: border-box;
  border-radius: 6px;
}
.tips p {
  font-size: 12px;
  height: 24px;
  line-height: 24px;
}
.tips-title {
  font-size: 18px !important;
  font-family: HarmonyOS Sans SC-Medium, HarmonyOS Sans SC;
  font-weight: 500;
  margin-bottom: 10pxs;
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
.goback-btn {
  position: fixed;
  top: 20px;
  right: 80px;
}
/* 开始游戏CSS */
.acction-span-start {
  display: flex;
  justify-content: space-around;
  align-items: space-between;
  flex-direction: row;
  height: 100%;
}
.plate-chart {
  margin-left: 32px;
}
.display-plate {
  width: 240px;
  height: 146px;
  background-color: #f3a4b4;
  padding-left: 90px;
  padding-top: 40px;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
}
.plate-font {
  margin-bottom: 15px;
  color: #595959;
}
.start-green-wrapper {
  height: max-content;
  width: max-content;
  position: relative;
}
.start-arrow-top {
  position: absolute;
  top: -5px;
  left: 10px;
  height: 80px;
  width: 80px;
  z-index: 0;
}
.start-arrow-bottom {
  position: absolute;
  bottom: -5px;
  left: 10px;
  height: 80px;
  width: 80px;
  z-index: 0;
  display: inline-block;
  transform: rotate(180deg);
}
.start-green {
  position: inherit;
  top: 0;
  left: 0;
  height: 120px;
  width: 82px;
  background-color: #b4da85;
  border-radius: 6px;
  border: #fff solid 8px;
  margin: 45px 0;
  z-index: 1;
}

.start_span_right-top {
  display: flex;
  justify-content: space-between;
  margin: 10px 0;
}
.start_span_right-bottom {
  width: 330px;
  height: 200px;
  background: #ffffff;
  border-radius: 6px 6px 6px 6px;
  opacity: 1;
}
</style>
