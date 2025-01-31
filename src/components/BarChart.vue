<template>
    <div>
        <canvas ref="barChart"></canvas>
    </div>
</template>

<script>
import { Chart, registerables } from 'chart.js';

export default {
name: 'BarChart',
props: {
    chartData: {
        type: Object,
        required: true,
    },
    chartOptions: {
        type: Object,
        default: () => ({}),
    },
},
mounted() {
    Chart.register(...registerables);
    const ctx = this.$refs.barChart.getContext('2d');
    new Chart(ctx, {
        type: 'bar', // Тип диаграммы (столбчатая)
        data: this.chartData, // Данные для диаграммы
        options: this.chartOptions, // Настройки диаграммы
    });
},
};
</script>

<style scoped>
div {
    width: 100%;
}
</style>