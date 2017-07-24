<template>
  <div id="chat-col" :class="{ open: isOpen }">
    <div v-show="isOpen" id="chats">
      <chat v-for="chat in chats"
        :class="{ active: chat.active }"
        :channelId="chat.id"
        :initialized="chat.initialized"
        :testMode="testMode"
        :key="chat.id">
      </chat>
    </div>
    <button id="chat-toggle" :class="{ closed: ! isOpen }" @click="$emit('toggleChat')">
      <span v-if="isOpen">&rsaquo;</span>
      <span v-else>&lsaquo;</span>
    </button>
  </div>
</template>

<script>
  import Chat from './Chat.vue';

  export default {
    props: {
      chats: { default: [] },
      isOpen: { default: true },
      testMode: { default: false },
    },
    components: { Chat },
  };
</script>

<style lang="scss">
  #chat-col {
    position: relative;
    z-index: 1;
    vertical-align: top;
    text-align: right;
    width: 1px;
    &:hover {
      > #chat-toggle {
        opacity: 1;
        color: #fff;
      }
    }
    &.open:hover {
      > #chat-toggle {
        left: -20px;
      }
    }
  }

  #chat-toggle {
    transition: left 0.4s, color 0.4s, opacity 0.4s;
    position: absolute;
    display: block;
    box-sizing: border-box;
    width: 50px;
    height: 50px;
    background: #222;
    color: #555;
    top: 50%;
    border-radius: 50%;
    padding-left: 5px;
    font-family: 'Arial', sans-serif;
    font-size: 18px;
    left: 0px;
    text-align: left;
    transform: translateY(-50%);
    z-index: -1;
    border: 1px solid #666;
    &.closed {
      opacity: 0.3;
      position: fixed;
      left: auto;
      right: -30px;
    }
  }

  .inactive-chat {
    position: relative;
    display: none;
  }
</style>
