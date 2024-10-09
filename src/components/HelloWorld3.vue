<template>
  <div class="gameBox">
    <div class="bg1"></div>
    <div class="bg2" v-show="lampShow"></div>
    <div class="start" @click="move">
      <p>{{ number_of_draws }}次</p>
    </div>
    <ul>
      <li v-for="(item, i) in list" :key="i" :class="['item' + (i + 1), { active: index == i }]">
        <div class="img1">
          <img :src="item.image" alt="" />
        </div>
        <p>+{{ item.number }}{{ item.prize_name }}</p>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: [], //奖品列表
      index: 0, // 当前转动到哪个位置，第一次起点位置0,对应页面item1位置
      count: 8, // 总共有多少个位置
      times: 0, // 转动跑格子次数,
      cycle: 60, // 转动基本次数：即至少需要转动多少次再进入抽奖环节
      speed: 200, // 初始转动速度
      lampShow: false, //开始抽奖，灯光闪烁
      timer: 0, // 转动定时器
      lamp: 0, // 灯光定时器
      prize: 0, // 中奖位置，接口返回
      number_of_draws: 0, //限制每天抽奖次数，接口返回
      prize_data: {
        //中奖信息
        id: Number, //奖品ID
        name: '', //奖品名称
        number: Number, //奖品数量
        image: '', //奖品图片
        type: Number, // 奖品类型
      },
    };
  },
  mounted() {
    //获取奖品列表

    this.list = [
      { id: '1', prize_name: '乐豆', number: 45, image: 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg' },
      { id: '2', prize_name: '乐豆', number: 945, image: 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg' },
      { id: '3', prize_name: '乐豆', number: 745, image: 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg' },
      { id: '4', prize_name: '乐豆', number: 445, image: 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg' },
      { id: '5', prize_name: '乐豆', number: 545, image: 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg' },
      { id: '6', prize_name: '乐豆', number: 345, image: 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg' },
      { id: '7', prize_name: '乐豆', number: 145, image: 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg' },
      { id: '8', prize_name: '乐豆', number: 245, image: 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg' },
    ]; // 奖品列表数据
    this.number_of_draws = 9; // 该用户剩余抽奖次数
  },
  methods: {
    //点击开始抽奖
    move() {
      if (this.number_of_draws == 0) {
        this.$toast('今日抽奖次数已用完,明天再来吧');
      } else if (this.times != 0) {
        this.$toast('正在抽奖中，请勿重复点击');
      } else {
        this.number_of_draws--; //抽奖开始，次数-1
        this.speed = 200; //每次抽奖速度初始化为200
        this.prize_data = {}; //已经拿到中奖信息，页面展示，等抽奖结束后，将弹窗弹出
        this.prize = 8 - 1; //中奖位置赋值，跑马灯最终停留位置，这里实际位置需要-1
        this.startRoll(); //执行抽奖
        this.lamp = setInterval(() => {
          //灯光闪烁开启
          this.lampShow = !this.lampShow;
        }, 300);
      }
    },
    // 开始转动
    startRoll() {
      this.times += 1; // 转动次数
      this.oneRoll(); // 转动过程调用的每一次转动方法，这里是第一次调用初始化
      this.usePrize();
    },

    // 每一次转动
    oneRoll() {
      let index = this.index; // 当前转动到哪个位置
      const count = 8; // 总共有多少个位置
      index += 1;
      if (index > count - 1) {
        index = 0;
      }
      this.index = index;
    },

    usePrize() {
      // 如果当前转动次数达到要求 && 目前转到的位置是中奖位置
      if (this.times > this.cycle + 10 && this.prize === this.index) {
        clearTimeout(this.timer); // 清除转动定时器
        clearTimeout(this.lamp); // 清除灯光定时器
        this.lampShow = false; // 白色灯隐藏
        this.times = 0; // 转动跑格子次数初始化为0，可以开始下次抽奖

        if (this.prize_data.type == 0) {
          console.log('未中奖，谢谢参与'); //未中奖提示弹出，
        } else {
          console.log('中奖啦'); //中奖弹出提示
        }
      } else {
        if (this.times < this.cycle - 20) {
          this.speed -= 4; // 加快转动速度
        } else {
          this.speed += 10; // 抽奖即将结束，放慢转动速度
        }
        this.timer = setTimeout(this.startRoll, this.speed); //开始转动
      }
    },
  },
};
</script>
<style scoped lang="less">
/*
 整体布局采用定位实现
 gameBox:父盒子，最外层背景图
 bg1:灰色灯
 bg2:点击开始按钮后，白色灯出现，同时要每个500s同bg1做切换隐藏显示
 start：按钮样式
 item1-8:通过定位方式将dom元素顺时针排列
 active：点击开始开妞后，从1位置开始高亮，类似跑马灯
*/
.gameBox {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 326px;
  height: 326px;
  margin: 150px auto 0;
  border-radius: 8px;
  background: url(../../common/images/home/game1.png) no-repeat left top;
  background-size: 326px 326px;
  position: relative;
  .bg1 {
    position: absolute;
    left: 4.5px;
    top: 4px;
    width: 317px;
    height: 317px;
    background: url(../../common/images/home/game3.png) no-repeat center;
    background-size: 317px 317px;
  }
  .bg2 {
    position: absolute;
    left: 4.5px;
    top: 4px;
    width: 317px;
    height: 317px;
    background: url(../../common/images/home/game2.png) no-repeat center;
    background-size: 317px 317px;
  }
  .start {
    position: relative;
    padding-top: 70px;
    width: 86px;
    height: 86px;
    background: url(https://img.picui.cn/free/2024/10/10/6706e0a82a434.jpg) no-repeat center;
    background-size: 86px 86px;
    p {
      text-align: center;
      font-size: 12px;
      font-weight: 400;
      color: rgba(255, 255, 255, 1);
    }
  }
  ul {
    li {
      position: absolute;
      width: 86px;
      height: 86px;
      background: rgba(255, 255, 255, 1);
      border: 2px solid rgba(227, 161, 0, 1);
      border-radius: 8px;
      .img1 {
        margin: 15px auto 3px;
        width: 35px;
        height: 35px;
        img {
          width: 100%;
          height: auto;
        }
      }
      p {
        text-align: center;
        font-size: 13px;
        font-weight: 500;
        color: rgba(153, 153, 153, 1);
      }
    }
    .item1 {
      left: 25px;
      top: 25px;
    }
    .item2 {
      left: 120px;
      top: 25px;
    }
    .item3 {
      left: 215px;
      top: 25px;
    }
    .item4 {
      left: 215px;
      top: 120px;
    }
    .item5 {
      left: 215px;
      top: 215px;
    }
    .item6 {
      left: 120px;
      top: 215px;
    }
    .item7 {
      left: 25px;
      top: 215px;
    }
    .item8 {
      left: 25px;
      top: 120px;
    }
    .active {
      background: #fff2b1;
    }
  }
}
</style>
