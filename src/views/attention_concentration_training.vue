<template>
  <div class="wapper">
    <div class="attention_concentration-widget">
      <div class="acction_span-left-right" v-if="!start">
        <div class="acction_span-left">
          <h2 class="title">注意力集中性训练</h2>
          <div class="attention_concentration-config">
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
        <div class="attention_concentration-content">
          <div
            v-for="(item, index) in cards"
            :class="[
              'card',
              rightArray.indexOf(index) >= 0 ? 'active' : '',
              currentChooseArray.indexOf(index) >= 0 ? 'active' : '',
            ]"
            :style="cardStyle"
            @click="choose(index)"
          >
            {{ item }}
          </div>
        </div>
        <div class="choose-info">
          <p class="plate-font">用时: {{ timmerShow }}</p>
          <p class="plate-font">
            轮数：<span style="color: #2868c9">{{ formData.count }}</span>
          </p>
          <p class="plate-font">此刻： {{ currentIndex }}</p>
          <p class="plate-font">总共： {{ findPairs.length }}</p>
          <p class="plate-font">
            剩余： {{ findPairs.length - rightArray.length / 2 }}
          </p>
          <p class="plate-font">
            错误次数:
            <span style="color: #ea0000">{{
              currentTrainResult.errorNumber
            }}</span>
          </p>
        </div>
      </div>
      <div
        class="attention_concentration_train-time-count"
        v-if="timeCountShow"
      >
        {{ count }}
      </div>
    </div>
    <img src="../assets/children.png" class="chidren-img" />
  </div>
</template>

<script>
import { savePationData } from "../api/index";

export default {
  data() {
    return {
      time: "00: 00: 00",
      start: false,
      currentIndex: 1,
      rightArray: [],
      cards: [],
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
      currentChooseArray: [],
      trainResultTotal: [],
    };
  },
  computed: {
    numberCards() {
      if (this.formData.level == 1) {
        return 10;
      }

      if (this.formData.level == 2) {
        return 20;
      }

      if (this.formData.level == 3) {
        return 30;
      }

      if (this.formData.level == 4) {
        return 40;
      }
    },
    findPairs() {
      let used = []; // 用于记录已经使用过的元素
      let pairs = []; // 用于记录满足条件的元素对
      const arr = this.cards;
      for (let i = 0; i < arr.length - 1; i++) {
        if (used.includes(i)) continue; // 如果 i 已经使用过，跳过该元素
        for (let j = i + 1; j < arr.length; j++) {
          if (used.includes(j)) continue; // 如果 j 已经使用过，跳过该元素
          if (arr[i] + arr[j] === 10) {
            pairs.push([arr[i], arr[j]]);
            used.push(i, j);
            break; // 找到一组符合条件的元素，跳出内循环
          }
        }
      }
      return pairs;
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
        .map((item, index) => parseInt(9 * Math.random() + 1))
        .sort(() => Math.random() - 0.5);
      this.timeCount();
    },
    async endTotalTask() {
      console.log("end total task");
      console.log(this.trainResultTotal);
      this.start = false;
      clearInterval(this.timmer);
      const res = await savePationData(this.trainResultTotal);
      this.$router.go(-1);
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
    reStart() {
      console.log("this is re start");
      if (this.timmer) {
        clearInterval(this.timmer);
      }
      this.currentTrainResult.time = {
        time: 0,
        errorNumber: 0,
      };
      this.rightArray = [];
      this.cards = new Array(this.numberCards * this.numberCards)
        .fill(15)
        .map((item, index) => parseInt(9 * Math.random() + 1))
        .sort(() => Math.random() - 0.5);
      this.timeCount();
    },
    choose(index) {
      if (this.currentChooseArray.length == 0) {
        this.currentChooseArray.push(index);
        return;
      }
      if (this.cards[this.currentChooseArray[0]] + this.cards[index] == 10) {
        this.rightArray.push(this.currentChooseArray[0], index);
        this.currentChooseArray = [];
      } else {
        this.currentTrainResult.errorNumber += 1;
        this.currentChooseArray = [];
      }
      console.log(this.findPairs, this.rightArray);
      if (this.findPairs.length == this.rightArray.length / 2) {
        this.trainResultTotal.push({
          pationId: this.$router.currentRoute.params.id,
          mode: "concentration",
          currentTime: +new Date(),
          level: this.formData.level,
          time: this.currentTrainResult.time / 1000,
          errorNumber: this.currentTrainResult.errorNumber,
          totalNumber: this.findPairs.length,
        });
        if (this.currentIndex >= this.formData.count) {
          this.endTotalTask();
        } else {
          this.currentIndex += 1;
          this.reStart();
        }
      }
    },
    generateRandomNumbers() {
      const arr = [];
      let sum = 0;
      for (let i = 0; i < 5; i++) {
        const num = Math.random() * 0.1 + 0.15;
        arr.push(num);
        sum += num;
      }
      const multiplier = 1 / sum;
      for (let i = 0; i < arr.length; i++) {
        arr[i] *= multiplier;
      }
      return arr;
    },
    getRandomHeight(start, totalHeight) {
      console.log(start, totalHeight);
    },
  },
};
</script>

<style scoped>
.attention_concentration-widget {
  box-sizing: border-box;
}
.choose-info {
  width: 240px;
  height: max-content;
  background-color: #f3a4b4;
  padding-left: 90px;
  padding-top: 40px;
  padding-bottom: 40px;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
  margin-left: 32px;
}
.attention_concentration-content {
  width: 600px;
  height: 600px;
  /* border-right: 1px solid #ddd; */
  /* border-bottom: 1px solid #ddd; */
  background-color: #fff;
  border: 19px solid #f3a4b4;
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

.attention_concentration_train-time-count {
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
.goback-btn {
  position: fixed;
  top: 20px;
  right: 80px;
}
/* 游戏开始css */
.acction-span-start {
  display: flex;
  justify-content: space-around;
  align-items: space-between;
  flex-direction: row;
  height: 100%;
}
.plate-font {
  margin-bottom: 15px;
  color: #595959;
  line-height: 22px;
}
</style>
