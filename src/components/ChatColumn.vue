<template>
  <div id="chat-col"
    :class="{ open: isOpen }"
    @mouseover="toggleTabPeeking = true"
    @mouseleave="toggleTabPeeking = false">
    <div v-show="isOpen" id="chats">
      <chat v-for="chat in chats"
        :class="{ active: chat.active }"
        :channelId="chat.id"
        :initialized="chat.initialized"
        :testMode="testMode"
        :key="chat.id">
      </chat>
    </div>
    <toggle-tab
      orientation="right"
      :isPeeking="toggleTabPeeking"
      :isOpen="isOpen"
      @click="$emit('toggleChat')"
    ></toggle-tab>
  </div>
</template>

<script>
  import 'vue-awesome/icons/angle-left';
  import 'vue-awesome/icons/angle-right';
  import Icon from 'vue-awesome/components/Icon.vue';
  import Chat from './Chat.vue';
  import ToggleTab from './ToggleTab.vue';

  export default {
    data() {
      return { toggleTabPeeking: false };
    },
    props: {
      chats: { default: [] },
      isOpen: { default: true },
      testMode: { default: false },
    },
    components: { Chat, Icon, ToggleTab },
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

  .inactive-chat {
    position: relative;
    display: none;
  }
</style>
