<template>
  <h1 class="text center">Dice on ice 🎲</h1>
  <main class="wrapper gameTable">
    <section class="buttonContainer">
      <div class="dice" v-show="!gameEnd">6</div>
      <button
        v-if="!gameEnd"
        data-alttext="🎲 Roll"
        @click="rollDice"
        :disabled="rollingDice"
      >
        <span>🎲 Roll dice</span>
      </button>
      <button
        v-if="!gameEnd"
        data-alttext="📥 hold"
        @click="holdScore"
        :disabled="rollingDice"
      >
        <span>📥 hold</span>
      </button>
      <button v-if="gameEnd" data-alttext="🔄 Restart" @click="newGame">
        <span>🔄 New Game</span>
      </button>
    </section>
    <div class="players-container">
      <PlayerVue
        :player="player"
        v-for="player in players"
        :key="player.num"
      ></PlayerVue>
    </div>
  </main>
  <section class="wrapper flex column gap-1">
    <div>
      <button
        class="accordion"
        :class="showRules ? 'active' : ''"
        @click="showRules = !showRules"
      >
        Rules
      </button>
      <div class="panel" v-show="showRules">
        <ul>
          <li>
            Each player is allowed to roll the dice till the dice shows one
          </li>
          <li>
            As soon as dice shows one all the cumulative scores till now becomes
            zero
          </li>
          <li>
            If the player holds the score, then his/her score is added to his
            permanent final score
          </li>
          <li>The first player to score a permanent score of 50 wins!!</li>
        </ul>
      </div>
    </div>
  </section>
</template>

<script>
import PlayerVue from "./components/Player.vue";

export default {
  components: {
    PlayerVue,
  },
  data() {
    return {
      showRules: false,
      gameEnd: false,
      rollingDice: false,
      dice: 6,
      playerRef: 1,
      players: {
        1: {
          name: "",
          num: 1,
          active: true,
          score: 0,
          held: 0,
          wins: [],
        },
        2: {
          name: "",
          num: 2,
          active: false,
          score: 0,
          held: 0,
          wins: [],
        },
        3: {
          name: "",
          num: 3,
          active: false,
          score: 0,
          held: 0,
          wins: [],
        },
        4: {
          name: "",
          num: 4,
          active: false,
          score: 0,
          held: 0,
          wins: [],
        },
      },
    };
  },
  methods: {
    rollDice() {
      // Dice vars
      const sides = 6;
      const roll = Math.floor(Math.random() * sides) + 1;
      const dice = document.querySelector(".dice");

      // Player vars
      const player = this.players[this.playerRef];
      const newScore = Number(player.score) + Number(roll);

      this.rollingDice = true;
      this.animateValue(dice, 8, roll, 150);
      this.dice = roll;

      // Loosing scenario
      if (roll === 1) {
        this.updateScore(player.held);
        dice.classList.add("error-animation");
        setTimeout(() => {
          dice.classList.remove("error-animation");
        }, 1200);
        this.switchPlayer();
        return;
      }

      // // Winning scenario
      if (newScore >= 10) {
        player.wins.push("win");
        this.gameEnd = true;
      }

      setTimeout(() => {
        this.rollingDice = false;
      }, 250);
      this.updateScore(newScore);
    },
    updateScore(newScore) {
      this.players[this.playerRef].score = newScore;
    },
    totalResetScore() {
      const reset = 0;
      const player = this.players[this.playerRef];

      player.held = reset;
      this.updateScore(reset);
    },
    switchPlayer() {
      this.rollingDice = true;

      setTimeout(() => {
        this.rollingDice = false;
      }, 2000);

      const arrPlayers = Object.values(this.players);

      // let arrByActive = arrPlayers.filter((player) => player.active === true);

      // console.log(arrByActive);

      switch (this.playerRef) {
        case 1:
          this.playerRef = 2;
          this.players[1].active = false;
          this.players[2].active = true;
          break;
        case 2:
          if (arrPlayers.length > 2) {
            this.playerRef = 3;
            this.players[2].active = false;
            this.players[3].active = true;
            break;
          }
          this.playerRef = 1;
          this.players[2].active = false;
          this.players[1].active = true;
          break;
        case 3:
          if (arrPlayers.length > 3) {
            this.playerRef = 4;
            this.players[3].active = false;
            this.players[4].active = true;
            break;
          }
          this.playerRef = 1;
          this.players[3].active = false;
          this.players[1].active = true;
          break;
        case 4:
          if (arrPlayers.length > 4) {
            this.playerRef = 5;
            this.players[4].active = false;
            this.players[5].active = true;
            break;
          }
          this.playerRef = 1;
          this.players[4].active = false;
          this.players[1].active = true;
          break;
        case 5:
          this.playerRef = 1;
          this.players[5].active = false;
          this.players[1].active = true;
          break;
      }
    },
    holdScore() {
      const player = this.players[this.playerRef];

      player.held = player.score;
      this.switchPlayer();
    },
    newGame() {
      this.totalResetScore();
      this.switchPlayer();
      this.totalResetScore();
      this.switchPlayer();
      this.gameEnd = false;
    },
    animateValue(obj, start, end, duration) {
      let startTimestamp = null;
      const step = (timestamp) => {
        if (!startTimestamp) startTimestamp = timestamp;
        const progress = Math.min((timestamp - startTimestamp) / duration, 1);
        obj.innerHTML = Math.floor(progress * (end - start) + start);
        if (progress < 1) {
          window.requestAnimationFrame(step);
        }
      };
      window.requestAnimationFrame(step);
    },
  },
};
</script>

<style></style>
