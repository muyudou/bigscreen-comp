<template>
  <div class="FlipNumber">
    <Flipper v-for="n in 10" :key="n" :ref="(el: refItem) => setRefMap(el)" />
  </div>
</template>

<script setup lang="ts">
import {onMounted, onUnmounted, defineExpose } from 'vue';
import Flipper from './Flipper.vue';

const props = defineProps({
  initNum: {
    type: Number,
    default: 0,
  }
});

type refItem = FlipperCtx | null;
const flipObjs: (FlipperCtx | null)[] = [];
const setRefMap = (el: refItem) => {
  if (el) {
	flipObjs.push(el);
  }
};

const SUM_COUNT = 90;

type FlipperCtx = InstanceType<typeof Flipper>;


let flipObjsLen = 9;

onMounted(() => {
	console.log('flipper', flipObjs);
	flipObjsLen = flipObjs.length;
	// run(0, props.initNum);
})

let timer: ReturnType<typeof setTimeout>;
let initNumStr = '';

// 获取动画相关的step、duration，差值，翻牌类型
function getAnmiInfo(initNum: number, nextNum: number) {
	// 15min翻完，每s翻一次，可以翻900次，用diff除以900，如果不能整除，则余数加到剩余的最后余数次数，每次多加1
	const diff = Math.abs(nextNum - initNum);
	const flipType = nextNum - initNum > 0 ? 'down' : 'up';
	// 如果diff大于SUM_COUNT，则1s翻一次，否则间隔改为SUM_COUNT / diff
	let duration = 1000;
	let step = Math.floor(diff / SUM_COUNT);
	const left = diff % SUM_COUNT;
	if (diff < SUM_COUNT) {
		duration = SUM_COUNT / diff * 1000;
		step = 1;
	}
	return { step, duration, left, diff, flipType };
}

// 
function setFlipper(nextShowNum: number, nowStr: string, flipType: string) {
	// 转为字符串
	let nextTimeStr = nextShowNum + '';
	let index = flipObjsLen;
	for (let i = nextTimeStr.length - 1; i >= 0 && index >= 0; i--) {
		index--;
		if (nowStr[i] === nextTimeStr[i]) {
			continue;
		}
		if (flipType === 'down') {
			flipObjs[index]?.flipDown(
				nowStr[i],
				nextTimeStr[i]
			)
		} else {
			flipObjs[index]?.flipUp(
				nowStr[i],
				nextTimeStr[i]
			)
		}
	}
}


const isFlipDown = (flipType: string) => flipType === 'down';

function setNextNum(flipType: string, cur: number, step: number) {
	return isFlipDown(flipType) ? cur + step : cur - step;
}

function run(initNum: number, nextNum: number) {
	clearInterval(timer);
	const { step, duration, left, diff, flipType } = getAnmiInfo(initNum, nextNum);

	let nowStr = initNumStr;
	// 下一个动画显示的数字
	let nextShowNum = initNum;
	let counter = 0;
	timer = setInterval(() => {
		// 如果diff大于SUM_COUNT可能会有余数，余数加到后面的次数，如果diff小于SUM_COUNT，每次动画+1;
		if (diff > SUM_COUNT) {
			if (counter < SUM_COUNT - left) {
				nextShowNum = setNextNum(flipType, nextShowNum, step);
			} else {
				nextShowNum = setNextNum(flipType, nextShowNum, step + 1);
			}
		} else {
			nextShowNum = setNextNum(flipType, nextShowNum, step);
		}
		// 如果显示的数字超过了最终显示的数字，则取消定时器
		if (isFlipDown(flipType) && nextShowNum >= nextNum
			|| !isFlipDown(flipType) && nextShowNum <= nextNum) {
			clearInterval(timer);
		}
		counter++;
		// console.log('nextShowNum', nextShowNum, nextTimeStr);
		// 设置翻牌器的数字
		setFlipper(nextShowNum, nowStr, flipType);
		nowStr = nextShowNum + '';
	}, duration)
}

onUnmounted(() => {
    clearInterval(timer);
})

defineExpose({
	run
});

</script>

<style>
.FlipNumber {
    display: flex;
	align-items: center;
	justify-content: center;
	width: 700px;
	height: 122px;
	margin-bottom: 20px;
	background: #02091f;
}
.FlipNumber .M-Flipper {
    margin: 0 3px;
}
.FlipNumber em {
    display: inline-block;
    line-height: 102px;
    font-size: 66px;
    font-style: normal;
    vertical-align: top;
}
</style>
