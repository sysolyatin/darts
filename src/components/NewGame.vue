<template>
    <div class="container-fluid">
        <h1>Новая игра</h1>
        <div class="form-floating mb-3">
            <select class="form-select" @change="changeGameType">
                <option value="101">101</option>
                <option value="201">201</option>
                <option value="301" selected>301</option>
                <option value="501">501</option>
            </select>
            <label>Тип игры</label>
        </div>
        <div class="form-floating mb-3">
            <input type="text" class="form-control" placeholder="Введите имя игрока" v-model="playerName1">
            <label>Имя первого игрока</label>
        </div>
        <div class="form-floating mb-3">
            <input type="text" class="form-control" placeholder="Введите имя игрока" v-model="playerName2">
            <label>Имя второго игрока</label>
        </div>
        <div class="form-floating mb-3">
            <input type="text" class="form-control" placeholder="Введите имя игрока" v-model="playerName3">
            <label>Имя третьего игрока</label>
        </div>
        <div class="form-floating mb-3">
            <input type="text" class="form-control" placeholder="Введите имя игрока" v-model="playerName4">
            <label>Имя четвертого игрока</label>
        </div>
        <button class="btn btn-success" @click="startGame">Начать игру</button>

        <div class="historyNames mt-5">
            <button class="btn btn-sm btn-outline-success me-2" v-for="name in historyNames" @click="setName(name)">{{ name }}</button>
        </div>
    </div>

    

</template>
  
<script>
export default {
    name: 'NewGame',
    props: {
        historyNames: Object
    },
    data() {
        return {
            gameType: 301,
            playerName1: "",
            playerName2: "",
            playerName3: "",
            playerName4: "",
        } 
    },
    methods: {
        startGame() {
            let playersNames = [];
            if (this.playerName1 !== "") playersNames.push(this.playerName1);
            if (this.playerName2 !== "") playersNames.push(this.playerName2);
            if (this.playerName3 !== "") playersNames.push(this.playerName3);
            if (this.playerName4 !== "") playersNames.push(this.playerName4);

            if (playersNames.length === 0) return;

            this.$emit("startGame", this.gameType, playersNames);
        },
        changeGameType(e) {
            this.gameType = Number(e.target.value);
        }, 
        setName(name) {
            if (this.playerName1 === "") {
                this.playerName1 = name;
                return;
            }
            if (this.playerName2 === "") {
                this.playerName2 = name;
                return;
            }
            if (this.playerName3 === "") {
                this.playerName3 = name;
                return;
            }
            this.playerName4 = name;
        }
    }
}
</script>
  
<style scoped>

</style>
  