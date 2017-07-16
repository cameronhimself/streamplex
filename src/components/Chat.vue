<template>
  <div class="chat">
    <iframe :src="`https://www.twitch.tv/${channelId}/chat`" height="100%" width="340px"></iframe>
  </div>
</template>

<script>
  export default {
    props: ['channelId'],
  };
</script>

<style>
  .chat {
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
