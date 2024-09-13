<template>
  <div class="player-info-container">
    <div class="player-info">
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
    player: {
      type: Object,
      required: true
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
