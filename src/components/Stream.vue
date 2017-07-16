<template>
  <div class="stream">
    <div class="stream-wrapper" :id="htmlId"></div>
    <div class="stream-overlay" :class="{ active: isActive }">
      <span class="channel-id">{{ channelId }}</span>
      <button @click="activate"></button>
    </div>
  </div>
</template>

<script>
  export default {
    props: [
      'channelId',
      'isActive',
    ],
    data() {
      return {
        player: null,
        isReady: false,
      };
    },
    computed: {
      htmlId() {
        return `stream-player-${this.channelId}`;
      }
    },
    methods: {
      activate() {
        this.$emit('activate', this.channelId);
      },
      ready() {
        this.$emit('ready', this.channelId);
      },
    },
    mounted() {
      const options = {
        channel: this.channelId,
        width: '100%',
        height: '100%',
      };
      const self = this;
      const player = new Twitch.Player(this.htmlId, options);
      this.player = player;
      player.addEventListener(Twitch.Player.PLAY, function() {
        if (! this.isReady) {
          this.isReady = true;
          self.ready();
        }
      });
    },
  };
</script>

<style lang="scss">
  .stream {
    position: relative;
  }
  .stream-wrapper {
    width: 100%;
    height: 100%;
  }
  .stream-wrapper iframe, object, embed {
    width: 100%;
    height: 100%;
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
  }

  .stream-overlay:hover {
    background-color: rgba(0,0,0,0.0);
  }

  .stream-overlay .channel-id {
    color: #fff;
    padding: 10px 20px;
    background: rgba(0,0,0,.5);
    font-weight: bold;
    display: block;
    float: left;
    position: absolute;
    right: 0;
    bottom: 0;
  }

  .stream-overlay button {
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

  .stream.active .stream-overlay {
    display: none;
  }
</style>
