<template>
  <div id="streams-col">
    <div id="streams">
      <stream v-for="stream in streams"
        :data-stream="stream.id"
        :style="getStreamStyle(stream.id, stream.order)"
        :active="stream.active"
        :channelId="stream.id"
        :testMode="testMode"
        :ready="stream.ready"
        :key="stream.id"
        @activate="$emit('activateStream', stream.id)"
        @ready="onStreamReady"
        @close="$emit('closeStream', stream.id)">
      </stream>
    </div>
  </div>
</template>

<script>
  import Stream from './Stream.vue';

  export default {
    components: { Stream },
    props: {
      streams: { default: [] },
      testMode: { default: false },
      activeStreamRatio: { default: 0.7 },
    },
    methods: {
      onStreamReady(streamId, player) {
        this.$emit('streamReady', { streamId, player });
      },
      getStreamStyle(streamId, order) {
        const active = this.activeStream.id === streamId;
        const ratio = active ? this.activeStreamRatio : this.inactiveStreamRatio;
        return {
          order: active ? -1 : order,
          width: active ? '100%' : `${100 / (this.streams.length - 1)}%`,
          height: `${ratio * 100}%`,
        };
      },
    },
    computed: {
      activeStream() {
        return this.streams.find(s => s.active);
      },
      inactiveStreamRatio() {
        return 1 - this.activeStreamRatio;
      },
    },
  };
</script>

<style lang="scss">
  #streams-col {
    vertical-align: middle;
  }

  #streams {
    height: 100vh;
    overflow: hidden;
    display: flex;
    flex-wrap: wrap;
    align-items: stretch;
    justify-content: center;
  }

  .stream {
    &.active {
      background: none;
      width: 100%;
    }
  }
</style>
