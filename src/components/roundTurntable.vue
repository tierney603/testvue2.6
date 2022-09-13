<template>
  <div class="dial_com" :style="{width: turntableStyleOption.size+'px'}">

    <div class="dial_plate"
      :style="{width: turntableStyleOption.size+'px',height:turntableStyleOption.size+'px',backgroundColor:turntableStyleOption.borderColor}">
      <!-- 表盘 -->
      <div class="turntable" ref="turntable"
        :style="{width: turntableStyleOption.size+'px',height:turntableStyleOption.size+'px'}">

        <!-- 表盘边框 -->
        <!-- <h1>{{turntableStyleOption.dialLayoutSetup.size}}</h1> -->
        <img class="dial_layout" v-if="turntableStyleOption.dialLayoutSetup.src"
          :src="turntableStyleOption.dialLayoutSetup.src" alt=""
          :style="{width:turntableStyleOption.dialLayoutSetup.size+'px',height:turntableStyleOption.dialLayoutSetup.size+'px'}">

        <div class="pointer" v-if="turntableStyleOption.pointerSetup.src">
          <img class="pointer_needle_img" :src="turntableStyleOption.pointerSetup.src" :style="{
          top:turntableStyleOption.pointerSetup.top+'px',
          left:turntableStyleOption.pointerSetup.left+'px',
          right:turntableStyleOption.pointerSetup.right+'px',
          bottom:turntableStyleOption.pointerSetup.bottom+'px',
          transform: `rotate(${turntableStyleOption.pointerSetup.rotate}deg)`,
          }" alt="">
        </div>


        <div class="pointer" v-else>
          <div class="pointer_dot"></div>
          <div class="pointer_needle"></div>
        </div>

        <div class="myTurntable" :style="{ transform: rotateAngle, transition: rotateTransition }">
          <canvas id="canvas" ref="canvas">
            当前浏览器版本过低，请使用其他浏览器尝试
          </canvas>

          <div class="prize-container">
            <div v-for="(item, index) in prizeData" :key="index" class="item"
              :style="getRotateAngle(index)">
              <div class="turntable-name" :style="{
              'font-size':item.fontSize?item.fontSize+'px':turntableStyleOption.fontSize+'px',
              'color':item.fontColor?item.fontColor:turntableStyleOption.fontColor
              }">
                {{ item.prizeName }}</div>

              <div class="turntable-img">
                <img :src="item.img"
                  :style="{width:item.imgSize?item.imgSize+'px':turntableStyleOption.imgSize+'px',height:item.imgSize?item.imgSize+'px':turntableStyleOption.imgSize+'px'}">
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
  
<script>

export default {
  name: "round-turntable",
  mounted() {
    this.init();
  },
  props: {
    prizeData: {
      type: Array,
      default: function () {
        return [
          {
            id: 1,
            prizeName: '一等奖',
            img: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jj20.com%2Fup%2Fallimg%2F4k%2Fs%2F02%2F2109242332225H9-0-lp.jpg&refer=http%3A%2F%2Fimg.jj20.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665239287&t=a10a5e7dff3cf09e426eb8b90b1b2694',
            // prizeBc:'#4D3FFF',
            // fontColor: '#111',
            // fontSize: 10,
            // imgSize: 40,
          },
          {
            id: 2,
            prizeName: '二等奖',
            img: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jj20.com%2Fup%2Fallimg%2F4k%2Fs%2F02%2F2109242332225H9-0-lp.jpg&refer=http%3A%2F%2Fimg.jj20.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665239287&t=a10a5e7dff3cf09e426eb8b90b1b2694',
            // prizeBc:'#AE3EFF',
            // fontColor: 'red',
            // fontSize: 10,
            // imgSize: 40,
          },
        ]
      }
    },

    rotateCircle: {
      default: 6,
    },
    turntableStyleOption: {
      type: Object,
      default: () => {
        return {
          // 背景色
          prizeBc: ['#f9ebdf', '#f8dfc2'], // 商品背景色
          size: 300,//大小
          borderColor: "#fff", // 转盘的外边框颜色
          fontColor: 'black',// 文字颜色
          imgSize: 40,
          fontSize: 15,
          pointerSetup: {
            src: false,
            top: 0,
            botttom: 0,
            left: 0,
            right: 0,
            rotate: 0,//旋转
          },
          dialLayoutSetup: {
            src: false,
            size: 360,
          },
        };
      },
    },
    duringTime: {
      default: 4.5,
    },
    size: {
      default: 300,
    },
  },
  data() {
    return {
      // 开始转动的角度
      startRotateDegree: 0,
      rotateAngle: 0,
      rotateTransition: "",
      prizeBcOption:  ['#f9ebdf', '#f8dfc2'], // 商品背景色
    };
  },
  methods: {

    // 根据index计算每一格要旋转的角度的样式
    getRotateAngle(index) {
      const angle =
        (360 / this.prizeData.length) * index + 180 / this.prizeData.length;
      return {
        transform: `rotate(${angle}deg)`,
      };
    },

    // 初始化圆形转盘canvas
    init() {
      console.log(this.turntableStyleOption)

      // 各种数据
      let prizeBgColors = this.initPrizeBg()
      const prizeNum = this.prizeData.length

      // 表盘内部边框色
      let borderColor = "#fff"

      // 开始绘画
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext("2d");
      const canvasW = (this.$refs.canvas.width =
        this.$refs.turntable.clientWidth); // 画板的高度
      const canvasH = (this.$refs.canvas.height =
        this.$refs.turntable.clientHeight); // 画板的宽度
      // translate方法重新映射画布上的 (0,0) 位置
      ctx.translate(0, canvasH);
      // rotate方法旋转当前的绘图，因为文字适合当前扇形中心线垂直的！
      ctx.rotate((-90 * Math.PI) / 180);
      // 圆环的外圆的半径
      const outRadius = canvasW / 2;
      // 圆环的内圆的半径
      const innerRadius = 0;
      const baseAngle = (Math.PI * 2) / prizeNum; // 计算每个奖项所占角度数
      ctx.clearRect(0, 0, canvasW, canvasH); //去掉背景默认的黑色
      ctx.strokeStyle = borderColor; // 设置画图线的颜色
      ctx.lineWidth = 0 //图线的粗细
      for (let index = 0; index < prizeNum; index++) {
        const angle = index * baseAngle;
        ctx.fillStyle = prizeBgColors[index]; //设置每个扇形区域的颜色
        ctx.beginPath(); //开始绘制
        // 标准圆弧：arc(x,y,radius,startAngle,endAngle,anticlockwise)
        ctx.arc(
          canvasW * 0.5,
          canvasH * 0.5,
          outRadius,
          angle,
          angle + baseAngle,
          false
        );
        ctx.arc(
          canvasW * 0.5,
          canvasH * 0.5,
          innerRadius,
          angle + baseAngle,
          angle,
          true
        );
        ctx.stroke(); //开始链线
        ctx.fill(); //填充颜色
        ctx.save(); //保存当前环境的状态
      }
    },
    // 转动起来
    rotate(index) {
      // 运转时长
      const { duringTime, rotateCircle } = this.turntableStyleOption;
      const rotateAngle =
        this.startRotateDegree +
        rotateCircle * 360 +
        360 -
        (180 / this.prizeData.length + (360 / this.prizeData.length) * index) -
        (this.startRotateDegree % 360);
      this.startRotateDegree = rotateAngle;
      this.rotateAngle = `rotate(${rotateAngle}deg)`;
      this.rotateTransition = `transform ${duringTime}s cubic-bezier(0.250, 0.460, 0.455, 0.995)`;
      setTimeout(() => {
        this.$emit("endRotation");
      }, duringTime * 1000 + 500);
    },
    initPrizeBg() {
      // 奖品背景色
      let prizeBgColors = this.prizeData.map(item => {
        return item.prizeBc
      })
      // 初始化奖品背景色
      const elementsAreEqual = array => array.every(el => el === array[0] && el === undefined)
      let notSetPrizeBc = elementsAreEqual(prizeBgColors)
      let prizeBcOption = this.turntableStyleOption.prizeBc||this.prizeBcOption
      // 如果没有逐个设置颜色
      if (notSetPrizeBc) {
        let colorNum = 0
        for (let itemNum = 0; itemNum < prizeBgColors.length; itemNum++) {
          if (prizeBcOption[colorNum] !== undefined) {
            prizeBgColors[itemNum] = prizeBcOption[colorNum]
            colorNum++
            if (colorNum >= prizeBcOption.length) {
              colorNum = 0
            }
          } else {
            colorNum = 0

          }
        }
      }
      return prizeBgColors
    },
  },
};
</script>
  
<style scoped >
/*  */
.dial_com {
  position: relative;
  width: auto;
  padding: 10px;
  /* overflow: hidden; */
  /* background-color: #111; */
}

/* 表盘 */
.dial_plate {
  background-color: rgba(255, 255, 255, 1);
  /* position: relative; */
  border-radius: 50%;
  box-shadow: 0rem 0rem .625rem rgb(121, 121, 121);
  border: .125rem solid rgb(0, 0, 0);
}

/* 表盘边框 */
.dial_layout {
  z-index: 10;
}

.turntable {
  position: relative;
  /* position: absolute; */
  /* left: .625rem; */
  /* top: .625rem; */
  text-align: center;
  transform: translateZ(0);
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.canvas {}

.myTurntable {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;

}

.prize-container {
  position: absolute;
  left: 25%;
  top: 0;
  width: 50%;
  height: 50%;
}

.item {
  /*background: pink;*/
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  transform-origin: center bottom;
}


/* 指针 */
.pointer {
  position: absolute;
  z-index: 5;

}

/* 指针图片 */
.pointer_needle_img {
  width: 80px;
  height: 150px;
  position: relative;

}

/* 指针针尖 */
.pointer_needle {
  /* width: 0rem;
  height: 0rem; */
  border-bottom: 80px solid rgb(255, 169, 169);
  border-left: 16px solid transparent;
  border-right: 16px solid transparent;
  position: absolute;
  /* background-color: rgb(255, 251, 251); */
  z-index: 9;
  top: -60px;
  left: 0px;
}

.pointer_dot {
  background-color: rgb(255, 0, 0);
  border-radius: 50%;
  border: 1rem rgb(10, 10, 10) solid;
  position: relative;
  /* top: 10.75rem;
  left: 10.625rem; */
  z-index: 10;
  margin: 0 auto;
  /* display: flex; */
  /* justify-content: center; */
  /* align-content: center; */

}

.turntable {
  /* position: absolute;
      left: calc(50% - 12.5rem);
      top: calc(50% - 12.5rem);
      width: 25rem;
      height: 25rem; */
}

.turntable-name {
  /*background: pink;*/
  position: absolute;
  left: .625rem;
  top: 1.25rem;
  width: calc(100% - 1.25rem);
  font-size: 1.25rem;
  text-align: center;
  color: #fff;
}

.turntable-img {
  position: absolute;
  /*要居中就要50% - 宽度 / 2*/
  left: calc(50% - 5rem / 2);
  top: 3.75rem;
  width: 5rem;
  height: 5rem;

}
</style>