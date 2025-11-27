<template>
  <h1>Mom, my business is fine</h1>
  <div>
    <h2 style="color: white; text-align: center; margin-bottom: 24px">
      MMR (Monthly Recurring Revenue):
      <span class="flex">${{ totalAmount.toLocaleString() }} </span>
    </h2>
    <button @click="debouncedGenerateChart">Generate Chart</button>
    <canvas ref="canvasRef" width="900" height="500"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted, nextTick } from "vue";
import {
  Chart,
  LineController,
  LineElement,
  PointElement,
  LinearScale,
  Title,
  CategoryScale,
  Tooltip,
  Legend,
  Filler,
} from "chart.js";

Chart.register(
  LineController,
  LineElement,
  PointElement,
  LinearScale,
  Title,
  CategoryScale,
  Tooltip,
  Legend,
  Filler
);

const canvasRef = ref(null);
const chartInstance = ref(null);
const months = 12;
const labels = Array.from({ length: months }, (_, i) => `Month ${i + 1}`);
const totalAmount = ref(0);
let debounceTimeout = null;

function generateRandomChart() {
  const dataArr = [];
  let mrr = 1000;
  for (let i = 0; i < months; i++) {
    mrr += Math.floor(Math.random() * 10001);
    dataArr.push(mrr);
  }
  totalAmount.value = dataArr.length ? dataArr[dataArr.length - 1] : 0;
  nextTick(() => {
    renderChart(dataArr);
  });
}

function debouncedGenerateChart() {
  if (debounceTimeout) clearTimeout(debounceTimeout);
  debounceTimeout = setTimeout(() => {
    generateRandomChart();
  }, 500);
}

function renderChart(dataArr) {
  if (!canvasRef.value) return;
  if (
    chartInstance.value &&
    typeof chartInstance.value.destroy === "function"
  ) {
    try {
      chartInstance.value.destroy();
    } catch (e) {
      chartInstance.value = null;
    }
  }
  chartInstance.value = null;
  chartInstance.value = new Chart(canvasRef.value, {
    type: "line",
    data: {
      labels,
      datasets: [
        {
          label: "MRR",
          data: dataArr,
          borderColor: "rgba(75, 192, 192, 1)",
          backgroundColor: "rgba(75, 192, 192, 0.6)",
          fill: true,
          tension: 0.4,
        },
      ],
    },
    options: {
      responsive: false,
      plugins: {
        legend: { display: true },
        title: { display: true, text: "Monthly MRR Growth" },
      },
      scales: {
        y: {
          title: { display: true, text: "Dollars" },
          beginAtZero: true,
        },
        x: {
          title: { display: true, text: "Month" },
        },
      },
    },
  });
}

onMounted(() => {
  generateRandomChart();
});
</script>
