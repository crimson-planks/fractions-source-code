<script setup lang="ts">
import { ref } from 'vue'

const props = defineProps<{ size: number }>()
const emit = defineEmits(['numeratorClicked','denominatorClicked'])

const fractionNumberStyle = { fontSize: props.size + 'px' }

const numerator = ref(1n)
function clickNumerator() {
  numerator.value++
  reduceFraction();
  emit("numeratorClicked");
}
const denominator = ref(1n)
function clickDenominator() {
  denominator.value++
  reduceFraction()
  emit("denominatorClicked");
}
function gcdF(a: bigint, b: bigint) {
  if (b === 0n) return a
  return gcdF(b, a % b)
}
function reduceFraction() {
  const gcd = gcdF(numerator.value, denominator.value)
  if (gcd != 1n) {
    numerator.value /= gcd
    denominator.value /= gcd
  }
}
</script>
<template>
  <div class="fraction">
    <div
      class="fraction-number fraction-numerator"
      @click="clickNumerator()"
      :style="fractionNumberStyle"
    >
      {{ numerator }}
    </div>
    <img class="fraction-bar" src="../assets/fraction_bar.svg" />
    <div
      class="fraction-number fraction-denominator"
      @click="clickDenominator()"
      :style="fractionNumberStyle"
    >
      {{ denominator }}
    </div>
  </div>
</template>
<style>
.fraction {
  width: min-content;
}
.fraction-number {
  width: min-content;
  margin: 0 auto;
}
.fraction-number:hover {
  cursor: pointer;
}
.fraction-bar {
  display: block;
  width: 100%;
  height: 5%;
  margin-top: 10px;
  margin-bottom: 10px;
}
</style>
