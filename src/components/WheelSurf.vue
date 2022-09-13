<template>
  <div
    class="WheelSurf"
    :style="{
      width: option.turntableOption.size + 'px',
      height: option.turntableOption.size + 'px',
    }"
  >
    <div
      class="turntable"
      :style="{
        backgroundImage: option.turntableOption.src,
        width: option.turntableOption.size,
        height: option.turntableOption.size,
      }"
    ></div>
    <div class="pointer">
      <img
        :src="option.pointerOption.src"
        :style="{
          width: option.pointerOption.width,
          height: option.pointerOption.height,
          transform: `rotate(${transformRotate}deg)`,
          transition: rotateTransition,
        }"
        alt="pointer"
      />
    </div>
  </div>
</template>
<script>
export default {
  name: "WheelSurf",
  props: {
    msg: String,
  },
  data() {
    return {
      // 转盘配置
      option: {
        turntableOption: {
          src: "url(https://images1.freesion.com/106/1e/1e25e4f7976d70ca9b865f3bf74e4382.png)",
          size: "500px",
        },
        pointerOption: {
          src: "https://images3.freesion.com/659/17/17c21277048fa6c760fcad3fec43d70b.png",
          size: "150px",
          width: "100px",
          height: "130px",
        },
        list: [
          {
            id: 1,
            value: "一等奖：免单4999",
            prob: 1,
          },
          {
            id: 2,
            value: "二等奖:免单50",
            prob: 5,
          },
          {
            id: 3,
            value: "三等奖:免单10",
            prob: 10,
          },
          {
            id: 4,
            value: "四等奖:免单5",
            prob: 15,
          },
          {
            id: 5,
            value: "五等奖:免息服务",
            prob: 18,
          },
          {
            id: 6,
            value: "六等奖:提高白条额度",
            prob: 20,
          },
          {
            id: 7,
            value: "七等奖:未中奖",
            prob: 38,
          },
        ],
      },
      isLocking: false, // 用来锁定转盘，避免同时多次点击转动
      prevrdm: 0, //缓存上一次的旋转度
      transformRotate: 0, //本次旋转的度
      rotateTransition: 0, //旋转圈数
      duringTime: 5, //延时
      rotateSpeed: 3, //转速
    };
  },
  methods: {
    start() {
      let num = this.option.list.length; //奖品种类数量

      let cat = Number(360 / num).toFixed(1); //总共7个扇形区域，每个区域约51.4度

      const rdm = parseInt(Math.random() * (cat - 1) + 1); //随机的数
      console.log(rdm);

      let nextrdm = 0; //本次旋转度

      nextrdm = Math.floor(num * rdm); //定义本次抽奖结果

      let biginRotate = this.rotateSpeed * 360 + (360 - this.prevrdm); //定义默认的旋转圈数，同时补全使轮盘置零，
      this.prevrdm = nextrdm;

      this.rotateTransition = `transform ${this.duringTime}s cubic-bezier(0.250, 0.460, 0.455, 0.995)`;

      this.transformRotate = nextrdm + biginRotate + this.transformRotate; //本次旋转的度

      let randmArr = this.option.list.map((v) => {
        return v.prob;
      }); //概率数组

      let _this = this;

      console.log("nextrdm!!", nextrdm, "nextrdm:", num, "*", rdm);
      setTimeout(function () {
        for (let i = 0; i <= randmArr.length; i++) {
          if (nextrdm <= cat * (i + 1)) {
            return console.warn(_this.option.list[i].value, nextrdm);
          }
        }
      }, 0);
    },
  },
};
</script>

<style scoped lang="scss">
.WheelSurf {
  position: relative;
  background-color: red;
  width: 500px;
  height: 500px;
}
.pointer {
  position: absolute;
  z-index: 1;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  // width: 100px;
  // height: 100px;
}
.turntable {
  // position: relative;
  background-color: #111;
  // width: 500px;
  // height: 500px;
  // background-image: url(https://images1.freesion.com/106/1e/1e25e4f7976d70ca9b865f3bf74e4382.png);
  background-repeat: no-repeat;
  background-size: contain;
}
</style>
