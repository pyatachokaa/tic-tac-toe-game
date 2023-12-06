<template>
  <div id="app">
    <game-board></game-board>
  </div>
</template>

<script>
import GameBoard from './components/GameBoard.vue';

export default {
  components: {
    GameBoard
  }
};
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f0f0f0;
}
</style>