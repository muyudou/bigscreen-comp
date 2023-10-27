<script setup lang="ts">
import { ref, onMounted } from 'vue'
import DataBar from './DataBar.vue';
import Pie from './Pie.vue';
import FlipNumber from './Flipper/FlipNumber.vue';
import Marquee from './Marquee.vue';

const flipNumberRef = ref(null);

const marqueeList = [
  {
    name: "1军",
    index: "1",
    amount: "10",
    src: "../img/冠军.png",
  },
  {
    name: "2军",
    index: "2",
    amount: "20",
    src: "../img/亚军.png",
  },
  {
    name: "3军",
    index: "3",
    amount: "30",
    src: "../img/季军.png",
  },
  {
    name: "4军",
    index: "4",
    amount: "40",
  },
  {
    name: "5军",
    index: "5",
    amount: "50",
  },
  {
    name: "6军",
    index: "6",
    amount: "60",
  },
  {
    name: "7军",
    index: "7",
    amount: "70",
  },
  {
    name: "8军",
    index: "8",
    amount: "80",
  },
];

const headerList = ["名称", "金额(万元)"];

onMounted(() => {
  flipNumberRef.value?.run(0, 100000);
})

</script>

<template>
  <flip-number :initNum="1000" ref="flipNumberRef"></flip-number>
  <Marquee :marqueeList="marqueeList" :headerList="headerList" :showNumber="4"></Marquee>
  <data-bar></data-bar>
  <pie></pie>
</template>

<style scoped>
</style>
