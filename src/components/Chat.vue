<template>
  <div  v-if="initialized" class="chat" :style="{ width: `${width}px` }">
    <iframe v-if="! testMode" :src="`https://www.twitch.tv/${channelId}/chat`" height="100%" :width="`${width}px`"></iframe>
  </div>
</template>

<script>
  export default {
    props: {
      channelId: {},
      initialized: {},
      testMode: { default: false },
      width: { default: 340 },
    },
  };
</script>

<style>
  .chat {
    background: #090909;
    width: 340px;
    height: 100vh;
    display: none;
  }

  .chat.active {
    display: block;
  }

  .chat iframe {
    display: block;
    border: none;
  }
</style>
