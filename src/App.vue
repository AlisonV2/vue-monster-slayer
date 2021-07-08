<template>
    <div>
        <Header />
        <Game />
    </div>
</template>

<script>
import Header from './components/Header';
import Game from './components/Game';

export default {
  name: 'App',
  components: {
    Header,
    Game
  }
}
</script>

<style>
* {
  box-sizing: border-box;
}

html {
  font-family: 'Jost', sans-serif;
}

body {
  margin: 0;
}
</style>
