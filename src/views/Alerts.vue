<template>
  <div class="alerts" ref="alerts">
    <div :class="['alert', alert.type]" v-for="alert in alerts" :key="alert.id">
      <span class='alert-timestamp'>{{getTimestampString(alert.timestamp)}}</span>
      <pre>{{alert.message}}</pre>
    </div>
  </div>
</template>

<script>
import io from 'socket.io-client';

export default {
  name: 'Alerts',
  data() {
    return {
      alerts: [],
      socket: null,
    };
  },
  created() {
    this.socket = io(this.$route.query.host);
    this.socket.on('alerts', (alerts) => {
      this.alerts.splice(this.alerts.length, 0, ...alerts);
      this.$nextTick(() => this.scrollToBottomChecked());
    });
  },
  beforeDestroy() {
    this.socket.disconnect();
  },
  methods: {
    scrollToBottom() {
      this.$refs.alerts.scrollTop = this.$refs.alerts.scrollHeight;
    },
    scrollToBottomChecked() {
      if (this.atBottom()) this.scrollToBottom();
    },
    atBottom() {
      const elem = this.$refs.alerts;
      return (elem.scrollTop - elem.scrollHeight - elem.clientHeight < 50);
    },
    getTimestampString(timestamp) {
      const datetime = new Date(timestamp);
      return `[${String(datetime.getHours()).padStart(0, 2)}:${String(datetime.getMinutes()).padStart(0, 2)}:${String(datetime.getSeconds()).padStart(0, 2)}.${String(datetime.getMilliseconds()).padStart(0, 3)}]`;
    },
  },
};
</script>

<style scoped>
  .alerts {
    overflow: scroll;
    display: flex;
    flex-direction: column;
    align-items: stretch;
    justify-content: flex-start;
    text-align: left;
    max-width: 100%;
    max-height: 100%;
  }

  .alert {
    padding: .5rem;
    margin: 0;
    margin-top: -1px;
    display: flex;
    flex-direction: row;
  }

  .alert-timestamp {
    font-family: monospace;
    width: 100px;
    margin-right: 10px;
  }

  .alert pre {
    margin: 0;
    padding: 0;
  }

  .alert.info {
    border: 1px solid #333;
    background: #3333;
  }

  .alert.warn {
    border: 1px solid #806f00;
    background: #806f0033;
    color: #ffdd00;
  }

  .alert.error {
    border: 1px solid #800000;
    background: #80000033;
    color: #dd0000;
  }
</style>
