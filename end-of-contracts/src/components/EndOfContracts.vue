<template>
  <div class="container-fluid p-0 d-flex flex-column min-vh-100">
    <div class="table-wrapper">
      <table class="table table-responsive w-100 flex-grow-1">
        <thead>
          <tr>
            <th class="header-title player-info-column player-info-header">
              <button @click="toggleSortOrder" class="sort-button">
                <span class="title-enclosed title-no-border">
                  Nickname ({{ sortOrder === 'asc' ? 'ASC' : 'DESC' }})
                </span>
              </button>
            </th>
            <th v-if="windowWidth <= 768" class="header-title dynamic-column dynamic-header">
              <div class="title-wrapper">
                <button @click="showPreviousColumn" class="icon-container-left-icon">
                  <font-awesome-icon :icon="['fas', 'caret-left']" />
                </button>
                <span class="title-enclosed">{{ getColumnTitle(showColumn) }}</span>
                <button @click="showNextColumn" class="icon-container-right-icon">
                  <font-awesome-icon :icon="['fas', 'caret-right']" />
                </button>
              </div>
            </th>
            <th v-if="windowWidth > 768" class="header-title end-contracts-header">
              <font-awesome-icon :icon="['fas', 'caret-left']" />
              <span class="title-enclosed">END OF CONTRACTS</span>
            </th>
            <th v-if="windowWidth > 768" class="header-title option-header">
              <span class="title-enclosed">OPTION</span>
            </th>
            <th v-if="windowWidth > 768" class="header-title option-validity-header">
              <span class="title-enclosed">OPTION-VALIDITY</span>
              <font-awesome-icon :icon="['fas', 'caret-right']" />
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="player in sortedPlayers" :key="player.id">
            <td class="d-md-table-cell player-info-column">
              <PlayerInfoList :players="[player]" />
            </td>
            <td v-if="showColumn === 'contracts' || windowWidth > 768" class="d-md-table-cell">
              <span :class="['status-text', getStatusClass(player.contract_end_status)]">
                {{ formatDate(player.contract_end) }}
              </span>
            </td>
            <td v-if="showColumn === 'option' || windowWidth > 768" class="d-md-table-cell">
              <span :class="['option-text', player.option ? 'text-info' : 'text-secondary']">
                {{ player.option ? formatDate(player.option) : '--' }}
              </span>
            </td>
            <td v-if="showColumn === 'validity' || windowWidth > 768" class="d-md-table-cell">
              <span :class="['option-validity-text', player.option_validity ? 'text-info' : 'text-secondary']">
                {{ player.option_validity ? formatDate(player.option_validity) : '--' }}
              </span>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import PlayerInfoList from './PlayerInfo.vue';
export default {
  name: 'EndOfContracts',
  components: {
    PlayerInfoList
  },
  data() {
  return {
    players: [
      { id: 71, nickname: 'Player 1', position: 'Midfielder', contract_end: '2026-06-30', option: null, option_validity: null, contract_end_status: 'yellow' },
      { id: 25, nickname: 'Player 2', position: 'Midfielder', contract_end: '2026-06-30', option: '2027-06-30', option_validity: '2026-05-16', contract_end_status: 'yellow' },
      { id: 44, nickname: 'Player 3', position: 'Defender', contract_end: '2026-06-30', option: null, option_validity: null, contract_end_status: 'yellow' },
      { id: 75, nickname: 'Player 4', position: 'Forward', contract_end: '2027-06-30', option: '2028-06-30', option_validity: '2027-05-15', contract_end_status: 'green' },
      { id: 22, nickname: 'Player 5', position: 'Goalkeeper', contract_end: '2025-06-30', option: null, option_validity: null, contract_end_status: 'red' },
      { id: 72, nickname: 'Player 6', position: 'Forward', contract_end: '2026-06-30', option: null, option_validity: null, contract_end_status: 'yellow' },
      { id: 79, nickname: 'Player 7', position: 'Forward', contract_end: '2028-06-30', option: '2029-06-30', option_validity: '2028-05-20', contract_end_status: 'green' },
      { id: 70, nickname: 'Player 8', position: 'Midfielder', contract_end: '2027-06-30', option: null, option_validity: null, contract_end_status: 'green' },
      { id: 63, nickname: 'Player 9', position: 'Midfielder', contract_end: '2025-06-30', option: null, option_validity: null, contract_end_status: 'red' },
      { id: 84, nickname: 'Player 10', position: 'Goalkeeper', contract_end: '2027-06-30', option: '2028-06-30', option_validity: '2027-05-15', contract_end_status: 'green' }
    ],
    showColumn: 'contracts',
    windowWidth: window.innerWidth,
    sortOrder: 'asc'
  };
},

  computed: {
    sortedPlayers() {
      const playersCopy = [...this.players];
      const player10Index = playersCopy.findIndex(player => player.nickname === 'Player 10');
      let player10 = null;

      if (player10Index !== -1) {
        [player10] = playersCopy.splice(player10Index, 1);
      }

      playersCopy.sort((a, b) => {
        if (this.sortOrder === 'asc') {
          return a.nickname.localeCompare(b.nickname);
        } else {
          return b.nickname.localeCompare(a.nickname);
        }
      });

      if (player10) {
        if (this.sortOrder === 'asc') {
          playersCopy.push(player10);
        } else {
          playersCopy.unshift(player10);
        }
      }

      return playersCopy;
    }
  },
  methods: {
    toggleSortOrder() {
      this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc';
    },
    formatDate(date) {
      if (!date) return '--';
      return new Date(date).toLocaleDateString('fr-FR', { day: '2-digit', month: '2-digit', year: 'numeric' }).replace(/\//g, '-');
    },
    getStatusClass(status) {
      const statusClasses = {
        green: 'text-success',
        yellow: 'text-warning',
        red: 'text-danger',
        default: 'text-secondary'
      };
      return statusClasses[status] || statusClasses.default;
    },
    showPreviousColumn() {
      const columns = ['contracts', 'option', 'validity'];
      this.showColumn = columns[(columns.indexOf(this.showColumn) + 2) % 3];
    },
    showNextColumn() {
      const columns = ['contracts', 'option', 'validity'];
      this.showColumn = columns[(columns.indexOf(this.showColumn) + 1) % 3];
    },
    getColumnTitle(column) {
      const titles = {
        contracts: 'END OF CONTRACTS',
        option: 'OPTION',
        validity: 'OPTION-VALIDITY'
      };
      return titles[column] || '';
    },
    updateWindowWidth() {
      this.windowWidth = window.innerWidth;
    }
  },
  mounted() {
    window.addEventListener('resize', this.updateWindowWidth);
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.updateWindowWidth);
  }
};
</script>

<style scoped>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
  background-color: #000;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container-fluid {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background-color: #000;
}

.table-wrapper {
  overflow-x: auto;
  white-space: nowrap;
  flex-grow: 1;
  padding: 0 10px;
}

.table {
  background-color: #000;
  border-collapse: collapse;
  width: 100%;
  padding: 10px;
  margin-bottom: 0;
}

.table td,
.table th {
  text-align: center;
  vertical-align: middle;
  padding: 0;
  padding-top: 20px;
  width: 50%;
  border-bottom: 2px dashed #333;
}

.table td {
  padding-bottom: 10px;
  background-color: #000;
}

.table td.d-md-table-cell.player-info-column {
  border-bottom: 2px dashed #333;
}

.table thead th {
  border: none;
  background-color: #000;
  color: #215480;
  font-size: 0.8rem;
  padding-bottom: 0;
}

.table td.player-info-column {
  border-bottom: 2px dashed #333;
}

.player-info-column {
  margin-left: 10px;
}

.sort-button {
  background: none;
  border: none;
  color: #4d95bc;
  font-weight: bold;
  display: inline-flex;
  align-items: center;
  cursor: pointer;
}

.sort-button .fa-arrow-up,
.sort-button .fa-arrow-down {
  margin-left: 5px;
}

.title-no-border {
  border: none;
}

.title-enclosed {
  padding: 0px;
  padding-bottom: 12px;
  border-top: 5px solid #354751;
  border-bottom: 5px solid #354751;
  border-right: 20px solid #354751;
  border-left: 20px solid #354751;
  background-color: #000;
  border-radius: 5px;
  color: #4d95bc;
  font-weight: bold;
  display: inline-block;
  width: 100%;
}

.title-wrapper {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  margin-right: 10px;
}

.header-title.player-info-header .sort-button .title-enclosed.title-no-border {
  border: none;
}

.icon-container-left-icon,
.icon-container-right-icon {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: transparent;
  border: none;
  color: #8697a0;
}

.icon-container-left-icon {
  left: 5px;
}

.icon-container-right-icon {
  right: 5px;
}

.header-title {
  margin-right: 10px;
  position: relative;
  padding-top: 20px;
  padding-bottom: 20px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.end-contracts-header .fa-caret-left,
.option-validity-header .fa-caret-right {
  color: #8697a0;
  font-size: 1rem;
  position: absolute;
  top: 65%;
  transform: translateY(-50%);
  margin: 0;
  left: 0;
  right: 0;
}

.end-contracts-header .fa-caret-left {
  left: 5%;
}

.option-validity-header .fa-caret-right {
  left: 92%; 
}

.status-text,
.option-text,
.option-validity-text {
  color: #fff;
}

.text-success {
  color: #28a745;
}

.text-warning {
  color: #ffc107;
}

.text-danger {
  color: #dc3545;
}

.text-info {
  color: #fff !important;
}

@media (min-width: 768px) {
  .table thead tr {
    display: table-row;
  }

  .table thead th {
    width: 1%;
  }

  .header-title.player-info-header .title-enclosed {
    border: none;
  }

  .option-validity-header .title-enclosed {
    padding-left: 10px;
    margin-left: -20px;
  }

  .option-header .title-enclosed {
    margin-left: -2px;
  }

  .end-contracts-header .title-enclosed {
    margin-left: 15px;
  }

  .table td.player-info-column {
    width: 31%;
  }

  .table td:not(.player-info-column) {
    width: 24%;
  }

  .end-contracts-header .title-enclosed,
  .option-header .title-enclosed,
  .option-validity-header .title-enclosed {
    border: 4px solid #354751;
    border-radius: 0.375rem;
    padding-bottom: 15px;
    padding-top: 0px;
  }

  .end-contracts-header .title-enclosed {
    border-left-width: 15px;
    border-top-right-radius: 0.375rem;
    border-bottom-right-radius: 0.375rem;
  }

  .option-header .title-enclosed {
    margin-right: 20px;
  }

  .option-validity-header .title-enclosed {
    margin-right: 10px;
    border-right-width: 15px;
  }
}
</style>
