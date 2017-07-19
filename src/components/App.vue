<template>
  <div id="app">
    <div v-if="streams.length" id="app-container">
      <div id="streams-col">
        <div id="streams">
          <stream v-for="stream in streams"
            :data-stream="stream.id"
            :style="getInlineStreamStyle(stream.id, stream.order)"
            :class="{ active: stream.id === activeStream.id }"
            :ref="getStreamRef(stream.id)"
            :channelId="stream.id"
            :key="stream.id"
            @activate="activateStream(stream.id)"
            @ready="onReady(stream.id)">
          </stream>
        </div>
      </div>
      <div id="chat-col">
        <div id="chats">
          <chat v-for="chat in chats"
            :class="{ active: chat.id === activeChat.id }"
            :channelId="chat.id"
            :initialized="chat.initialized"
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
          <button type="submit">Watch</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
  import Stream from './Stream.vue';
  import Chat from './Chat.vue';
  import queryString from 'query-string';

  export default {
    name: 'app',
    components: { Stream, Chat },
    data () {
      return { streams: [] };
    },
    created() {
      let streams = queryString.parse(window.location.search).streams;
      if (streams) {
        streams = streams.split(' ').filter(s => s);
        this.streams = streams.map((streamId, idx) => ({
          id: streamId.trim(),
          order: idx,
          active: idx === 0,
        }));
        this.activateStream(streams[0]);
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
    },
    methods: {
      activateStream(streamId) {
        const previous = this.activeStream;
        const stream = this.streams.find(s => s.id === streamId);
      
        this.streams.forEach(s => s.active = false);
        stream.order = 0;
        stream.active = true;
        stream.initialized = true;
        previous.order = stream.order;

        this.muteAll();
        this.unmute(streamId)
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
      getInlineStreamStyle(streamId, order) {
        const active = this.activeStream.id === streamId;
        return {
          order: active ? -1 : order,
          width: active ? '100%' : `${100 / (this.streams.length - 1)}%`,
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
    height: 30%;
    width: 25%;
    &.active {
      width: 100%;
      height: 70%;
    }
  }

  .inactive-chat {
    position: relative;
    display: none;
  }
  
</style>
