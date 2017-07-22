<template>
  <div class="stream" :class="{ active }">
    <div class="stream-wrapper" :id="htmlId"></div>
    <div v-if="! active" class="stream-overlay">
      <div class="channel-badge">
        <span class="channel-id">{{ channelId }}</span><button class="close" @click="$emit('close')">&times;</button>
      </div>
      <button @click="$emit('activate')"></button>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      channelId: {},
      active: {},
      testMode: { default: false },
    },
    data() {
      return { player: null, ready: false };
    },
    computed: {
      htmlId() {
        return `stream-player-${this.channelId}`;
      }
    },
    methods: {
      onReady() {
        if (! this.ready) {
          this.ready = true;
          this.$emit('ready');
        }
      },
    },
    mounted() {
      if (! this.testMode) {
        const options = {
          channel: this.channelId,
          width: '100%',
          height: '100%',
        };
        this.player = new Twitch.Player(this.htmlId, options);
        this.player.addEventListener(Twitch.Player.PLAY, this.onReady);
      }
    },
  };
</script>

<style lang="scss">
  .stream {
    position: relative;
    background: #090909;
  }

  .stream-wrapper {
    width: 100%;
    height: 100%;
    iframe, object, embed {
      width: 100%;
      height: 100%;
    }
  }

  .stream-overlay {
    transition: background-color 0.2s;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    background-color: rgba(0,0,0,0.3);
    width: 100%;
    height: 100%;
    &:hover {
      background-color: rgba(0,0,0,0.0);
    }
    > button {
      display: block;
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      margin: auto;
      width: 100%;
      height: 100%;
      background: none;
      border: none;
      cursor: pointer;
    }
  }

  .channel-badge {
    background: rgba(0,0,0,.5);
    display: block;
    float: left;
    position: absolute;
    right: 0;
    bottom: 0;
    z-index: 1;
    border: 1px solid rgba(40,40,40,.5);
    border-bottom: none;
    border-right: none;
    > .channel-id, > .close {
      line-height: 1;
      font-size: 16px;
      padding: 5px 12px;
      vertical-align: middle;
      color: #fff;
      display: inline-block;
    }
    > .channel-id {
      font-weight: bold;
    }
    > .close {
      transition: background-color 0.2s;
      font-family: 'Arial', sans-serif;
      font-size: 24px;
      &:hover {
        background-color: rgba(255,0,0,.5);
      }
    }
  }
</style>
