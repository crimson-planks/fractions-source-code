<script setup lang="ts">
import Fraction from './components/Fraction.vue'
import ClickableFraction from './components/ClickableFraction.vue'
const level = 1
const levelComplete = false
</script>

<template>
  <div id="goal">
    목표: <Fraction id="goal-fraction" :size="36" :numerator="10n" :denominator="9n"></Fraction>
  </div>
  <ClickableFraction
    id="primary-fraction"
    :size="72"
    @numerator-clicked="console.log('numerator')"
    @denominator-clicked="console.log('denominator')"
  ></ClickableFraction>
  <div v-show="levelComplete">성공!</div>
</template>

<style>
* {
  font-family: 'Courier New', Courier, monospace;
}
#primary-fraction {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
#goal {
  position: absolute;
  top: 20%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: flexbox;
}
</style>
