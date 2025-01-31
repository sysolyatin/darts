<template>
    <div class="win bg-success text-center p-5">
        <h2>игра завершена, победитель</h2>
        <h1 class="text-warning display-1 mb-5">{{ name }}</h1>
        <button class="btn btn-dark btn-lg me-5" @click="newGame">Новая игра</button>
        <button class="btn btn-dark btn-lg" @click="newGameSavePlayers">Такая же игра</button>


        <div class="row mt-5" v-if="winners.length > 0">
            <div class="col-md-4 text-start">
                <h3>История победителей:</h3>
                <table class="table">
                    <tr v-for="winner in winners.sort((a, b) => a.time < b.time ? 1 : -1)">
                        <td>{{ getDateString(winner.time) }}</td>
                        <td>{{ winner.name }}</td>
                    </tr>
                </table>
            </div>
            <div class="col-md-8">
                <BarChart :chartData="chartData" :chartOptions="chartOptions" />
            </div>
        </div>

    </div>
</template>
  
<script>
import BarChart from './BarChart.vue';

export default {
    components: {
        BarChart,
    },
    name: 'WinMessage',
    props: {
        name: String
    },
    data() {
        return {
            winners: [],
            chartData: {},
            chartOptions: {
                responsive: true,
                plugins: {
                    legend: {
                        display: false,
                    },
                },
                scales: {
                    x: {
                        ticks: {
                            color: 'white',
                        },
                    },
                    y: {
                        ticks: {
                            color: 'white',
                        },
                        beginAtZero: true,
                    },
                },
            },
        }
    },
    methods: {
        newGame() {
            this.$emit("newGame", true);
        },
        newGameSavePlayers() {
            this.$emit("newGame", false);
        },
        getDateString(timestamp) {
            const date = new Date(timestamp);
            const year = date.getFullYear();

            const month = date.getMonth() + 1;
            const strMonth = month < 10 ? `0${month}` : `${month}`;

            const day = date.getDate();
            const strDay = day < 10 ? `0${day}` : `${day}`;

            const hours = date.getHours();
            const strHours = hours < 10 ? `0${hours}` : `${hours}`;

            const minutes = date.getMinutes();
            const strMinutes = minutes < 10 ? `0${minutes}` : `${minutes}`;

            const seconds = date.getSeconds();
            const strSeconds = seconds < 10 ? `0${seconds}` : `${seconds}`;

            return `${strDay}.${strMonth}.${year} ${strHours}:${strMinutes}:${strSeconds}`;
        }
    },
    mounted: function() {
        let winnersData = localStorage.getItem('winners');
        this.winners = winnersData ? JSON.parse(winnersData) : [];
        this.winners.push({time: Date.now(), name: this.name});
        localStorage.setItem('winners', JSON.stringify(this.winners));

        let winStats = {};
        for (let i = 0; i < this.winners.length; i++) {
            let winner = this.winners[i];
            let winnerStat = winStats[winner.name];
            winStats[winner.name] = winnerStat ? winnerStat + 1 : 1
        }

        let labels = [];
        let chartData = [];

        for (var statPlayerName in winStats) {
            labels.push(statPlayerName);
            chartData.push(winStats[statPlayerName]);
        }

        this.chartData = {
            labels: labels,
            datasets: [
                {
                    label: 'Победы',
                    data: chartData,
                    backgroundColor: 'rgba(255, 193, 7, 1)',
                    borderColor: 'rgba(255, 255, 255, 1)',
                    borderWidth: 0,
                },
            ],
        }
    }
}
</script>
  
<style scoped>
.win {
    width: 100%;
    min-height: 100vh;
}
</style>
  