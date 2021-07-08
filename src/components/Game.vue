<template>
  <div id="game">
    <section id="monster" class="container">
      <h2>Monster Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="monsterBarStyles"></div>
      </div>
    </section>
    <section id="player" class="container">
      <h2>Your Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="playerBarStyles"></div>
      </div>
    </section>
    <!-- 
        null = false in JS, so v-if ="winner" means that the section won't show if the winner's value is null.
        if it's a draw, or the winner is player or monster, then the winner is truthy and the section will show. 
      -->
    <section class="container" v-if="winner">
      <h2>Game Over!</h2>
      <h3 v-if="winner === 'monster'">You lost!</h3>
      <h3 v-else-if="winner === 'player'">You won!</h3>
      <h3 v-else>You won!</h3>
      <button @click="restartGame">NEW GAME</button>
    </section>
    <section id="controls" v-else>
      <!-- <section id="controls" v-if='!winner'> -->
      <button @click="attackMonster">ATTACK</button>
      <button :disabled="mayUseSpecialAttack" @click="specialAttackMonster">
        SPECIAL ATTACK
      </button>
      <button @click="healPlayer">HEAL</button>
      <button @click="surrender">SURRENDER</button>
    </section>
    <section id="log" class="container">
      <h2>Battle Log</h2>
      <!--<h3 v-else-if="winner === 'draw'">It's a draw!</h3>-->
      <ul>
        <li v-for="logMessage in logMessages" :key="logMessage">
          <!-- {{ logMessage.actionBy }} - {{logMessage.actionType }} - {{ logMessage.actionValue }} -->
          <span
            :class="{
              'log--player': logMessage.actionBy === 'player',
              'log--monster': logMessage.actionBy === 'monster',
            }"
            >{{ logMessage.actionBy === "player" ? "Player " : "Monster " }}
          </span>
          <span v-if="logMessage.actionType === 'heal'"
            >heals himself for
            <span class="log--heal">{{ logMessage.actionValue }}</span>
          </span>
          <span v-else
            >attacks and deals
            <span class="log--damage">{{ logMessage.actionValue }}</span>
          </span>
        </li>
      </ul>
    </section>
  </div>
</template>

<script>

function getRandomValue(min, max) {
    return Math.floor(Math.random() * (max - min)) + min;
}

export default {
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      currentRound: 0,
      winner: null,
      logMessages: [],
    };
  },
  computed: {
    monsterBarStyles() {
      if (this.monsterHealth < 0) {
        return {
          width: "0%",
        };
      }
      return {
        width: this.monsterHealth + "%",
      };
    },
    playerBarStyles() {
      if (this.playerHealth < 0) {
        return {
          width: "0%",
        };
      }
      return {
        width: this.playerHealth + "%",
      };
    },
    mayUseSpecialAttack() {
      return this.currentRound % 3 !== 0;
    },
  },
  watch: {
    // Reminder : for watchers, the name has to be the name of the property we want to watch
    playerHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.winner = "draw";
      } else if (value <= 0) {
        this.winner = "monster";
      }
    },
    monsterHealth(value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.winner = "draw";
      } else if (value <= 0) {
        this.winner = "player";
      }
    },
  },
  methods: {
    attackMonster() {
      this.currentRound++;
      // const attackValue = Math.floor(Math.random() * (12 - 5)) + 5;
      const attackValue = getRandomValue(5, 12);
      this.monsterHealth -= attackValue;
      // this.monsterHealth = this.monsterHealth - attackValue;
      this.addLogMessage("player", "attack", attackValue);
      this.attackPlayer();
    },
    attackPlayer() {
      const attackValue = getRandomValue(8, 15);
      this.addLogMessage("monster", "attack", attackValue);
      this.playerHealth -= attackValue;
    },
    specialAttackMonster() {
      this.currentRound++;
      const attackValue = getRandomValue(10, 25);
      this.monsterHealth -= attackValue;
      this.addLogMessage("player", " attack", attackValue);
      this.attackPlayer();
    },
    healPlayer() {
      this.currentRound++;
      const healValue = getRandomValue(8, 20);
      if (this.playerHealth + healValue > 100) {
        this.playerHealth = 100;
      } else {
        this.playerHealth += healValue;
      }
      this.addLogMessage("player", "heal", healValue);
      this.attackPlayer();
    },
    restartGame() {
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.currentRound = 0;
      this.winner = null;
      this.logMessages = [];
    },
    surrender() {
      this.winner = "monster";
    },
    addLogMessage(who, what, value) {
      //push adds at the end of the array, unshift at the beginning
      this.logMessages.unshift({
        actionBy: who,
        actionType: what,
        actionValue: value,
      });
    },
  },
};
</script>

<style>
section {
  width: 90%;
  max-width: 40rem;
  margin: auto;
}

.healthbar {
  width: 100%;
  height: 40px;
  border: 1px solid #575757;
  margin: 1rem 0;
  background: rgba(175, 10, 120, 0.3);
}

.healthbar__value {
  background-color: #af0a78;
  width: 100%;
  height: 100%;
}

.container {
  text-align: center;
  padding: 0.5rem;
  margin: 1rem auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
}

#monster h2,
#player h2 {
  margin: 0.25rem;
}

#controls {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

button {
  font: inherit;
  border: 1px solid #00a876;
  background-color: #00a876;
  color: white;
  padding: 1rem 2rem;
  border-radius: 12px;
  margin: 1rem;
  width: 12rem;
  cursor: pointer;
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
}

button:focus {
  outline: none;
}

button:hover,
button:active {
  background-color: #af0a78;
  border-color: #af0a78;
  box-shadow: 1px 1px 8px rgba(0, 0, 0, 0.26);
}

button:disabled {
  background-color: #ccc;
  border-color: #ccc;
  box-shadow: none;
  color: #3f3f3f;
  cursor: not-allowed;
}

#log ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

#log li {
  margin: 0.5rem 0;
}

.log--player {
  color: #7700ff;
}

.log--monster {
  color: #da8d00;
}

.log--damage {
  color: red;
}

.log--heal {
  color: green;
}
</style>
