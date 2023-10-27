<template>
  <div class="vueBox">
    <div class="marquee" :style="{ height: height + 'px' }">
      <div class="marquee_box">
        <div class="title" :style="{ height: headerHeight + 'px', lineHeight: headerHeight + 'px' }">
          <div v-for="item in headerList">{{ item }}</div>
        </div>
        <ul
          ref="listRef"
          :class="['marquee_list', animate ? 'marquee_top' : '']"
        >
          <li
            v-for="(item, index) in list"
            :class="'item' + item.index"
            :key="index"
            @mouseenter="handleStop()"
            @mouseleave="handleUp()"
          >
            <div class="name">
              <div class="no">{{ item.index }}</div>
              <div>{{ item.name }}</div>
            </div>
            <span> {{ item.amount }} </span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch, computed, PropType } from "vue";

interface IListItem {
    name: string,
    amount: number,
    index?: number
}

const props = defineProps({
    hasAnim:{
        type: Boolean,
        default: true
    },
  marqueeList: {
    type: Array as PropType<IListItem[]>,
    default: () => {
      return [];
    },
  },
  headerList: {
    type: Array as PropType<string[]>,
    default: () => {
      return [];
    },
  },
  waitTime: {
    type: Number,
    required: false,
    default: 2000,
  },
  animTime: {
    type: Number,
    required: false,
    default: 500,
  },
  itemHeight: {
    type: Number,
    default: 36,
  },
  headerHeight: {
    type: Number,
    default: 36,
  },
  showNumber: {
    type: Number,
    default: 4,
  },
});

// 计算容器高度
const height = computed(() => {
  let sum = props.itemHeight * props.showNumber;
  if (props.headerList) {
    sum += props.headerHeight || props.itemHeight;
  }
  return sum;
});

const list = computed(() => {
    let count = 0;
    return props.marqueeList.map((item:IListItem) => {
        item.index = ++count;
        return item;
    });
});

let animate = ref(false);
let timer: number;
let listRef = ref<HTMLInputElement | null>(null);

watch(animate, (cur: boolean) => {
    if (!listRef.value) {
        return;
    }
    if (cur) {
        listRef.value.style.marginTop = `-${props.itemHeight}px`;
    } else {
        listRef.value.style.marginTop = '0';
    }
});

onMounted(() => {
    if (props.hasAnim) {
        startAnim();
    }
});

onUnmounted(() => {
  stopAnim();
});
function showMarquee() {
  animate.value = true;
  setTimeout(() => {
    list.value.push(list.value[0]);
    list.value.shift();
    animate.value = false;
  }, props.animTime);
}

function startAnim() {
  timer = setInterval(showMarquee, props.waitTime);
}

function stopAnim() {
  clearInterval(timer);
}

function handleStop() {
  stopAnim();
}

function handleUp() {
  startAnim();
}
</script>

<style scoped lang="less">
div,
ul,
li,
span,
img {
  margin: 0;
  padding: 0;
  display: flex;
  box-sizing: border-box;
}

.vueBox {
    padding: 0 6px;
    width: 700px;
}

.marquee {
  width: 100%;
  height: 308px;
  color: #fff;
  box-sizing: border-box;
  font-family: PingFangSC-Medium, PingFang SC;
  font-weight: 500;
  background: #02091f;
}

.marquee_box {
  display: block;
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.title {
  position: relative;
  z-index: 10;
  display: flex;
  padding: 0 10px 0 66px;
  justify-content: space-between;
  background: #02091f;
}

.marquee_list {
  display: block;
  width: 100%;
  position: absolute;
  top: 36px;
  left: 0;
  z-index: 0;
  overflow: hidden;
}

.marquee_top {
  transition: all 0.5s ease-out;
  z-index: 0;
}

.marquee_list li {
  position: relative;
  justify-content: space-between;
  align-items: center;
  height: 36px;
  line-height: 36px;
  font-size: 12px;
  color: #fff;
  padding: 0 10px 0 26px;
  border-radius: 5px;
  .no {
    display: flex;
    align-self: center;
    justify-content: center;
    margin-right: 20px;
    width: 22px;
    height: 22px;
    line-height: 22px;
    background: url("../assets/iconbg.png") no-repeat;
    background-size: 100%;
  }
}
.item1,
.item3,
.item5,
.item7,
.item9 {
  background: rgba(75, 133, 241, 0.12);
}

.marquee_list li span {
  padding: 0 2px;
}
</style>
