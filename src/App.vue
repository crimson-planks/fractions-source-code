<script setup lang="ts">
import Fraction from './components/Fraction.vue';
</script>

<template>
  <Fraction id="primary-fraction"/>
</template>

<style>
#primary-fraction{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
