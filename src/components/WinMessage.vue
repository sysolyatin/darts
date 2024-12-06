<template>
    <div class="win bg-success text-center p-5">
        <h2>игра завершена, победитель</h2>
        <h1 class="text-warning display-1 mb-5">{{ name }}</h1>
        <button class="btn btn-dark btn-lg me-5" @click="newGame">Новая игра</button>
        <button class="btn btn-dark btn-lg" @click="newGameSavePlayers">Такая же игра</button>
    </div>
</template>
  
<script>
export default {
    name: 'WinMessage',
    props: {
        name: String
    },
    methods: {
        newGame() {
            this.$emit("newGame", true);
        },
        newGameSavePlayers() {
            this.$emit("newGame", false);
        },
    }
}
</script>
  
<style scoped>
.win {
    width: 100%;
    height: 100vh;
}
</style>
  