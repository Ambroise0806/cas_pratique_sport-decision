<template>
  <div class="player-info-container">
    <div v-for="player in sortedPlayers" :key="player.id" class="player-info">
      <img src="@/assets/playerIcon.svg" alt="Player Icon" class="player-icon" />
      <div class="nickname-position-and-ellipsis">
        <div class="nickname-and-position">
          <div class="nickname">{{ player.nickname }}</div>
          <div class="position">{{ player.position }}</div>
        </div>
        <div class="ellipsis">...</div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    players: {
      type: Array,
      required: true
    }
  },
  computed: {
    sortedPlayers() {
      return [...this.players].sort((a, b) => {
        const numberA = parseInt(a.nickname.match(/\d+/)?.[0]);
        const numberB = parseInt(b.nickname.match(/\d+/)?.[0]);

        if (numberA === 10) return 1;
        if (numberB === 10) return -1;

        if (!isNaN(numberA) && !isNaN(numberB)) {
          return numberA - numberB;
        }
        
        return a.nickname.localeCompare(b.nickname);
      });
    }
  }
};
</script>

<style scoped>
.player-info-container {
  flex-shrink: 0;
  margin-right: 20px;
  border-right: 1px solid #333;
  background-color: #003957;
  border-radius: 0.375rem;
}

.player-info {
  display: flex;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #333;
  height: 3.5rem;
}

.player-icon {
  width: 3rem;
  margin-right: 10px;
  flex-shrink: 0;
}

.nickname-position-and-ellipsis {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.nickname-and-position {
  display: flex;
  flex-direction: column;
  margin: 0 2px;
  justify-content: center;
}

.nickname {
  font-weight: bold;
  padding-top: 5px;
  color: #fff;
}

.position {
  color: #ccc;
  font-size: 0.9em;
  padding-bottom: 5px;
}

.ellipsis {
  color: #ccc;
  font-size: 1.2rem;
  padding-top: 10px;
}
</style>
