<script setup lang="ts">
import { ref } from 'vue';

const numerator=ref(1n)
function clickNumerator(){
  numerator.value++;
  reduceFraction();
}
const denominator=ref(1n)
function clickDenominator(){
  denominator.value++;
  reduceFraction();
}
function gcdF(a:bigint,b:bigint){
    if(b===0n) return a;
    return gcdF(b, a%b);
}
function reduceFraction(){
  let gcd = gcdF(numerator.value,denominator.value);
  if(gcd!=1n){
    numerator.value/=gcd;
    denominator.value/=gcd;
  }
}
</script>
<template>
  <div class="fraction">
  <div class="fraction-number fraction-numerator" @click="clickNumerator()">{{ numerator }}</div>
  <img class="fraction-bar" src="../assets/fraction_bar.svg">
  <div class="fraction-number fraction-denominator" @click="clickDenominator()">{{ denominator }}</div>
  </div>
</template>
<style>
.fraction{
  width:min-content;
}
.fraction-number{
  font-size: 72px;
  width: min-content;
  margin: 0 auto;
}
.fraction-number:hover{
  cursor: pointer;
}
.fraction-bar{
  width: 100%;
  height: 5px;
}
</style>
