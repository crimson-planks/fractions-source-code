<script setup lang="ts">
import Fraction from './components/Fraction.vue'
import ClickableFraction from './components/ClickableFraction.vue'
import { ref, computed, type Ref } from 'vue'

const isDebug = ref(false)
const currentScreen: Ref<'levelSelect' | 'game'> = ref('levelSelect')
const level = ref(0n)
const levelComplete: Ref<boolean[]> = ref([false, false, false, false, false])
const numerator = ref(1n)
const denominator = ref(1n)
const levelArray = ref([0n, 1n, 2n, 3n, 4n])
const levelInfo = [
  [3n, 2n],
  [4n, 3n],
  [10n, 9n],
  [5n, 8n],
  [25n, 22n],
]
const currentLevelInfo = computed(() => levelInfo[Number(level.value)])
function enterLevel(arg_level: bigint) {
  if (levelInfo[Number(arg_level)] == undefined) return
  currentScreen.value = 'game'
  console.log(`enterLevel ${arg_level}`)
  numerator.value = 1n
  denominator.value = 1n
  level.value = arg_level
}
function updatePrimaryFraction(arg_numerator: bigint, arg_denominator: bigint) {
  numerator.value = arg_numerator
  denominator.value = arg_denominator
  checkCompletion()
}
function checkCompletion() {
  if (
    numerator.value === currentLevelInfo.value[0] &&
    denominator.value === currentLevelInfo.value[1]
  ) {
    levelComplete.value[Number(level.value)] = true
  }
}
function goToNextLevel() {
  numerator.value = 1n
  denominator.value = 1n
  level.value++
}
</script>

<template>
  <button @click="isDebug = !isDebug">toggle debug mode</button>
  <div id="levelselect" v-show="currentScreen === 'levelSelect'">
    <button
      class="levelselect-button"
      :class="{
        'complete-level': levelComplete[Number(level)],
        'incomplete-level': !levelComplete[Number(level)],
      }"
      v-for="level in levelArray"
      :key="Number(level)"
      @click="enterLevel(level)"
    >
      {{ level + 1n }}
    </button>
  </div>
  <div id="game" v-show="currentScreen === 'game'">
    <div id="goal">
      <br />
      레벨 {{ level + 1n }}<br />
      목표:
      <Fraction
        id="goal-fraction"
        :size="36"
        :numerator="currentLevelInfo[0]"
        :denominator="currentLevelInfo[1]"
      ></Fraction>
    </div>
    <div id="primary-fraction-container">
      <ClickableFraction
        id="primary-fraction"
        :size="72"
        :p_numerator="numerator"
        :p_denominator="denominator"
        @update="
          (v) => {
            updatePrimaryFraction(v.numerator, v.denominator)
          }
        "
      ></ClickableFraction>
    </div>
    <div id="level-complete-div" v-show="levelComplete[Number(level)]">
      <h2>성공!</h2>
      <br />
      <button @click="goToNextLevel">다음 레벨로</button>
    </div>
    <div id="return-container">
      <button id="returnto-levelselect-button" @click="currentScreen = 'levelSelect'">
        돌아가기
      </button>
    </div>
  </div>
  <div id="debug-screen" v-show="isDebug">
    currentScreen: {{ currentScreen }}<br />
    <button @click="currentScreen = 'levelSelect'">to levelSelect</button>
    <button @click="currentScreen = 'game'">to game</button>
  </div>
</template>

<style>
* {
  font-family: 'Courier New', Courier, monospace;
}
#primary-fraction-container {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}
#goal {
  display: flex;
  flex-direction: column;
  width: 100%;
  align-items: center;
}
#goal * {
  width: fit-content;
}
#level-complete-div {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 50vw;
  height: 25vh;
  transform: translate(-50%, -50%);
  font-size: large;
  text-align: center;
  background-color: white;
  border-style: solid;
  border-color: black;
  border-width: 0.5em;
  border-radius: 10px;
}
#return-container {
  padding-top: 30px;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}
#returnto-levelselect-button {
  width: 100px;
  height: 50px;
}
.levelselect-button {
  width: 40px;
  height: 40px;
}
.complete-level {
  background-color: green;
}
.incomplete-level {
  background-color: dimgray;
}
</style>
