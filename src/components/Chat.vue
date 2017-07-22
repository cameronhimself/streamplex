<template>
  <div class="chat" v-if="initialized">
    <iframe v-if="! testMode" :src="`https://www.twitch.tv/${channelId}/chat`" height="100%" width="340px"></iframe>
  </div>
</template>

<script>
  export default {
    props: {
      channelId: {},
      initialized: {},
      testMode: { default: false },
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
