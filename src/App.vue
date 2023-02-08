<script setup>
  import { ref, shallowRef, computed, watch, nextTick } from 'vue'
  import Chart from 'chart.js/auto'

  const weights = ref([])
  const weightChatEl = ref([])
  const weightChart = shallowRef(null)
  const weightInput = ref(60.0)
  const currentWeight = computed(() => {
    return weights.value.sort((a, b) => b.date - a.date)[0] || {weight:0}
  })
  const addweight = () => {
    weights.value.push ({
      weight: weightInput.value,
      date: new Date().getTime() 
    })
  }
</script>

<template>

  <main>

    <h1>Weight tracker</h1>

    <div class="current">
      <span>{{ currentWeight.weight }}</span>
      <small>Current weight (kg)</small>
    </div>

    <form @submit.prevent="addweight">
      <input type="number" name="" id="" step="0.1" v-model="weightInput">
      <input type="submit" value="Add weight">
    </form>

    <div v-if="weights && weights.length > 0">
      <h3>Last 7 days</h3>
      <div class="canvas-box">
        <canvas ref="weightChartEl"></canvas>
      </div>
      <div class="weight-history">
        <h3>weight history</h3>
        <ul>
          <li v-for="weight in weights">
            <span>{{ weight.weight }}</span>
            <small>{{ new Date(weight.date).toLocaleDateString() }}</small>
          </li>
        </ul>
      </div>
    </div>
  </main>

</template>

<style scoped>

</style>
