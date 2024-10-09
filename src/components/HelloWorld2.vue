<template>
  <div class="wrap">
    <div class="activcont">
      <div class="rotecont">
        <img src="https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg" ref="tianyi" style="display: none" />
        <img src="https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg" ref="taideng" style="display: none" />
        <img src="https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg" ref="yinxiang" style="display: none" />
        <img src="https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg" ref="youhuiquan200" style="display: none" />
        <img src="https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg" ref="youhuiquan300" style="display: none" />
        <img src="https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg" ref="hongbao" style="display: none" />
        <img src="https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg" ref="wangyi" style="display: none" />
        <div class="turnplate">
          <canvas class="item" ref="wheelcanvas" width="422px" height="422px"></canvas>
          <img class="pointer" src="https://img.picui.cn/free/2024/10/10/6706bb63cb20a.jpg" @click="clickRotate" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    msg: String,
  },
  data() {
    return {
      turnplate: {
        restaraunts: ['天翼高清10元', '智能音箱', '智能台灯', '300元购机优惠券', '200元购机优惠券', '红包'],
        colors: ['#ffecd6', '#ffdcc5', '#ffecd6', '#ffdcc5', '#ffecd6', '#ffdcc5', '#ffecd6'],
        outsideRadius: 168,
        textRadius: 140,
        insideRadius: 30,
        startAngle: 0,
      },
      count: 0,
    };
  },
  methods: {
    drawRouletteWheel() {
      const canvas = this.$refs.wheelcanvas;
      if (canvas.getContext) {
        const arc = Math.PI / (this.turnplate.restaraunts.length / 2);
        console.log('arc', arc, Math.PI, this.turnplate.restaraunts.length / 2);
        const ctx = canvas.getContext('2d');
        ctx.clearRect(0, 0, 422, 422);
        ctx.strokeStyle = '#f5b599';
        ctx.font = 'bold 16px Helvetica';
        for (let i = 0; i < this.turnplate.restaraunts.length; i++) {
          const angle = this.turnplate.startAngle + i * arc;
          console.log('angle', angle);
          ctx.fillStyle = this.turnplate.colors[i];
          ctx.beginPath();
          ctx.arc(211, 211, this.turnplate.outsideRadius, angle, angle + arc, false);
          ctx.arc(211, 211, this.turnplate.insideRadius, angle + arc, angle, true);
          ctx.stroke();
          ctx.fill();
          ctx.save();
          ctx.fillStyle = '#f3142d';
          const text = this.turnplate.restaraunts[i];
          ctx.translate(
            211 + Math.cos(angle + arc / 2) * this.turnplate.textRadius,
            211 + Math.sin(angle + arc / 2) * this.turnplate.textRadius
          );
          ctx.rotate(angle + arc / 2 + Math.PI / 2);
          ctx.fillText(text, -ctx.measureText(text).width / 2, 0);

          if (text.indexOf('天翼') >= 0) {
            const img = this.$refs.tianyi;
            ctx.drawImage(img, -25, 10, 30, 30);
          } else if (text.indexOf('台灯') >= 0) {
            const img = this.$refs.taideng;
            ctx.drawImage(img, -25, 10, 30, 30);
          } else if (text.indexOf('音箱') >= 0) {
            const img = this.$refs.yinxiang;
            ctx.drawImage(img, -25, 10, 30, 30);
          } else if (text.indexOf('200') >= 0) {
            const img = this.$refs.youhuiquan200;
            ctx.drawImage(img, -25, 10, 30, 30);
          } else if (text.indexOf('300') >= 0) {
            const img = this.$refs.youhuiquan300;
            ctx.drawImage(img, -25, 10, 30, 30);
          } else if (text.indexOf('红包') >= 0) {
            const img = this.$refs.hongbao;
            ctx.drawImage(img, -25, 10, 30, 30);
          } else if (text.indexOf('网易') >= 0) {
            const img = this.$refs.wangyi;
            ctx.drawImage(img, -25, 10, 30, 30);
          }
          ctx.restore();
        }
      }
    },
    getRadom(x, y) {
      const min = Math.ceil(x);
      const max = Math.floor(y);
      return Math.floor(Math.random() * (max - min + 1)) + min;
    },
    getPrize(num) {
      switch (num) {
        case 0:
          this.rotate(210, this.turnplate.restaraunts[0]);
          break;
        case 1:
          this.rotate(150, this.turnplate.restaraunts[1]);
          break;
        case 2:
          this.rotate(97, this.turnplate.restaraunts[2]);
          break;
        case 3:
          this.rotate(48, this.turnplate.restaraunts[3]);
          break;
        case 4:
          this.rotate(355, this.turnplate.restaraunts[4]);
          break;
        case 5:
          this.rotate(305, this.turnplate.restaraunts[5]);
          break;
        case 6:
          this.rotate(255, this.turnplate.restaraunts[6]);
          break;
      }
    },
    rotate(angle, prize) {
      console.log(`Rotating to angle: ${angle}, prize: ${prize}`);
      // 这里写你的旋转逻辑
    },
    clickRotate() {
      // 获取奖品总数
      const totalPrizes = this.turnplate.restaraunts.length;
      // 生成一个随机的奖品索引
      const prizeIndex = this.getRadom(0, totalPrizes - 1);

      // 每个奖品的角度（以弧度表示）
      const anglePerPrize = 360 / totalPrizes;
      // 计算奖品的目标角度，使得指针停在奖品区域的中心
      const prizeAngle = anglePerPrize * prizeIndex;

      // 我们希望转盘至少转 3 圈，可以增加这个值来调整转动圈数
      const baseRotation = 360 * 3;

      // 由于指针初始指向 0 度（12 点钟方向），目标角度为 baseRotation + (360 - prizeAngle)
      const finalRotation = baseRotation + (225 - prizeAngle);

      // 应用旋转动画
      this.$refs.wheelcanvas.style.transition = 'transform 5s ease-out';
      this.$refs.wheelcanvas.style.transform = `rotate(${finalRotation}deg)`;

      // 在动画结束后处理中奖逻辑
      this.$refs.wheelcanvas.addEventListener(
        'transitionend',
        () => {
          this.getPrize(prizeIndex);
        },
        { once: true }
      );
    },
  },
  mounted() {
    this.$nextTick(() => {
      // 当图片都渲染完成，再调绘制
      let img = this.$refs.tianyi;
      img.onload = () => {
        this.drawRouletteWheel();
      };
    });
  },
};
</script>
<style scoped>
.wrap {
  width: 100%;
  max-width: 640px;
  margin: auto;
}
.activcont {
  width: 100%;
  position: relative;
}
.rotecont {
  width: 80%;
  position: relative;
  z-index: 2;
  max-width: 512px;
  left: 0px;
  right: 0px;
  margin: auto;
  overflow: hidden;
}
.rotecont .turnplate {
  display: block;
  width: 100%;
  position: relative;
  background-image: url('');
  background-size: 100% 100%;
}
.rotecont .turnplate canvas.item {
  width: 100%;
  margin-top: 0.2rem;
  transition: all 8s;
}
.rotecont .turnplate img.pointer {
  position: absolute;
  width: 5rem;
  height: 5rem;
  left: 0px;
  right: 0px;
  margin: auto;
  top: 35%;
}
.rotecont .turnplate canvas.item {
  transition: all 8s;
}
</style>
