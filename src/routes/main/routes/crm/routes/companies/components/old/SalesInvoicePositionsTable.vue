<template>
  <v-card>
    <v-card-title>

      <!-- Dialog for creating item -->
      <v-tooltip top>
        <v-btn fab small dark color="green" @click="create()" slot="activator">
          <v-icon>add</v-icon>
        </v-btn>
        <span>{{ $t('add') }}</span>
      </v-tooltip>

      <!-- Select statuses (active/inactive) -->
      <template>
        <v-spacer></v-spacer>
        <v-select :label="$t('status.title')" v-bind:items="statuses" v-model="selectedStatuses" single-line item-text="text" item-value="value"
          multiple chips></v-select>
      </template>

      <!-- Search in table -->
      <v-spacer></v-spacer>
      <v-text-field append-icon="search" :label="$t('search')" single-line hide-details v-model="search"></v-text-field>
    </v-card-title>

    <!-- Table -->
    <v-data-table :rows-per-page-items="[10, 25, { text: $t('all'), value: -1 }]" light :headers="finalHeaders" :items="filteredItems"
      :search="search" :no-results-text="$t('noMatchingResults')" :no-data-text="$t('noDataAvailable')" :rows-per-page-text="$t('rowsPerPageText')">
      <template slot="items" slot-scope="props">
        <!-- action buttons -->
        <td class="text-xs-center cell-nowrap">
          <!-- edit record -->
          <v-tooltip top>
            <v-btn fab small class="xs white--text" color="orange" @click="edit(props.item.id)" slot="activator">
              <v-icon>edit</v-icon>
            </v-btn>
            <span>{{ $t('buttons.edit') }}</span>
          </v-tooltip>
          <!-- suspend/restore record (if soft deletes are enabled) -->
          <template>
            <v-tooltip top v-if="props.item.active == '1'">
              <v-btn fab small class="xs white--text" color="red" @click="suspend(props.item.id)" slot="activator">
                <v-icon>undo</v-icon>
              </v-btn>
              <span>{{ $t('buttons.suspend') }}</span>
            </v-tooltip>
            <v-tooltip top v-else>
              <v-btn fab small class="xs white--text" color="green" @click="restore(props.item.id)" slot="activator">
                <v-icon>redo</v-icon>
              </v-btn>
              <span>{{ $t('buttons.restore') }}</span>
            </v-tooltip>
          </template>
        </td>
        <!-- table fields -->
        <td v-if="key != 'active'" v-for="(field, key) in props.item" :key="key" class="text-xs-center">
          {{ field }}
        </td>
      </template>
      <template slot="pageText" slot-scope="{ pageStart, pageStop }">
        {{ $t('from') }} {{ pageStart }} {{ $t('to') }} {{ pageStop }}
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
  import {
    mapState,
    mapGetters,
    mapMutations,
    mapActions
  } from 'vuex'

  export default {
    props: [
      'headers',
      'itemElements',
      'editButton',
      'tableData',
    ],
    data() {
      return {
        tmp: '',
        search: '',
        pagination: {},
        selectedStatuses: [1],
      }
    },
    computed: {
      finalHeaders() {
        let headers = this.headers.map(header => {
          header.align = 'center'
          return header
        })
        let actionHeader = [{
          text: this.$t('fields.action'),
          align: 'center',
          sortable: false,
        }]
        return [...actionHeader,...headers]
      },
      items() {
        return this.tableData
      },
      statuses() {
        return [{
            text: this.$t('status.active'),
            value: 1
          },
          {
            text: this.$t('status.inactive'),
            value: 0
          },
        ]
      },
      filteredItems() {
        return this.items.filter(item => this.selectedStatuses.includes(parseInt(item.active)))
      },
    },
    methods: {
      edit(id) {
        this.$parent.edit(id)
      },
      create() {
        this.$parent.create()
      },
      suspend(id) {
        this.$parent.suspend(id)
      },
      restore(id) {
        this.$parent.restore(id)
      },
    },
    i18n: {
      messages: {
        pl: {
          fields: {
            action: 'Akcja',
          },
          status: {
            title: "Statusy",
            active: "Aktywne",
            inactive: "Nieaktywne",
          },
          search: "Szukaj",
          noMatchingResults: "Nie znaleziono pasujących rekordów",
          noDataAvailable: "Brak danych",
          rowsPerPageText: "Rekordów na stronę:",
          from: "od",
          to: "do",
          add: "Dodaj",
          all: "Wszystko",
          buttons: {
            edit: 'Edytuj',
            suspend: 'Zawieś',
            restore: 'Przywróć',
            tasks: 'Zadania'
          },
          alerts: {
            suspended: 'Zawieszono',
            suspendError: 'Błąd! nie udało się zawiesić rekordu',
            restored: 'Przywrócono',
            restoreError: 'Błąd! Nie udało się przywrócić rekordu'
          }
        },
        en: {
          fields: {
            action: 'Action',
          },
          status: {
            title: "Statuses",
            active: "Active",
            inactive: "Inactive",
          },
          search: "Search",
          noMatchingResults: "No matching records found",
          noDataAvailable: "No data available",
          rowsPerPageText: "Rows per page:",
          from: "from",
          to: "to",
          add: "Add",
          all: "All",
          buttons: {
            edit: 'Edit',
            suspend: 'Suspend',
            restore: 'Restore',
            tasks: 'Tasks'
          },
          alerts: {
            suspended: 'Suspended',
            suspendError: 'Error! Suspend unsuccessful',
            restored: 'Restored',
            restoreError: 'Error! Restore unsuccessful'
          }
        }
      }
    },
  }

</script>
<style scoped>
  .xs {
    width: 25px;
    height: 25px;
    margin: 3 !important;
  }

  .xs>div {
    /* height: 15px !important;
    line-height: 12px !important; */
    padding: 3px !important;
  }

  .xs>div>i {
    height: 12px !important;
    width: 12px !important;
    line-height: 12px;
  }

</style>
