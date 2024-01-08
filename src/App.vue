<script setup lang="ts">
import { computed, reactive, watch } from 'vue'

import ButtonKey from './components/ButtonKey.vue';

const hiddenPass = computed(() => Array(typedPass.length).fill('*').join(''))

const typedPass = reactive<Array<Array<Number>>>([])

const passList = reactive<Array<String>>([])


function generateKeyboard(numbers: number[], size: number) {
   const keyboard: Array<Array<Number>> = []
   const restNumbers = [...numbers]

   while (restNumbers.length > 0) {
      const key = restNumbers.shift()

      if (key === undefined) break

      const position = Math.floor(Math.random() * (numbers.length / size))

      if (keyboard[position] === undefined) keyboard[position] = [key]
      else if (keyboard[position].length < size) keyboard[position].push(key)
      else restNumbers.push(key)
   }

   return keyboard as Array<[number, number]>
}

const keyboard = generateKeyboard([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], 2)


let executionTime = 0
watch(typedPass, (newTypedPass) => {
   const startTime = performance.now()

   const positions = Array(newTypedPass.length).fill(0)

   let pass = ''

   passList.splice(0, passList.length)

   while (positions.includes(0)) {
      let lastZeroIndex: number = positions.findLastIndex((value) => value === 0)

      for (let index = 0; index < positions.length; index++) {
         pass += newTypedPass[index][positions[index]]
      }

      passList.push(pass)
      pass = ''

      positions[lastZeroIndex] = 1

      for (let index = 0; index < positions.length; index++) {
         if (index > lastZeroIndex) positions[index] = 0
      }
   }


   for (let index = 0; index < positions.length; index++) {
      pass += newTypedPass[index][positions[index]]
   }

   passList.push(pass)
   pass = ''

   const endTime = performance.now()
   executionTime = endTime - startTime;
}, { deep: true })

</script>

<template>
   <section>

      <input type="text" :value="hiddenPass" disabled />

      <div>
         <button id="reset" @click="() => typedPass.length = 0">Reset</button>
      </div>

      <div id="Keyboard">
         <ButtonKey v-for="keys in keyboard" :keys="keys" @click="(keys) => typedPass.push(keys)" />
      </div>
   </section>

   <span>Possibilities: {{ passList.length }} (pin length: {{ typedPass.length }} ) </span>
   <p>Execution: {{ executionTime }} ms</p>

   <div id="Result">
      {{ passList }}
   </div>
</template>

<style>
* {
   margin: 0;
   padding: 0;
   box-sizing: border-box;
}

#app {
   padding-top: 32px;
   display: flex;
   align-items: center;
   flex-direction: column;
   height: 100vh;
   text-align: center;
}

input {
   width: 100%;
   height: 32px;
   font-size: 24px;
   text-align: center;
   border: 1px solid rgb(230, 230, 230);
   border-radius: 8px;
}

#reset {
   margin: 16px;
   border-radius: 8px;
   height: 40px;
   width: 64px;
   border: none;
   background-color: rgb(207, 71, 71);
   color: #fff;
   cursor: pointer;
}

#Keyboard {
   margin-bottom: 32px;
}


#Keyboard>button {
   margin: 4px;
   border-radius: 8px;
   height: 40px;
   width: 64px;
   border: none;
   background-color: #3e52be;
   color: #fff;
   cursor: pointer;
}

#Result {
   max-width: 768px;
   display: flex;
   flex-direction: column;
   align-items: center;
   justify-content: center;
   margin-top: 32px;
}
</style>