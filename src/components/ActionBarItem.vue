<template>
  <button class="action-bar-item"
    :style="{ height: `${size}px` }"
    :class="{ disabled }"
    @click="$emit('click')"
    @mouseover="tooltipVisible = true"
    @mouseleave="tooltipVisible = false">
    <span class="content">
      <slot name="content" class="icon"></slot>
    </span>
    <span v-show="tooltipVisible" class="tooltip">
      <slot name="tooltip">{{ tooltip }}</slot>
    </span>
  </button>
</template>

<script>
  export default {
    data() {
      return { tooltipVisible: false };
    },
    props: ['url', 'event', 'size', 'tooltip', 'disabled'],
  };
</script>

<style lang="scss">
  .action-bar-item {
    display: block;
    transition: background-color 0.2s;
    position: relative;
    box-sizing: border-box;
    padding: 9px;
    width: 100%;
    text-decoration: none;
    text-align: center;
    vertical-align: middle;
    &:hover {
      background-color: rgba(255,255,255,0.04);
      > .content {
        opacity: 1;
      }
      > .tooltip {
        right: 5px;
      }
    }
    &.disabled {
      > .content {
        opacity: 0.1;
      }
    }
    > .content {
      display: block;
      transition: opacity 0.2s;
      opacity: 0.6;
      font-family: 'Arial', sans-serif;
      text-decoration: none;
      color: #fff;
      height: 100%;
      width: 100%;
      font-size: 30px;
      text-align: center;
      > svg {
        width: 100%;
        height: 100%;
        display: block;
      }
      > span {
        display: block;
        width: 100%;
        height: 100%;
        line-height: 100%;
      }
      > img {
        display: block;
        width: 100%;
        height: auto;
      }
    }
    > .tooltip {
      position: absolute;
      text-align: left;
      top: 50%;
      transform: translateY(-50%);
      left: 100%;
      font-size: 14px;
      width: 90px;
      background-color: rgba(0,0,0,0.8);
      padding: 5px;
      color: #fff;
      margin-left: 5px;
      z-index: 2;
    }
  }
</style>
