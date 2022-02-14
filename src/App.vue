<template>
  <h1 class="text center">Dice on ice 🎲</h1>
  <main class="wrapper gameTable">
    <aside>
      <div class="buttonContainer">
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
      </div>
      <button
        class="ruleBtn"
        :class="showRules ? 'active' : ''"
        @click="showRules = !showRules"
      >
        Rules
      </button>
    </aside>

    <div class="players-container">
      <PlayerVue
        v-for="player in players"
        :player="player"
        :key="player.num"
      ></PlayerVue>
      <div class="addPlayer box" @click="addPlayer">
        <h2>Add player +</h2>
      </div>
    </div>
  </main>
  <section class="rules">
    <div
      class="background"
      @click="showRules = !showRules"
      v-show="showRules"
    ></div>
    <div class="panel" v-show="showRules">
      <h3>Rules:</h3>
      <hr />
      <ul>
        <li>Each player is allowed to roll the dice till the dice shows one</li>
        <li>
          As soon as the dice shows one all the accumulated score till now
          resets to the last held score
        </li>
        <li>
          If the player holds their score, then their score is added to their
          permanent score
        </li>
        <li>The first player to score 50 points wins!!</li>
      </ul>
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
      playerRef: 0,
      players: [
        {
          name: "",
          num: 1,
          color: 200,
          active: true,
          score: 0,
          held: 0,
          wins: [],
        },
        {
          name: "",
          num: 2,
          color: 0,
          active: false,
          score: 0,
          held: 0,
          wins: [],
        },
      ],
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
      if (newScore >= 50) {
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

      this.players.forEach((player) => {
        player.score = reset;
        player.held = reset;
      });
    },
    switchPlayer() {
      this.rollingDice = true;

      setTimeout(() => {
        this.rollingDice = false;
      }, 2000);

      this.players[this.playerRef].active = false;
      console.log(this.playerRef + 1, this.amountPlayers);

      if (this.playerRef + 1 < this.amountPlayers) {
        this.playerRef = this.playerRef + 1;
      } else {
        console.log("else");
        this.playerRef = 0;
      }

      this.players[this.playerRef].active = true;
    },
    holdScore() {
      const player = this.players[this.playerRef];

      player.held = player.score;
      this.switchPlayer();
    },
    newGame() {
      this.totalResetScore();
      this.gameEnd = false;
    },
    addPlayer() {
      this.players.push({
        name: "",
        num: this.amountPlayers + 1,
        color: this.randomColor(),
        active: false,
        score: 0,
        held: 0,
        wins: [],
      });
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
    randomColor() {
      return Math.floor(Math.random() * 360);
    },
  },
  computed: {
    amountPlayers() {
      return this.players.length;
    },
  },
};
</script>

<style></style>
