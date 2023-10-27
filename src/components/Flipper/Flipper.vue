<template>
  <div class="M-Flipper" :class="[data.flipType, {'go': data.isFlipping}]">
    <div class="digital front" :class="_textClass(data.frontTextFromData)"></div>
    <div class="digital back" :class="_textClass(data.backTextFromData)"></div>
  </div>
</template>

<script setup lang="ts">
import {ref, onMounted, defineExpose } from 'vue';


const props = defineProps({
  frontText: {
    type: [Number, String],
    default: 0,
  },
  backText: {
    type: [Number, String],
    default: 0,
  },
  duration: {
    type: Number,
    default: 600,
  },
});

interface Data {
  isFlipping: boolean;
  flipType: string;
  frontTextFromData: number | string;
  backTextFromData: number | string;
}

const data = ref<Data>({
  isFlipping: false,
  flipType: 'down',
  frontTextFromData: 0,
  backTextFromData: 0,
});

onMounted(() => {
  data.value.frontTextFromData = props.frontText;
  data.value.backTextFromData = props.backText;
});

function _textClass(number: number | string): string {
  return `number${number}`;
}

function _flip(type: string, front: number | string, back: number | string): void {
  // 如果处于翻转中，则不执行
  if (data.value.isFlipping) {
    return;
  }
  data.value.frontTextFromData = front;
  data.value.backTextFromData = back;
  // 根据传递过来的type设置翻转方向
  data.value.flipType = type;
  // 设置翻转状态为true
  data.value.isFlipping = true;
  setTimeout(() => {
    // 设置翻转状态为false
    data.value.isFlipping = false;
    data.value.frontTextFromData = back;
  }, props.duration);
}

function flipDown(front: number | string, back: number | string): void {
  _flip('down', front, back);
}

function flipUp(front: number | string, back: number | string): void {
  _flip('up', front, back);
}

function setFront(text: number | string): void {
  data.value.frontTextFromData = text;
}

function setBack(text: number | string): void {
  data.value.backTextFromData = text;
}

defineExpose({
  flipDown,
  flipUp,
  setFront,
  setBack
});

</script>

<style>
.M-Flipper {
  display: inline-block;
  position: relative;
  width: 31px;
  height: 52px;
  line-height: 52px;
  /* border: solid 1px #000; */
  border-radius: 5px;
  background: #02091F;
  font-size: 30px;
  color: #fff;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.5);
  text-align: center;
  font-family: 'Helvetica Neue';
}

.M-Flipper .digital:before,
.M-Flipper .digital:after {
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  background: #02091F;
  background-repeat: no-repeat;
  overflow: hidden;
  box-sizing: border-box;
}

.M-Flipper .digital:before {
  background: url(../assets/flipper-up.png);
  background-size: 100% 100%;
}

.M-Flipper .digital:after {
  background: url(../assets/flipper-down.png);
  background-size: 100% 100%;
}

.M-Flipper .digital:before {
  top: 0;
  bottom: 50%;
  border-radius: 5px 5px 0 0;
  /* border-bottom: solid 1px #666; */
}

.M-Flipper .digital:after {
  top: 50%;
  bottom: 0;
  border-radius: 0 0 5px 5px;
  line-height: 0;
}

/*向下翻*/
.M-Flipper.down .front:before {
  z-index: 3;
}

.M-Flipper.down .back:after {
  z-index: 2;
  transform-origin: 50% 0%;
  transform: perspective(160px) rotateX(180deg);
}

.M-Flipper.down .front:after,
.M-Flipper.down .back:before {
  z-index: 1;
}

.M-Flipper.down.go .front:before {
  transform-origin: 50% 100%;
  animation: frontFlipDown 0.6s ease-in-out both;
  box-shadow: 0 -2px 6px rgba(255, 255, 255, 0.3);
  backface-visibility: hidden;
}

.M-Flipper.down.go .back:after {
  animation: backFlipDown 0.6s ease-in-out both;
}

/*向上翻*/
.M-Flipper.up .front:after {
  z-index: 3;
}

.M-Flipper.up .back:before {
  z-index: 2;
  transform-origin: 50% 100%;
  transform: perspective(160px) rotateX(-180deg);
}

.M-Flipper.up .front:before,
.M-Flipper.up .back:after {
  z-index: 1;
}

.M-Flipper.up.go .front:after {
  transform-origin: 50% 0;
  animation: frontFlipUp 0.6s ease-in-out both;
  box-shadow: 0 2px 6px rgba(255, 255, 255, 0.3);
  backface-visibility: hidden;
}

.M-Flipper.up.go .back:before {
  animation: backFlipUp 0.6s ease-in-out both;
}

@keyframes frontFlipDown {
  0% {
    transform: perspective(160px) rotateX(0deg);
  }

  100% {
    transform: perspective(160px) rotateX(-180deg);
  }
}

@keyframes backFlipDown {
  0% {
    transform: perspective(160px) rotateX(180deg);
  }

  100% {
    transform: perspective(160px) rotateX(0deg);
  }
}

@keyframes frontFlipUp {
  0% {
    transform: perspective(160px) rotateX(0deg);
  }

  100% {
    transform: perspective(160px) rotateX(180deg);
  }
}

@keyframes backFlipUp {
  0% {
    transform: perspective(160px) rotateX(-180deg);
  }

  100% {
    transform: perspective(160px) rotateX(0deg);
  }
}

.M-Flipper .number0:before,
.M-Flipper .number0:after {
  content: '0';
}

.M-Flipper .number1:before,
.M-Flipper .number1:after {
  content: '1';
}

.M-Flipper .number2:before,
.M-Flipper .number2:after {
  content: '2';
}

.M-Flipper .number3:before,
.M-Flipper .number3:after {
  content: '3';
}

.M-Flipper .number4:before,
.M-Flipper .number4:after {
  content: '4';
}

.M-Flipper .number5:before,
.M-Flipper .number5:after {
  content: '5';
}

.M-Flipper .number6:before,
.M-Flipper .number6:after {
  content: '6';
}

.M-Flipper .number7:before,
.M-Flipper .number7:after {
  content: '7';
}

.M-Flipper .number8:before,
.M-Flipper .number8:after {
  content: '8';
}

.M-Flipper .number9:before,
.M-Flipper .number9:after {
  content: '9';
}
</style>
