<template>
  <div class="container-fluid p-0 d-flex flex-column min-vh-100">
    <div class="table-wrapper">
      <table class="table table-bordered table-responsive w-100 flex-grow-1">
        <thead class="thead-dark">
          <tr class="row">
            <th class="col-md-4 col-6">
              <button @click="resetToNicknameAsc" class="sort-button">
                <span class="title-enclosed title-no-border">Reset</span>
              </button>
              <button @click="sortByEndOfContracts" class="sort-button">
                <span class="title-enclosed title-no-border">EndOfContracts ({{ sortOrder === 'asc' ? 'ASC' : 'DESC' }})</span>
              </button>
            </th>
            <th class="col-md-8 col-6 d-md-none border border-3 rounded-2 th-custom" style="border-color: #354751 !important">
              <div class="d-flex justify-content-center align-items-center" style="position: relative;">
                <button @click="changeColumn(-1)" class="btn btn-link p-0 icon-container-left-icon" style="position: absolute;left: -3%; color: #7e9099;top: 10%;">
                  <font-awesome-icon :icon="['fas', 'caret-left']" />
                </button>
                <span class="mx-4 title-general" :class="getColumnClass(showColumn)">
                  {{ getColumnTitle(showColumn) }}
                </span>
                <button @click="changeColumn(1)" class="btn btn-link p-0 icon-container-right-icon" style="position: absolute; right: 10%; left: 100%; top: 10%; color: #7e9099;">
                  <font-awesome-icon :icon="['fas', 'caret-right']"/>
                </button>
              </div>
            </th>
            <th class="col-md-8 d-none d-md-block ">
              <div class="row">
                <div class="col-md-4 d-none d-md-block border p-2 border-3 rounded-2 th-end" style="border-color: #354751 !important">END OF CONTRACTS</div>
                <div class="col-md-4 d-none d-md-block border p-2 border-3 rounded-2 th-option" style="border-color: #354751 !important">OPTION</div>
                <div class="col-md-4 d-none d-md-block border p-2 border-3 rounded-2 th-validity" style="border-color: #354751 !important">OPTION-VALIDITY</div>
              </div>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="player in sortedPlayers" :key="player.id" class="row">
            <td class="col-md-4 col-6">
              <PlayerInfoList :player="player" />
            </td>
            <td class="col-md-8 col-6">
              <div class="row">
                <div :class="['col-md-4', showColumn !== 'contracts' && 'd-none d-md-block']">
                  <span :class="getTextClass('contracts', player)">
                    {{ formatDate(player.contract_end) }}
                  </span>
                </div>
                <div :class="['col-md-4', showColumn !== 'option' && 'd-none d-md-block']">
                  <span :class="getTextClass('option', player)">
                    {{ player.option ? formatDate(player.option) : '--' }}
                  </span>
                </div>
                <div :class="['col-md-4', showColumn !== 'validity' && 'd-none d-md-block']">
                  <span :class="getTextClass('validity', player)">
                    {{ player.option_validity ? formatDate(player.option_validity) : '--' }}
                  </span>
                </div>
              </div>
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
    PlayerInfoList,
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
      sortOrder: 'asc',
      sortBy: 'nickname',
      showColumn: 'contracts', 
      columns: ['contracts', 'option', 'validity'], 
    };
  },
  computed: {
    sortedPlayers() {
      let sortedPlayers = [...this.players];

      if (this.sortBy === 'nickname') {
        const player10 = sortedPlayers.find((player) => player.id === 84);
        const otherPlayers = sortedPlayers.filter((player) => player.id !== 84).sort((a, b) => {
          return a.nickname.localeCompare(b.nickname);
        });
        sortedPlayers = [...otherPlayers, player10];
      } else if (this.sortBy === 'EndOfContracts') {
        sortedPlayers.sort((a, b) => {
          const dateA = new Date(a.contract_end);
          const dateB = new Date(b.contract_end);
          return this.sortOrder === 'asc' ? dateA - dateB : dateB - dateA;
        });
      }
      
      return sortedPlayers;
    },
  },
  methods: {
    resetToNicknameAsc() {
      this.sortBy = 'nickname';
      this.sortOrder = 'asc';
    },
    sortByEndOfContracts() {
      if (this.sortBy !== 'EndOfContracts') {
        this.sortBy = 'EndOfContracts';
        this.sortOrder = 'asc';
      } else {
        this.toggleSortOrder();
      }
    },
    toggleSortOrder() {
      this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc';
    },
    changeColumn(direction) {
      const index = (this.columns.indexOf(this.showColumn) + direction + this.columns.length) % this.columns.length;
      this.showColumn = this.columns[index];
    },
    getColumnTitle(column) {
      const titles = {
        contracts: 'END OF CONTRACTS',
        option: 'OPTION',
        validity: 'OPTION-VALIDITY',
      };
      return titles[column];
    },
    getColumnClass(column) {
      return {
        contracts: 'text-contracts',
        option: 'text-option',
        validity: 'text-validity',
      }[column];
    },
    formatDate(date) {
      return date ? new Date(date).toLocaleDateString('fr-FR').replace(/\//g, '-') : '--';
    },
    getTextClass(column, player) {
      if (column === 'contracts') return this.getStatusClass(player.contract_end_status);
      return 'text-white';
    },
    getStatusClass(status) {
      return {
        green: 'text-success',
        yellow: 'text-warning',
        red: 'text-danger',
      }[status] || 'text-secondary';
    },
  },
};
</script>

<style scoped>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
  background-color: black;
  max-width: 100%;
}

.container-fluid {
    width: 100%;
    margin: 0;
    padding: 0;
}

.table-wrapper {
  overflow-x: auto;
  flex-grow: 1;
  overflow-x: hidden;
  background-color: black;
}

.table-bordered[data-v-ec8f0742] > :not(caption) {
  border: none;
  background-color: black;
}

.table-wrapper[data-v-ec8f0742] {
  overflow-x: auto;
  flex-grow: 1;
  background-color: black;
}

.th-custom {
    border-left-width: 10px !important;
    border-right-width: 10px !important;
    border-bottom: 1px solid blue !important;
    padding: 0 !important; 
    padding-top: 5px !important;
    padding-bottom: 15px !important;
    height: 50%;
    margin-top: 10px;
}

.th-end {
  border-left-width: 15px !important;
  color: #619ec4;
  padding-bottom: 15px !important;
  padding-top: 0 !important;
}

.th-option {
  color: #619ec4;
  padding-bottom: 15px !important;
  padding-top: 0 !important;
}

.th-validity {
  border-right-width: 15px !important;
  color: #619ec4;
  padding-bottom: 15px !important;
  padding-top: 0 !important;
}
.table {
  border-collapse: collapse;
  width: 100%;
  margin-bottom: 0;
}

.table-bordered > :not(caption) > * {
  border-width: var(--bs-border-width) 0;
  margin-right: 10px;
  background-color: black;
}

.table td, .table th {
  background-color: black;
  text-align: center;
  vertical-align: middle;
  padding: 10px 0;
  border-bottom: 2px dashed #333 !important;
  color: white;
  border: none;
}

.table th {
  font-size: 14px;
}

.table-bordered > :not(caption) {
  border: none;
}

.table-bordered > :not(caption) > * {
  border: none !important;
}

th.col-md-4.col-6 .sort-button {
    background-color: black; 
    color: white;
    border: none;
    padding: 10px;
    font-size: 14px;
    cursor: pointer;
}

.table thead th {
  border-bottom: none !important;
}

.table thead .th-custom {
  border-bottom: 3px solid grey !important;
}

.row {
  position: relative;
}

.col-md-4{
  padding-top: 15px;
  margin-right: -5px;
}

.table td {
  padding-top: 20px;
}

.status-text, .option-text, .option-validity-text {
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

.text-white {
  color: white !important;
}

.title-general {
  font-size: 12px;
  color: #619ec4;
}
</style>
