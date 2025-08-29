<script setup lang="ts">
const props = defineProps<{ size: number; numerator: bigint; denominator: bigint }>()
const fractionNumberStyle = { fontSize: props.size + 'px' }
</script>
<template>
  <div class="fraction">
    <div class="fraction-number fraction-numerator" :style="fractionNumberStyle">
      {{ props.numerator }}
    </div>
    <img class="fraction-bar" src="../assets/fraction_bar.svg" />
    <div class="fraction-number fraction-denominator" :style="fractionNumberStyle">
      {{ props.denominator }}
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
  height: 5px;
  margin-top: 10px;
  margin-bottom: 10px;
}
</style>
