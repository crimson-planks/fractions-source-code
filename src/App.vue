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
const startTime = ref(0)
const completed시각 = ref(0)
const currentTime = ref(0)
const levelInfo = [
  [3n, 2n],
  [4n, 3n],
  [10n, 9n],
  [5n, 8n],
  [25n, 22n],
  [67n, 21n],
]
const playerLevelInfo: Ref<
  {
    moveAmount: bigint
    minTime: number | null
    minMoveAmount: bigint | null
    levelComplete: boolean
  }[]
> = ref([])
for (let i = 0; i <= 5; i++)
  playerLevelInfo.value[i] = {
    moveAmount: 0n,
    minTime: null,
    minMoveAmount: null,
    levelComplete: false,
  }
const currentLevelInfo = computed(() => levelInfo[Number(currentLevel.value)])
const complete시간 = computed(() => (completed시각.value - startTime.value) / 1000)
const elapsedTime = computed(() => (currentTime.value - startTime.value) / 1000)
/* const playerInfo = {
  isDebug,
  currentScreen,
  currentLevel,
  levelComplete,
  numerator,
  denominator,
  moveAmount,
  playerLevelInfo,
}*/
type Save = {
  isDebug: boolean
  currentScreen: typeof currentScreen.value
  currentLevel: bigint
  levelComplete: boolean
  numerator: bigint
  denominator: bigint
  moveAmount: bigint
  playerLevelInfo: typeof playerLevelInfo.value
}

function exportSave(): Save {
  return {
    isDebug: isDebug.value,
    currentScreen: currentScreen.value,
    currentLevel: currentLevel.value,
    levelComplete: levelComplete.value,
    numerator: numerator.value,
    denominator: denominator.value,
    moveAmount: moveAmount.value,
    playerLevelInfo: playerLevelInfo.value,
  }
}
function importSave(save: Save) {
  isDebug.value = save.isDebug
  currentScreen.value = save.currentScreen
  currentLevel.value = save.currentLevel
  levelComplete.value = save.levelComplete
  numerator.value = save.numerator
  denominator.value = save.denominator
  moveAmount.value = save.moveAmount
  playerLevelInfo.value = save.playerLevelInfo
}
function enterLevel(arg_level: bigint) {
  if (levelInfo[Number(arg_level)] == undefined) return
  currentScreen.value = 'game'
  console.log(`enterLevel ${arg_level}`)
  resetLevel()
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
  startTime.value = Date.now()
  checkCompletion()
}
function checkCompletion() {
  if (
    numerator.value === currentLevelInfo.value[0] &&
    denominator.value === currentLevelInfo.value[1]
  ) {
    levelComplete.value = true
    playerLevelInfo.value[Number(currentLevel.value)].levelComplete = true
    completed시각.value = Date.now()
    const pic = playerLevelInfo.value[Number(currentLevel.value)]
    if (pic.minMoveAmount === null || moveAmount.value < pic.minMoveAmount)
      pic.minMoveAmount = moveAmount.value

    if (pic.minTime === null || complete시간.value < pic.minTime) pic.minTime = complete시간.value
  }
}
function goToNextLevel() {
  resetLevel()
  currentLevel.value++
}
setInterval(() => {
  currentTime.value = Date.now()
}, 20)
</script>

<template>
  <button style="display: none" @click="isDebug = !isDebug">toggle debug mode</button>
  <div id="levelselect" v-show="currentScreen === 'levelSelect'">
    <button
      class="levelselect-button"
      :class="{
        'complete-level': playerLevelInfo[Number(level)].levelComplete,
        'incomplete-level': !playerLevelInfo[Number(level)].levelComplete,
      }"
      v-for="level in levelArray"
      :key="Number(level)"
      @click="enterLevel(level)"
    >
      {{ level + 1n }}
    </button>
  </div>
  <div id="game" v-show="currentScreen === 'game'">
    <div id="level-title">레벨 {{ currentLevel + 1n }}</div>
    <div id="goal">
      <div id="goal-box">
        <span id="goal-text">목표</span>
        <Fraction
          id="goal-fraction"
          :size="24"
          :numerator="currentLevelInfo[0]"
          :denominator="currentLevelInfo[1]"
        ></Fraction>
      </div>
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
      <div>시간: {{ elapsedTime }}</div>
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
      <div>걸린 시간: {{ complete시간 }} 초</div>
      <div class="min-move-amount-div">
        최소 횟수: {{ playerLevelInfo[Number(currentLevel)].minMoveAmount }}
      </div>
      <div class="min-time-div">
        최소 걸린 시간: {{ playerLevelInfo[Number(currentLevel)].minTime }} 초
      </div>
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
#game {
  text-align: center;
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
  align-items: center;
}
#goal * {
  min-width: fit-content;
}
#goal-box {
  text-align: center;
  width: fit-content;
  border-radius: 5px;
  border-style: dashed;
  border-color: black;
}
#goal-fraction {
  width: 100%;
}
#level-title {
  font-size: 50px;
}
#goal-text {
  font-size: 30px;
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
.min-time-div {
  font-size: 20px;
}
.incomplete-level {
  background-color: dimgray;
}
</style>
