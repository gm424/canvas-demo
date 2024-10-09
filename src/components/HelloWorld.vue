<template>
  <div id="app">
    <canvas width="400" height="200" id="canvas"></canvas>
  </div>
</template>

<script setup>
import { reactive, toRefs, onMounted, nextTick } from 'vue';

const state = reactive({
  lotteryImg: null, // 奖品图片
  mousedown: false, // 鼠标是否按下
  ctx: null, // canvas的上下文对象
});

onMounted(() => {
  nextTick(() => {
    state.canvas = document.querySelector('canvas');
    state.lotteryImg = new Image();
    state.lotteryImg.src = 'https://img.picui.cn/free/2024/10/10/6706b951d4e5f.jpg';
    // 监听图片的加载完成事件
    state.lotteryImg.addEventListener('load', handleImgLoaded);
    // 移动端触摸事件
    state.canvas.addEventListener('touchstart', eventDown);
    state.canvas.addEventListener('touchend', eventUp);
    state.canvas.addEventListener('touchmove', eventMove);
    // 网页版触摸事件
    state.canvas.addEventListener('mousedown', eventDown);
    state.canvas.addEventListener('mouseup', eventUp);
    state.canvas.addEventListener('mousemove', eventMove);
  });
});

function handleImgLoaded(e) {
  // 绘制奖品图片
  state.canvas.style.backgroundImage = 'url(' + state.lotteryImg.src + ')';
  state.canvas.style.backgroundSize = 'cover';
  // 绘制灰色涂层（源图像）
  state.ctx = state.canvas.getContext('2d');
  state.ctx.fillStyle = 'gray';
  state.ctx.fillRect(0, 0, 400, 200);
  state.ctx.globalCompositeOperation = 'destination-out';
}

// 鼠标按下
function eventDown(e) {
  e.preventDefault();
  state.mousedown = true;
}

// 鼠标抬起
function eventUp(e) {
  e.preventDefault();
  state.mousedown = false;
}

// 鼠标移动
function eventMove(e) {
  if (state.mousedown) {
    // changedTouches:涉及当前（引发）事件的触摸点列表
    if (e.changedTouches) {
      e = e.changedTouches[e.changedTouches.length - 1];
    }
    /*
                        e.pageX,e.pageY坐标相对于整个渲染页面的左上角（包括滚动隐藏距离）
                        也可以采用 e.clientX + (document.body.scrollLeft || document.documentElement.scrollTop)
                        e.clientX,e.clientY设置或获取鼠标指针位置相对于浏览器窗口（内容区域的左上角）的坐标，与浏览器是否有滚动条无关
                    */
    /*
                        offsetLeft:距离上一级定位元素左侧的距离
                        offsetTop:距离上一级定位元素上侧的距离
                    */
    const x = e.pageX - state.canvas.offsetLeft;
    const y = e.pageY - state.canvas.offsetTop;

    // 绘制圆形(目标图像)进行擦除
    state.ctx.beginPath();
    state.ctx.arc(x, y, 20, 0, Math.PI * 2);
    state.ctx.fill();
  }
}
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
