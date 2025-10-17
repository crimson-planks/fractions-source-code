<script setup lang="ts">
const props = defineProps<{ size: number; numerator: bigint; denominator: bigint }>()
const fractionNumberStyle = { fontSize: props.size + 'px' }
</script>
<template>
  <div class="fraction">
    <div class="fraction-number fraction-numerator" :style="fractionNumberStyle">
      {{ props.numerator }}
    </div>
    <div class="fraction-bar"></div>

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

.fraction-bar {
  display: block;
  width: 100%;
  height: 5px;
  margin-top: 10px;
  margin-bottom: 10px;
  background-color: black;
}
</style>
