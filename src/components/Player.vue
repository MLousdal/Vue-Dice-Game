<template>
  <section
    class="playerSection box"
    :class="[{ active: player.active }]"
    :style="`background-color:hsl(${player.color}, 100%, 80%)`"
  >
    <h2 v-if="!isEditing" @click="editName">
      {{ player.name == "Player" ? `Player ${player.num}` : player.name }}
    </h2>
    <input
      v-else
      type="text"
      @blur="isEditing = false"
      @keydown.enter="isEditing = false"
      v-model="player.name"
      ref="input"
    />
    <span id="playerScore" class="score">{{ player.score }}</span>
    <div class="current">
      <p class="held">Held: {{ player.held }}</p>
      <div class="wins">
        <span v-for="win in player.wins" :key="win">🌟</span>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: "Player",
  props: {
    player: {
      required: true,
      type: Object,
      default: {
        name: "Player",
        num: "1",
        active: false,
        score: 0,
        held: 0,
        wins: [],
      },
    },
  },
  data() {
    return { isEditing: false };
  },
  methods: {
    editName() {
      this.isEditing = true;
      this.$nextTick(() => this.$refs.input.focus());
    },
  },
};
</script>

<style></style>
