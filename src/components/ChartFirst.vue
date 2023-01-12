<template>
  <Bar :data="chartData" :options="options" />
</template>

<script>
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";
import { Bar } from "vue-chartjs";

ChartJS.register(
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend
);

const chartUrl = "https://service-apm.ru/test_json.php";
const paramsFetch = {
  method: "GET", // HTTP request method
  referrerPolicy: "no-referrer",
  headers: {},
  credentials: "same-origin",
};
const timeout = 10000;

export default {
  name: "ChartFirst",
  components: { Bar },

  data: () => ({
    options: { responsive: true },
    phpLabels: [],
    phpData: [],
  }),

  created() {
    this.getChartData();
  },

  methods: {
    async getChartData() {
      const response = await fetch(chartUrl, paramsFetch);
      const text = await response.text();
      if (!response.ok) throw Error(`ошибка ${response.status} ${text}`);
      else if (text) {
        const chartData = JSON.parse(text);
        this.phpLabels = Object.keys(chartData.data);
        this.phpData = Object.values(chartData.data);
      }
      setTimeout(() => {
        this.getChartData();
      }, timeout);
    },
  },

  computed: {
    chartData() {
      return {
        labels: this.phpLabels,
        datasets: [
          {
            label: "ChartFirst",
            data: this.phpData,
            borderWidth: 1,
          },
        ],
      };
    },
  },
};
</script>
