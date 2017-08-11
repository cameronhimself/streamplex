<template>
  <div class="stream" :class="{ active }">
    <div class="stream-wrapper" :id="htmlId"></div>
    <div v-if="! active" class="stream-overlay">
      <div class="channel-badge">
        <button class="channel-id" @click="activate">{{ channelId }}</button><button class="close" @click="$emit('close')">&times;</button>
      </div>
      <button @click="activate"></button>
    </div>
  </div>
</template>

<script>
  const dummyPlayer = {
    setMuted() {},
    setVolume() {},
    addEventListener() {},
  };

  export default {
    props: {
      channelId: {},
      active: {},
      ready: { default: false },
      testMode: { default: false },
    },
    computed: {
      htmlId() {
        return `stream-player-${this.channelId}`;
      }
    },
    methods: {
      activate() {
        if (this.ready) {
          this.$emit('activate');
        }
      },
      onReady(player) {
        if (! this.ready) {
          this.$emit('ready', this.channelId, player);
        }
      },
    },
    mounted() {
      if (this.testMode) {
        this.onReady(dummyPlayer);
      } else {
        const player = new Twitch.Player(this.htmlId, {
          channel: this.channelId,
          width: '100%',
          height: '100%',
        });
        player.addEventListener(Twitch.Player.PLAY, this.onReady.bind(this, player));
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
    border-radius: 3px 0 0 0;
    border-bottom: none;
    border-right: none;
    > .channel-id , > .close {
      transition: background-color 0.2s, color 0.2s, padding 0.2s;
      line-height: 24px;
      font-size: 16px;
      padding: 5px 12px;
      vertical-align: middle;
      color: #ccc;
      display: inline-block;
      &:hover {
        color: #fff;
      }
    }
    > .channel-id {
      font-weight: bold;
      &:hover {
        padding-top: 3px;
        padding-bottom: 6px;
      }
    }
    > .close {
      font-family: 'Arial', sans-serif;
      font-size: 24px;
      &:hover {
        background-color: rgba(255,0,0,.5);
      }
    }
  }
</style>
