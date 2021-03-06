<template>
  <div id="app">
    <div v-if="streams.length" id="app-container">
      <action-bar
        :isOpen="actionBar.isOpen"
        :width="actionBar.width"
        :bottomHeight="`${inactiveStreamRatio * 100}%`"
        :isChatOpen="isChatOpen"
        @toggle="actionBar.isOpen = ! actionBar.isOpen"
        @goHome="goHome"
        @addStreams="addingStreams = true"
        @toggleChat="toggleChat">
      ></action-bar>
      <stream-column
        :streams="streams"
        :testMode="testMode"
        :activeStream="activeStreamRatio"
        @streamReady="streamReady"
        @activateStream="activateStream"
        @closeStream="closeStream">
      </stream-column>
      <chat-column
        :chats="chats"
        :isOpen="isChatOpen"
        :testMode="testMode"
        @toggleChat="toggleChat">
      </chat-column>
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
    <modal v-show="addingStreams" @close="addingStreams = false">
      <form @submit.prevent="addStreams">
        <h3>Add Streams</h3>
        <input type="text" v-model="addStreamsString" />
        <button class="btn" type="submit">Add</button>
      </form>
    </modal>
  </div>
</template>

<script>
  import createHistory from 'history/createBrowserHistory'
  import StreamColumn from './StreamColumn.vue';
  import Modal from './Modal.vue';
  import ChatColumn from './ChatColumn.vue';
  import ActionBar from './ActionBar.vue';
  import queryString from 'query-string';

  const history = createHistory();

  export default {
    name: 'app',
    components: { StreamColumn, ChatColumn, Modal, ActionBar },
    data () {
      return {
        streams: [],
        activeStreamRatio: 0.7,
        isChatOpen: true,
        testMode: false,
        addingStreams: false,
        addStreamsString: '',
        actionBar: {
          width: 40,
          isOpen: true,
        },
      };
    },
    created() {
      const parsed = queryString.parse(window.location.search);
      if (parsed.testMode !== undefined) {
        this.testMode = parsed.testMode != false;
      }
      if (parsed.streams) {
        this.streams = this.streamsFromString(parsed.streams);
      }

      // Update streams if query param changes.
      history.listen((location, action) => {
        const currentQueryParams = queryString.parse(location.search);
        const streamsParam = currentQueryParams.streams || '';
        if (this.streamsQueryString !== streamsParam) {
          this.streams = this.streamsFromString(streamsParam);
        }
      });
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
        this.streams.forEach(s => s.player && s.player.setMuted(true));
        stream.player.setMuted(false);
      },
      streamReady({ streamId, player }) {
        const stream = this.streams.find(s => s.id === streamId);
        stream.ready = true;
        stream.player = player;
        if (stream === this.activeStream) {
          this.activateStream(streamId);
        } else {
          stream.player.setMuted(true);
        }
      },
      goHome() {
        history.push('');
      },
      toggleChat() {
        this.isChatOpen = ! this.isChatOpen;
      },
      addStreams() {
        const newStreams = this.streamsFromString(this.addStreamsString);
        this.addStreamsString = '';
        this.addingStreams = false;
        this.streams = this.streams.concat(newStreams);
      },
      streamsFromString(streams) {
        return streams
          .split(' ')
          .filter(s => s) // filter empty strings
          .map((streamId, idx) => ({
            id: streamId.trim(),
            order: this.streams.length + idx,
            active: this.streams.length + idx === 0,
            ready: false,
          }));
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
</style>
