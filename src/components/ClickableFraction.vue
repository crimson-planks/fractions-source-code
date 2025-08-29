<script setup lang="ts">
import { ref, watch } from 'vue'

const props = defineProps<{ size: number; p_numerator: bigint; p_denominator: bigint }>()
const emit = defineEmits(['update'])
//const model = defineModel<{numerator: bigint, denominator: bigint}>({required: true})
const numerator = ref(1n)
const denominator = ref(1n)
const fractionNumberStyle = { fontSize: props.size + 'px' }

watch(
  () => props.p_numerator,
  (newValue, oldValue) => {
    //console.log(`numerator changed: ${oldValue} -> ${newValue}`)
    if (oldValue != newValue) numerator.value = newValue
  },
)
watch(
  () => props.p_denominator,
  (newValue, oldValue) => {
    //console.log(`denominator changed: ${oldValue} -> ${newValue}`)
    if (oldValue != newValue) denominator.value = newValue
  },
)
function clickNumerator() {
  numerator.value++
  reduceFraction()
  emit('update', { numerator: numerator.value, denominator: denominator.value })
}
function clickDenominator() {
  denominator.value++
  reduceFraction()
  emit('update', { numerator: numerator.value, denominator: denominator.value })
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
