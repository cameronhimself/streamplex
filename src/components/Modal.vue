<template>
  <transition name="modal-fade">
    <div class="modal-backdrop" @click="$emit('close')" v-show="open">
      <div class="modal-dialog" @click.stop>
        <button class="modal-close" @click="$emit('close')">&times;</button>
        <div class="modal-content">
          <slot></slot>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  export default {
    props: {
      open: { default: false },
    },
  };
</script>

<style lang="scss">
  .modal-backdrop {
    position: fixed;
    background: rgba(0,0,0,0.8);
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 999;
  }
  .modal-dialog {
    border-radius: 5px;
    max-width: 600px;
    box-sizing: border-box;
    background: rgba(25,25,25,0.9);
    border-top: 1px solid #333;
    border-bottom: 1px solid #000;
    position: absolute;
    margin: auto;
    margin-top: 80px;
    left: 0;
    right: 0;
    padding: 38px 44px;
    box-shadow: 0px 12px 20px 6px rgba(0,0,0,0.47);
  }
  .modal-close {
    transition: background-color 0.2s, color 0.2s;
    color: #888;
    background-color: rgba(255,255,255,0.05);
    border-radius: 0 5px 0 0;
    font-family: 'Arial', sans-serif;
    font-size: 24px;
    display: block;
    padding: 5px;
    line-height: 0.55;
    position: absolute;
    right: 0px;
    top: 0px;
    &:hover {
      color: #fff;
      background-color: rgba(255,255,255,0.1);
    }
  }
  .modal-fade-enter-active, .modal-fade-leave-active {
    transition: opacity 0.2s
  }
  .modal-fade-enter, .modal-fade-leave-to {
    opacity: 0
  }
</style>
