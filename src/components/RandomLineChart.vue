<template>
  <div>
    <h1 style="color: white; text-align: center; margin-bottom: 24px">
      ${{ (totalAmount.value || 0).toLocaleString() }}
    </h1>
    <button @click="generateRandomChart">Generate Random Chart</button>
    <div style="width: 900px; height: 500px">
      <Line :data="chartData.value" :options="chartOptions.value" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { Line } from "vue-chartjs";
import {
  Chart,
  LineElement,
  PointElement,
  LinearScale,
  Title,
  CategoryScale,
  Tooltip,
  Legend,
} from "chart.js";

Chart.register(
  LineElement,
  PointElement,
  LinearScale,
  Title,
  CategoryScale,
  Tooltip,
  Legend
);

const months = 12;
const labels = ref([]);
const data = ref([]);
const chartData = ref({ labels: [], datasets: [] });
const chartOptions = ref({
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { display: true },
    title: { display: true, text: "Monthly MRR Growth" },
  },
  scales: {
    y: {
      title: {
        display: true,
        text: "Dollars",
      },
      beginAtZero: true,
    },
    x: {
      title: {
        display: true,
        text: "Month",
      },
    },
  },
});

const totalAmount = computed(() => {
  if (data.value.length === 0) return 0;
  return data.value[data.value.length - 1];
});

function generateRandomChart() {
  labels.value = [];
  data.value = [];
  let mrr = 0;
  for (let i = 1; i <= months; i++) {
    labels.value.push(`Month ${i}`);
    mrr += Math.floor(Math.random() * 1001); // 0-1000 increase
    data.value.push(mrr);
  }
  chartData.value = {
    labels: [...labels.value],
    datasets: [
      {
        label: "MRR",
        data: [...data.value],
        borderColor: "rgba(75, 192, 192, 1)",
        backgroundColor: "rgba(75, 192, 192, 0.2)",
        fill: true,
        tension: 0.4,
      },
    ],
  };
}

onMounted(() => {
  generateRandomChart();
});
</script>
