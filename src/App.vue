<template>
    <WinMessage :name="winner" @newGame="newGame" v-if="winner !== ''" />

    <NewGame v-if="players.length === 0 && winner === ''" @startGame="startGame" :historyNames="historyNames"/>

    <div class="container-fluid" v-if="players.length !== 0 && winner === ''">
        <div class="tablo p-2">
            <table class="table table-bordered border-success h1 mb-4">
                <tr v-for="player in players" v-bind:class="{'bg-success': player.id === currentPlayerId}">
                    <td>{{ player.name }}</td>
                    <td>{{ player.scoore }}</td>
                </tr>
            </table>
            <div class="row mb-4">
                <div class="col-md-3 h1 text-center p-4 m-1 ms-2" 
                    v-bind:class="{'bg-danger': currentShotNumber === 1, 'bg-warning': currentShotNumber !== 1}">{{ shot1 }}</div>
                <div class="col-md-3 h1 text-center p-4 m-1" 
                    v-bind:class="{'bg-danger': currentShotNumber === 2, 'bg-warning': currentShotNumber !== 2}">{{ shot2 }}</div>
                <div class="col-md-3 h1 text-center p-4 m-1" 
                    v-bind:class="{'bg-danger': currentShotNumber === 3, 'bg-warning': currentShotNumber !== 3}">{{ shot3 }}</div>
                <div class="col-md-2 h1 text-center p-4 text-success">{{ totalSum }}</div> 
            </div>
            <button class="btn btn-success btn-lg me-2" @click="finishMove">Завершить ход</button>  
            <button class="btn btn-warning btn-lg" @click="clearShots">Ввести ещё раз</button>

            <div class="currentPlayerScoore">{{ players[currentPlayerId].scoore }} <span>{{ players[currentPlayerId].scoore - totalSum }}</span></div>

        
            <table class="table table-hover" v-if="results.length > 0">
                <thead>
                    <tr>
                        <th>Имя игрока</th>
                        <th>1</th>
                        <th>2</th>
                        <th>3</th>
                        <th>Итого</th>
                    </tr>
                </thead>
                <tr v-for="res in results.sort((a, b) => a.time < b.time ? 1 : -1)">
                    <td>{{ res.name }}</td>
                    <td>{{ res.shot1 }}</td>
                    <td>{{ res.shot2 }}</td>
                    <td>{{ res.shot3 }}</td>
                    <td>{{ res.total }}</td>
                </tr>

            </table>
        </div>
        <div class="target">
            <Target @shot="processShot"/>
        </div>
        <div style="clear: both;"></div>
        <div class="text-end">
            <button class="btn btn-danger btn-sm" @click="newGame(true)">Новая игра</button>
        </div>
    </div>
</template>

<script>
import Target from '@/components/Target.vue'
import NewGame from '@/components/NewGame.vue'
import WinMessage from '@/components/WinMessage.vue'

export default {
    name: 'App',
    components: {
        Target,
        NewGame,
        WinMessage
    },
    data() {
        return {
            gameMode: 501,
            players: [],
            currentPlayerId: 0,
            shot1: 0,
            shot2: 0,
            shot3: 0,
            totalSum: 0,
            currentShotNumber: 1,
            winner: "",
            results: [],
            historyNames: []
        }
    },
    methods: {
        processShot(result) {
            if (this.currentShotNumber == 1){
                this.shot1 = result;
                this.currentShotNumber = 2;
                this.totalSum = this.shot1 + this.shot2 + this.shot3;
                return;
            }
            if (this.currentShotNumber == 2){
                this.shot2 = result;
                this.currentShotNumber = 3;
                this.totalSum = this.shot1 + this.shot2 + this.shot3;
                return;
            }
            if (this.currentShotNumber == 3){
                this.shot3 = result;
                this.currentShotNumber = 1;
                this.totalSum = this.shot1 + this.shot2 + this.shot3;
                return;
            }
        },
        clearShots() {
            this.shot1 = 0;
            this.shot2 = 0;
            this.shot3 = 0;
            this.currentShotNumber = 1;
            this.totalSum = 0;
        },
        startGame(gameType, playersNames){
            for (let i = 0; i < playersNames.length; i++) {
                let playerName = playersNames[i];
                if (!this.historyNames.find(a => a === playerName)) {
                    this.historyNames.push(playerName);
                }
                this.gameMode = gameType;
                this.players.push({
                    id: i,
                    name: playerName,
                    scoore: gameType
                });
            }
            this.results = [];
            this.saveGame();
        },
        saveGame() {
            localStorage.setItem('gameMode', `${this.gameMode}`);
            localStorage.setItem('currentPlayerId', `${this.currentPlayerId}`);
            localStorage.setItem('players', JSON.stringify(this.players));
            localStorage.setItem('results', JSON.stringify(this.results));
            localStorage.setItem('historyNames', JSON.stringify(this.historyNames));
        },
        newGame(clearPlayesAndMode) {
            this.winner = "";
            this.shot1 = 0;
            this.shot2 = 0;
            this.shot3 = 0;
            this.totalSum = 0;
            this.results = [];
            if (clearPlayesAndMode) {
                localStorage.removeItem('gameMode');
                localStorage.removeItem('currentPlayerId');
                localStorage.removeItem('players');
                localStorage.removeItem('results');
                this.gameMode = 0;
                this.currentPlayerId = 0;
                this.players = [];
                return;
            }
            this.currentPlayerId = 0;
            for (let i = 0; i < this.players.length; i++) {
                let player = this.players[i];
                player.scoore = this.gameMode;
            }
            this.saveGame();
            
        },
        finishMove() {
            let totalSum = this.shot1 + this.shot2 + this.shot3;
            let player = this.players.find(a => a.id == this.currentPlayerId);
            if (player.scoore > totalSum) {
                player.scoore -= totalSum;
                this.results.push({
                    time: Date.now(),
                    name: player.name, 
                    shot1: this.shot1, 
                    shot2: this.shot2, 
                    shot3: this.shot3, 
                    total: totalSum
                });
                this.nextPlayer();
                return;
            }
            if (player.scoore < totalSum) {
                this.results.push({
                    time: Date.now(),
                    name: player.name, 
                    shot1: 0, 
                    shot2: 0, 
                    shot3: 0, 
                    total: 0
                });
                this.nextPlayer();
                return;
            }
            this.playerWin(); 
        },
        nextPlayer() {
            this.clearShots();
            let maxPlayerId = this.players.length - 1;
            if (maxPlayerId === this.currentPlayerId) {
                this.currentPlayerId = 0;
                this.saveGame();
                return;
            } 
            this.currentPlayerId++;
            this.saveGame();
        },
        playerWin() {
            let player = this.players.find(a => a.id == this.currentPlayerId);
            this.winner = player.name;
        },
        handleKeyDown(event) {
            if (event.code === 'Space' || event.key === ' ') {
                event.preventDefault();
                this.finishMove();
            }
        }
    },
    mounted: function() {
        window.addEventListener('keydown', this.handleKeyDown);

        // Load saved game
        let historyNamesData = localStorage.getItem('historyNames');
        this.historyNames = historyNamesData ? JSON.parse(historyNamesData) : [];

        let gameModeData = localStorage.getItem('gameMode');
        let currentPlayerIdData = localStorage.getItem('currentPlayerId');
        let playersData = localStorage.getItem('players');
        let resultsData = localStorage.getItem('results');
        
        if (!gameModeData || !currentPlayerIdData || !playersData || !resultsData) {
            this.gameMode = 501;
            this.currentPlayerId = 0;
            this.players = [];
            this.results = [];
            return;
        }
        this.gameMode = Number(gameModeData);
        this.currentPlayerId = Number(currentPlayerIdData);
        this.players = JSON.parse(playersData);
        this.results = JSON.parse(resultsData);
    }
}

</script>

<style scoped>
.tablo {
    float: left;
    width: calc(100% - 1050px);
}
.shot {
    float: left;
    width: 1020px;
}
.currentPlayerScoore {
    text-align: center;
    font-size: 220px;
    font-weight: bold;
}
.currentPlayerScoore span {
    color: #888;
}
</style>
