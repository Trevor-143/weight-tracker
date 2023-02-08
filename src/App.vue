<script setup>
  import { ref, shallowRef, computed, watch, nextTick } from 'vue'
  import Chart from 'chart.js/auto'

  const weights = ref([])
  const weightChartEl = ref([])
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
  watch(weights, newWeights => {
    const ws = [...newWeights]
    if (weightChart.value) {
      weightChart.value.data.labels = ws.sort((a, b) => a.date - b.date).map(w => new Date(w.date).toLocaleDateString()).slice(-7)
      weightChart.value.data.datasets[0].data = ws.sort((a, b) => a.date - b.date).map(w => w.weight).slice(-7)
      weightChart.value.update()
      return
    }
    nextTick(() => {
      weightChart.value = new Chart(weightChartEl.value.getContext('2d'), {
        type: 'line',
        data: {
          labels: ws.sort((a, b) => a.date - b-date).map(w => new Date(w.date).toLocaleDateString()),
          datasets: [
            {
              label: 'weight',
              data: ws.sort((a, b) => a.date - b.date).map(w => w.weight),
              backgroundColor: 'rgba(55, 39, 122, 0.4)',
              borderColor: 'rgb(55, 39, 122)',
              borderWidth: 1,
              fill: true
            }
          ]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false
        }
      })
    })
  }, {deep:true})
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
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'montserrat', sans-serif;
  }
  body {
    background-color: #eee;
  }
  main {
    padding: 1.5rem;
  }
  h1 {
    font-size: 2em;
    text-align: center;
    margin-bottom: 2rem;
  }
  h2 {
    margin-bottom: 1rem;
    color: #888;
    font-weight: 400;
  }
  .current {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 200px;
    height: 200px;
    
    text-align: center;
    background-color: white;
    border-radius: 999px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    border: 5px solid #37277a;
    
    margin: 0 auto 2rem;
  }
  .current span {
    display: block;
    font-size: 2em;
    font-weight: bold;
    margin-bottom: 0.5rem;
  }
  .current small {
    color: #888;
    font-style: italic;
  }
  form {
    display: flex;
    margin-bottom: 2rem;
    border: 2px solid rgba(170, 170, 170, 0.5);
    border-radius: 99px;
    overflow: hidden;
    transition: 200ms linear;

  }
  form:focus-within,
  form:hover {
    border-color: #37277a;
    border-width: 2px;
  }
  form input[type="number"] {
    appearance: none;
    outline: none;
    border: none;
    background-color: white;
    flex: 1 1 0%;
    padding: 1rem 1.5rem;
    font-size: 1.25rem;
  }
  form input[type="submit"] {
    appearance: none;
    outline: none;
    border: none;
    cursor: pointer;
    background-color: #37277a;
    padding: 0.5rem 1rem;
    color: white;
    font-size: 1.25rem;
    font-weight: 700;
    transition: 200ms linear;
    border-left: 3px solid transparent;
  }
  form input[type="submit"]:hover {
    background-color: white;
    color: #37277a;
    border-left-color: #37277a;
  }
  .canvas-box {
    width: 100%;
    max-width: 720px;
    background-color: white;
    padding: 1rem;
    border-radius: 0.5rem;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    margin-bottom: 2rem;
  }
  .weight-history ul {
    list-style: none;
    padding: 0;
    margin: 0;
  }
  .weight-history ul li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0.5rem 2rem;
    cursor: pointer;
    border-radius: 99px;
  }
  .weight-history ul li:nth-child(odd) {
    background-color: #bac4ff;
  }
  .weight-history ul li:nth-child(even) {
    background-color: #212a5e;
    color: #fff;
    margin: 10px 0;
  }
  .weight-history ul li:nth-child(even):hover {
    color: #4e4e4e;
  }
  .weight-history ul li:hover {
    background-color: #f8f8f8;
  }
  .weight-history ul li:last-of-type {
    border-bottom: none;
  }
  .weight-history ul li span {
    display: block;
    font-size: 1.25rem;
    font-weight: 700;
    margin-right: 1rem;
  }
  .weight-history ul li small {
    color: #888;
    font-style: italic;
  }
</style>
