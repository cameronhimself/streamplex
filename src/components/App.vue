<template>
  <div id="app">
    <div v-if="streams.length" id="app-container">
      <div id="streams-col">
        <div id="streams">
          <stream v-for="stream in streams"
            :data-stream="stream.id"
            :style="getStreamStyle(stream.id, stream.order)"
            :active="stream.id === activeStream.id"
            :ref="getStreamRef(stream.id)"
            :channelId="stream.id"
            :testMode="testMode"
            :key="stream.id"
            @activate="activateStream(stream.id)"
            @ready="onReady(stream.id)"
            @close="closeStream(stream.id)">
          </stream>
        </div>
      </div>
      <div id="chat-col">
        <div id="chats">
          <chat v-for="chat in chats"
            :class="{ active: chat.id === activeChat.id }"
            :channelId="chat.id"
            :initialized="chat.initialized"
            :testMode="testMode"
            :key="chat.id">
          </chat>
        </div>
      </div>
    </div>
    <div v-else id="app-container">
      <div id="intro">
        <img class="logo" src="../assets/logo.png" /><h1>Streamplex</h1>
        <p>Add your streams below, separated by spaces.</p>
        <form>
          <input name="streams" type="text" placeholder="stream1 stream2 stream3..." />
          <button class="btn" type="submit">Watch</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
  import createHistory from 'history/createBrowserHistory'
  import Stream from './Stream.vue';
  import Chat from './Chat.vue';
  import queryString from 'query-string';

  const history = createHistory();

  export default {
    name: 'app',
    components: { Stream, Chat },
    data () {
      return { streams: [], testMode: false, activeStreamRatio: 0.7 };
    },
    created() {
      const parsed = queryString.parse(window.location.search);
      this.testMode = parsed.testMode != false;
      if (parsed.streams) {
        this.streams = parsed.streams
          .split(' ')
          .filter(s => s) // filter empty strings
          .map((streamId, idx) => ({
            id: streamId.trim(),
            order: idx,
            active: idx === 0,
          }));
      }
    },
    mounted() {
      if (this.streams.length) {
        this.activateStream(this.streams[0].id);
      }
    },
    computed: {
      chats() {
        return this.streams;
      },
      activeStream() {
        return this.streams.find(s => s.active);
      },
      activeChat() {
        return this.streams.find(s => s.active);
      },
      streamsQueryString() {
        return this.streams.slice(0)
          .sort((a, b) => a.order - b.order)
          .map(s => s.id)
          .join(' ');
      },
      inactiveStreamRatio() {
        return 1 - this.activeStreamRatio;
      },
    },
    watch: {
      streams(newStreams) {
        const currentQueryParams = queryString.parse(window.location.search);
        if (this.streamsQueryString !== currentQueryParams.streams) {
          currentQueryParams.streams = this.streamsQueryString;
          let newQueryString = queryString.stringify(currentQueryParams);
          newQueryString = newQueryString.replace(/%20/g, '+');
          history.replace(`?${newQueryString}`);
        }
      },
    },
    methods: {
      closeStream(streamId) {
        const toClose = this.streams.find(s => s.id === streamId);
        if (toClose) {
          this.streams.splice(this.streams.indexOf(toClose), 1);
          if (toClose.active) {
            this.activateStream(this.streams[0]);
          }
        }
      },
      activateStream(streamId) {
        const stream = this.streams.find(s => s.id === streamId);
        const streams = this.streams.slice(0);

        // Swap position of streams
        if (this.activeStream) {
          this.activeStream.order = stream.order;
        }
        stream.order = 0;

        // Swap active state
        streams.forEach(s => s.active = false);
        stream.active = true;

        // Initialize
        stream.initialized = true;

        // Update
        this.streams = streams;

        // Handle audio
        if (! this.testMode) {
          this.muteAll();
          this.unmute(streamId)
        }
      },
      onReady(streamId) {
        this.streams.find(s => s.id === streamId).ready = true;
        if (streamId === this.activeStream.id) {
          this.setVolume(streamId, 1);
          this.unmute(streamId);
        } else {
          this.mute(streamId);
        }
      },
      mute(channelId) {
        this.getStreamPlayer(channelId).setMuted(true);
      },
      unmute(channelId) {
        this.getStreamPlayer(channelId).setMuted(false);
      },
      setVolume(channelId, vol) {
        this.getStreamPlayer(channelId).setVolume(vol);
      },
      muteAll() {
        this.streams.forEach(s => this.mute(s.id));
      },
      getStreamPlayer(streamId) {
        const ref = this.$refs[this.getStreamRef(streamId)][0];
        if (ref) {
          return ref.player;
        }
        return null;
      },
      getStreamRef(channelId) {
        return `stream_${channelId}`;
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
  };
</script>

<style lang="scss">
  @import '../assets/styles/base.scss';

  h1 {
    display: inline-block;
    padding-bottom: 10px;
    border-bottom: 1px solid $primary-color;
    color: #fff;
    font-size: 30px;
    text-transform: uppercase;
  }

  #intro {
    padding-top: 80px;
    text-align: center;
    h1 {
      margin-bottom: 40px;
    }
    .logo {
      margin-right: 20px;
      display: inline-block;
      height: 40px;
      padding-bottom: 4px;
      vertical-align: middle;
    }
    p {
      margin-bottom: 30px;
    }
    input {
      width: 250px;
      margin-right: 10px;
    }
  }

  #app-container {
    display: table;
    height: 100vh;
    width: 100%;
  }

  #streams-col, #chat-col {
    display: table-cell;
    height: 100vh;
  }

  #chat-col {
    vertical-align: top;
    text-align: right;
    width: 1px; 
  }

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

  .inactive-chat {
    position: relative;
    display: none;
  }
  
</style>
