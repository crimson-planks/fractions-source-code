<script setup lang="ts">
import Fraction from './components/Fraction.vue'
import ClickableFraction from './components/ClickableFraction.vue'
import { ref, computed, type Ref } from 'vue'

const isDebug = ref(false)
const currentScreen: Ref<'levelSelect' | 'game'> = ref('levelSelect')
const currentLevel = ref(0n)
const levelComplete: Ref<boolean> = ref(false)
const numerator = ref(1n)
const denominator = ref(1n)
const moveAmount = ref(0n)
const levelArray = ref([0n, 1n, 2n, 3n, 4n, 5n])
const levelInfo = [
  [3n, 2n],
  [4n, 3n],
  [10n, 9n],
  [5n, 8n],
  [25n, 22n],
  [3n, 2n],
]
const playerInfo: Ref<{ moveAmount: bigint; levelComplete: boolean }[]> = ref([])
for (let i = 0; i <= 5; i++) playerInfo.value[i] = { moveAmount: 0n, levelComplete: false }
const currentLevelInfo = computed(() => levelInfo[Number(currentLevel.value)])
function enterLevel(arg_level: bigint) {
  if (levelInfo[Number(arg_level)] == undefined) return
  currentScreen.value = 'game'
  console.log(`enterLevel ${arg_level}`)
  numerator.value = 1n
  denominator.value = 1n
  currentLevel.value = arg_level
}
function exitLevel() {
  resetLevel()
  currentScreen.value = 'levelSelect'
}
function updatePrimaryFraction(
  arg_numerator: bigint,
  arg_denominator: bigint,
  arg_moveAmount: bigint,
) {
  numerator.value = arg_numerator
  denominator.value = arg_denominator
  moveAmount.value = arg_moveAmount
  checkCompletion()
}
function resetLevel() {
  levelComplete.value = false
  numerator.value = 1n
  denominator.value = 1n
  moveAmount.value = 0n
  checkCompletion()
}
function checkCompletion() {
  if (
    numerator.value === currentLevelInfo.value[0] &&
    denominator.value === currentLevelInfo.value[1]
  ) {
    levelComplete.value = true
    playerInfo.value[Number(currentLevel.value)].levelComplete = true
  }
}
function goToNextLevel() {
  resetLevel()
  currentLevel.value++
}
</script>

<template>
  <button @click="isDebug = !isDebug">toggle debug mode</button>
  <div id="levelselect" v-show="currentScreen === 'levelSelect'">
    <button
      class="levelselect-button"
      :class="{
        'complete-level': playerInfo[Number(level)].levelComplete,
        'incomplete-level': !playerInfo[Number(level)].levelComplete,
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
      레벨 {{ currentLevel + 1n }}<br />
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
        :p_move-amount="moveAmount"
        @update="
          (v) => {
            updatePrimaryFraction(v.numerator, v.denominator, v.moveAmount)
          }
        "
      ></ClickableFraction>
      <div class="move-amount-div">횟수: {{ moveAmount }}</div>
    </div>
    <div id="level-complete-div" v-show="levelComplete">
      <h2>성공!</h2>
      <br />
      <button v-show="levelInfo[Number(currentLevel) + 1] != undefined" @click="goToNextLevel">
        다음 레벨로
      </button>
      <button @click="resetLevel()">다시하기</button>
      <button @click="exitLevel()">돌아가기</button>
      <div class="move-amount-div">횟수: {{ moveAmount }}</div>
      <div class="min-move-amount-div">최소 횟수: {{ moveAmount }}</div>
    </div>
    <div id="return-container">
      <button id="returnto-levelselect-button" @click="exitLevel()">돌아가기</button>
      <button @click="resetLevel()">리셋</button>
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
  height: 50vh;
  transform: translate(-50%, -50%);
  font-size: large;
  text-align: center;
  background-color: #ffffff;
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
#return-container * {
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
.move-amount-div {
  font-size: 20px;
}
.min-move-amount-div {
  font-size: 20px;
}
.incomplete-level {
  background-color: dimgray;
}
</style>
