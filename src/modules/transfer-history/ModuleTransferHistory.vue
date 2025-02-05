<template>
  <mew6-white-sheet class="px-5 px-lg-7 py-5 justify-space-between">
    <v-row dense>
      <v-col cols="12">
        <div class="mew-heading-2 mb-3">{{ actualTitle }}</div>
      </v-col>
      <v-col cols="12" class="px-0">
        <div v-for="(data, key) in actualNotifications" :key="key" class="mb-2">
          <mew-notification
            :show-indicator="false"
            :notification="data.notification"
            class="px-0"
          />
        </div>
      </v-col>
    </v-row>
  </mew6-white-sheet>
</template>

<script>
import { mapState, mapGetters } from 'vuex';
import formatNotification from '@/modules/notifications/helpers/formatNotification';
import formatNonChainNotification from '@/modules/notifications/helpers/formatNonChainNotification';
import MewNotification from '@/components/MewNotification/MewNotification';

export default {
  name: 'ModuleTransferHistory',
  components: { MewNotification },
  props: {
    isSwap: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {};
  },
  computed: {
    ...mapState('wallet', ['web3', 'address']),
    ...mapGetters('global', ['network']),
    ...mapGetters('notifications', ['txNotifications', 'swapNotifications']),
    parsedTxNotifications() {
      return this.txNotifications
        .map(item => {
          if (item.hasOwnProperty('hash')) {
            return formatNotification(item, this.network);
          }
          return formatNonChainNotification(item);
        })
        .sort(this.sortByDate);
    },
    parsedSwapNotifications() {
      return this.swapNotifications
        .map(item => {
          if (item.hasOwnProperty('hash')) {
            return formatNotification(item, this.network);
          }
          return formatNonChainNotification(item);
        })
        .sort(this.sortByDate);
    },
    actualNotifications() {
      return !this.isSwap
        ? this.parsedTxNotifications
        : this.parsedSwapNotifications;
    },
    actualTitle() {
      return this.isSwap ? `Swap History` : `Tx History`;
    }
  },
  mounted() {},
  methods: {
    sortByDate(a, b) {
      return new Date(b.date) - new Date(a.date);
    }
  }
};
</script>

<style lang="scss" scoped>
.cursor {
  cursor: pointer;
}

.history-container {
  max-height: 300px;
  overflow-y: scroll;
  /**
  * adding this style here until mew-notification is updated with a new prop
  */
  .notification-container {
    background-color: var(--v-white-base) !important;
    border: none !important;
  }
}
</style>
