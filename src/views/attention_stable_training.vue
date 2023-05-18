<template>
  <div class="wapper">
    <div class="attention_stable-widget">
      <div class="acction_span-left-right" v-if="!start">
        <div class="acction_span-left">
          <h2 class="title">注意力稳定性训练</h2>
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
        <div class="attention_stable-content">
          <div class="left-cards">
            <div
              v-for="(item, index) in findPairs[0]"
              :class="[
                'attention_stable-content-cards',
                currentChooseId === index ? 'active' : '',
              ]"
              :style="`${cardStyle[0][index]['style']}`"
              @click="choose(index)"
            >
              {{ item }}
            </div>
          </div>
          <div class="right-cards">
            <div
              v-for="(item, index) in findPairs[1]"
              :class="[
                'attention_stable-content-cards',
                currentChooseItem === index >= 0 ? 'active' : '',
              ]"
              :style="`${cardStyle[1][index]['style']}`"
              @click="chooseItem(index)"
            >
              {{ item }}
            </div>
          </div>
          <div v-if="lines" v-for="(item, index) in lines">
            <div
              v-if="rightArray.indexOf(index) == -1"
              v-for="line in lines[index]"
              class="attention_stable-content-lines"
              :style="`width:${line['width']}px; top:${line['top']}px; left: ${line['left']}px; height: ${line['height']}px`"
            ></div>
          </div>
        </div>
        <div class="plate-chart">
          <div class="display-plate">
            <p>用时: {{ timmerShow }}</p>
            <p>总共： {{ formData.count }}</p>
            <p>剩余： {{ formData.count - trainResultTotal.length }}</p>
            <p>
              错误次数:
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
      <div class="attention_stable-content-lines-count" v-if="timeCountShow">
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
      count: 3,
      timeCountShow: false,
      time: "00: 00: 00",
      currentCount: 5,
      start: false,
      currentIndex: 1,
      rightArray: [],
      cards: [],
      shuffleIndex: [],
      totalHeight: 700,
      lines: [],
      trainResultTotal: [],
      formData: {
        level: 1,
        count: 2,
      },
      currentTrainResult: {
        errorNumber: 0,
        time: 0,
      },
      currentChooseId: "",
      currentChooseItem: "",
    };
  },
  computed: {
    numberCards() {
      if (this.formData.level == 1) {
        return 5;
      }

      if (this.formData.level == 2) {
        return 10;
      }

      if (this.formData.level == 3) {
        return 15;
      }

      if (this.formData.level == 4) {
        return 20;
      }
    },
    findPairs() {
      let arr1 = new Array(this.numberCards)
        .fill(0)
        .map((item, index) => index + 1);
      let arr2 = new Array(this.numberCards)
        .fill(0)
        .map((item, index) => String.fromCharCode(index + "A".charCodeAt()));
      return [arr1, arr2];
    },
    cardStyle() {
      return this.findPairs.map((cards) => {
        return cards.map((item, index) => {
          return {
            style: `width:${700 / cards.length}px; height: ${
              700 / cards.length
            }px; text-align: center;  line-height:  ${
              700 / cards.length
            }px;color: white; border-bottom: 1px solid white; box-sizing: border-box;`,
          };
        });
      });
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
      this.showLines();
      this.timeCount();
      this.trainResultTotal = [];
    },
    showLines() {
      const sigleCardHeight = this.totalHeight / this.numberCards;
      this.shuffleIndex = new Array(this.numberCards)
        .fill(0)
        .map((item, index) => index)
        .sort(() => Math.random() - 0.5);
      const lines = this.shuffleIndex.map((item, index) => {
        let widths = this.generateRandomNumbers(index).map(
          (item) => item * (this.totalHeight - 2 * sigleCardHeight)
        );
        let topStartPoint = index * sigleCardHeight + sigleCardHeight / 2;
        let topEndPoint = item * sigleCardHeight + sigleCardHeight / 2;
        let topPoint1 = 0;
        let topPoit2 = 0;
        if (index < this.shuffleIndex.length / 2) {
          topPoint1 =
            this.totalHeight / 2 +
            ((this.totalHeight - sigleCardHeight) / 2) * Math.random();
          topPoit2 = ((this.totalHeight - sigleCardHeight) / 2) * Math.random();
        } else {
          topPoint1 =
            ((this.totalHeight - sigleCardHeight) / 2) * Math.random() +
            sigleCardHeight / 2;
          topPoit2 =
            this.totalHeight / 2 +
            ((this.totalHeight - sigleCardHeight) / 2) * Math.random();
        }
        const positions = [
          {
            top: topStartPoint,
            left: sigleCardHeight,
          },
          {
            top: topStartPoint,
            left: sigleCardHeight + widths[0],
          },
          {
            top: topPoint1,
            left: sigleCardHeight + widths[0],
          },
          {
            top: topPoint1,
            left: sigleCardHeight + widths[0] + widths[1],
          },
          {
            top: topPoit2,
            left: sigleCardHeight + widths[0] + widths[1],
          },
          {
            top: topPoit2,
            left: sigleCardHeight + widths[0] + widths[1] + widths[2],
          },
          {
            top: topEndPoint,
            left: sigleCardHeight + widths[0] + widths[1] + widths[2],
          },
          {
            top: topEndPoint,
            left:
              sigleCardHeight + widths[0] + widths[1] + widths[2] + widths[3],
          },
        ];
        let element = [];
        for (let i = 0; i < positions.length; i++) {
          if (i == 0) {
            continue;
          }
          element.push({
            left: positions[i - 1]["left"],
            width: positions[i]["left"] - positions[i - 1]["left"] || 3,
            top:
              positions[i]["top"] > positions[i - 1]["top"]
                ? positions[i - 1]["top"]
                : positions[i]["top"],
            height:
              Math.abs(positions[i]["top"] - positions[i - 1]["top"]) + 3 || 3,
          });
        }
        return element;
      });
      this.lines = lines;
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
    async endTotalTask() {
      console.log("end total task");
      this.start = false;
      clearInterval(this.timmer);
      console.log(this.trainResultTotal);
      const res = await savePationData(this.trainResultTotal);
      this.$router.go(-1);
    },
    reStart() {
      console.log("this is re start");
      if (this.timmer) {
        clearInterval(this.timmer);
      }
      this.trainResultTotal.push({
        pationId: this.$router.currentRoute.params.id,
        mode: "stable",
        currentTime: +new Date(),
        level: this.formData.level,
        time: this.currentTrainResult.time / 1000,
        errorNumber: this.currentTrainResult.errorNumber,
      });
      this.currentTrainResult = {
        time: 0,
        errorNumber: 0,
      };
      this.rightArray = [];
      this.showLines();
      this.timeCount();
    },
    choose(index) {
      if (this.currentChooseItem === "") {
        this.currentChooseId = index;
        return;
      }

      if (this.shuffleIndex[this.currentChooseItem] == index) {
        this.rightArray.push(this.currentChooseId);
      } else {
        this.currentTrainResult.errorNumber += 1;
        return;
      }
      this.currentChooseItem = "";
      this.currentChooseId = "";
      if (this.rightArray.length < this.shuffleIndex) {
        return;
      }
      this.currentIndex += 1;
      if (this.currentIndex > this.formData.count) {
        this.trainResultTotal.push({
          pationId: this.$router.currentRoute.params.id,
          mode: "stable",
          currentTime: +new Date(),
          level: this.formData.level,
          time: this.currentTrainResult.time / 1000,
          errorNumber: this.currentTrainResult.errorNumber,
        });
        this.endTotalTask();
        return;
      }

      this.reStart();
    },
    chooseItem(index) {
      if (this.currentChooseId === "") {
        this.currentChooseItem = index;
        return;
      }
      if (this.currentChooseId == this.shuffleIndex.indexOf(index)) {
        this.rightArray.push(this.currentChooseId);
      } else {
        this.currentTrainResult.errorNumber += 1;
        return;
      }

      this.currentChooseItem = "";
      this.currentChooseId = "";
      if (this.rightArray.length < this.shuffleIndex.length) {
        return;
      }

      this.currentIndex += 1;

      if (this.currentIndex > this.formData.count) {
        this.trainResultTotal.push({
          pationId: this.$router.currentRoute.params.pationId,
          mode: "stable",
          currentTime: +new Date(),
          level: this.formData.level,
          time: this.currentTrainResult.time / 1000,
          errorNumber: this.currentTrainResult.errorNumber,
        });
        this.endTotalTask();
        return;
      }
      this.reStart();
    },
    generateRandomNumbers(index) {
      const start = 0.05 + (index / this.numberCards) * 0.37;
      const second = 0.235;
      const third = 0.235;
      const last = 1 - start - second - third;
      const arr = [start, second, third, last];
      return arr;
    },
    getRandomHeight(start, totalHeight) {
      console.log(start, totalHeight);
    },
  },
};
</script>

<style scoped>
.attention_stable-widget {
  box-sizing: border-box;
}

.attention_stable-content .left-cards {
  position: absolute;
  left: 0;
  top: 0;
}

.attention_stable-content .right-cards {
  position: absolute;
  right: 0;
  top: 0;
}

.attention_stable-content {
  position: relative;
  height: 700px;
  width: 700px;
  background-color: #ccc;
  border: 19px solid #f3a4b4;
  border-radius: 12px;
}

.attention_stable-content-cards {
  background: #ddd;
}

.attention_stable-content-cards.active {
  background: #787878;
}

.attention_stable-content-lines {
  position: absolute;
  display: inline-block;
  background-color: white;
  box-sizing: border-box;
  border: 1px solid hotpink;
}

.attention_stable-content-lines-count {
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
  margin: 60px 0;
}
.start_span_right-bottom {
  width: 330px;
  height: 200px;
  background: #ffffff;
  border-radius: 6px 6px 6px 6px;
  opacity: 1;
}
</style>
