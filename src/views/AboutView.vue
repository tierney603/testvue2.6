<template>
  <div class="app">
    <h1>剩余抽奖次数：{{ num }}</h1>
    <round-turntable 
    ref="roundTurntable" 
    :prizeData="prizeData" 
    :turntableStyleOption="turntableStyleOption"
    @endRotation="endRotation" 
    >
    </round-turntable>

    <div class="btnBox">
    大小：<input type="text" v-model="turntableStyleOption.size">
    <button @click="initRoundTurntable">生成</button>
    <button @click="startRotation">抽奖</button>
  </div>
    <!-- <img src="../../assets/p.png" alt=""> -->
  </div>
</template>

<script>
import roundTurntable from '../components/roundTurntable.vue';
export default {
  name: 'App',
  components: {
    roundTurntable
  },
  data() {
    return {
      // 转盘上的奖品数据,也可以单独进行设置奖品背景色
      prizeData: [
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
        {
          id: 3,
          prizeName: '三等奖',
          img: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jj20.com%2Fup%2Fallimg%2F4k%2Fs%2F02%2F2109242332225H9-0-lp.jpg&refer=http%3A%2F%2Fimg.jj20.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665239287&t=a10a5e7dff3cf09e426eb8b90b1b2694',
          // prizeBc:'#FC262C',
          // fontColor: 'red',
          // fontSize: 10,
          // imgSize: 40,
        },
        {
          id: 4,
          prizeName: '四等奖',
          img: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jj20.com%2Fup%2Fallimg%2F4k%2Fs%2F02%2F2109242332225H9-0-lp.jpg&refer=http%3A%2F%2Fimg.jj20.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665239287&t=a10a5e7dff3cf09e426eb8b90b1b2694',
          // prizeBc:'#3A8BFF',
          // fontColor: 'red',
          // fontSize: 10,
          // imgSize: 40,
        },
        {
          id: 5,
          prizeName: '五等奖',
          img: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jj20.com%2Fup%2Fallimg%2F4k%2Fs%2F02%2F2109242332225H9-0-lp.jpg&refer=http%3A%2F%2Fimg.jj20.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665239287&t=a10a5e7dff3cf09e426eb8b90b1b2694',
          // prizeBc:'#EE7602',
          // fontColor: 'red',
          // fontSize: 10,
          // imgSize: 40,


        },
        {
          id: 6,
          prizeName: '六等奖',
          img: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jj20.com%2Fup%2Fallimg%2F4k%2Fs%2F02%2F2109242332225H9-0-lp.jpg&refer=http%3A%2F%2Fimg.jj20.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665239287&t=a10a5e7dff3cf09e426eb8b90b1b2694',
          // prizeBc:'#FE339F',
          // fontColor: 'red',
          // fontSize: 10,
          // imgSize: 40,
        },
      ],

      /* 转盘样式的选项*/
      turntableStyleOption: {
        prizeBc: ['#f9ebdf', '#f8dfc2'], // 商品背景色
        borderColor: '#fff', // 转盘的外边框颜色
        fontColor: "#111",//字体颜色
        fontSize: 10,//文字大小
        imgSize: 40,//奖品图片大小
        size: 350,//转盘大小
        duringTime: 5,// 转动需要持续的时间（s）
        rotateCircle: 10,// 转动的圈数
        pointerSetup:{
          src:require("../assets/p.png"),
          top:-60,
          botttom:0,
          left:0,
          right:0,
          rotate:0,//旋转
        },
        dialLayoutSetup:{
          src:require("../assets/dialLayout.png"),
          size:400
        }
        


      },
      prizeIndex: 1,// 中奖的奖品的index默认指向
      isLocking: false,// 用来锁定转盘，避免同时多次点击转动
      num: 100,// 剩余抽奖次数
    }
  },
  methods: {
    initRoundTurntable() {
      this.$refs.roundTurntable.init()
    },
    startRotation() {

      // 如果还不可以转动
      if (!this.canBeRotated()) {
        return false;
      }

      // 先上锁
      this.isLocking = true;

      // 设置在哪里停下，应该与后台交互，这里随机抽取--------------
      /*
      目前基本均衡获取 最小值 0 和最大值 1 的几率少一半。
      */
      const index = (Math.random() * this.prizeData.length).toFixed(2)

      // 成功后次数减少一次
      this.num--;
      this.prizeIndex = index;

      // 告诉子组件，开始转动了
      this.$refs.roundTurntable.rotate(index);

    },
    // 转动完转盘触发的函数
    endRotation() {
      let num = Number(this.prizeIndex).toFixed()
      if (num >= this.prizeData.length) {
        num = 0
      }
      // 提示中奖
      console.log('获取的奖品号', num, "实际", this.prizeIndex)
      alert(`恭喜您获奖啦,您的奖品是：${this.prizeData[num].prizeName}`);
      // 解锁
      this.isLocking = false;
    },
    // 判断是否可以转动
    canBeRotated() {
      if (this.num <= 0) {
        alert('已经木有次数了！');
        return false;
      }
      if (this.isLocking) {
        return false;
      }
      return true;
    },
  }
}
</script>

<style scoped>
.app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  padding: 0;
}
</style>