<template>
  <button
    class="toggle-tab"
    :class="{ closed: ! isOpen, peeking: isPeeking }"
    :id="htmlId"
    :style="style"
    @click="$emit('click')">
    <icon v-if="isOpen" :name="`angle-${orientation}`"></icon>
    <icon v-else :name="`angle-${oppositeOrientation}`"></icon>
  </button>
</template>

<script>
  import 'vue-awesome/icons/angle-left';
  import 'vue-awesome/icons/angle-right';
  import Icon from 'vue-awesome/components/Icon.vue';

  export default {
    props: {
      isOpen: { default: true },
      isPeeking: { default: false },
      size: { default: 50 },
      htmlId: {},
      orientation: {},
    },
    components: { Icon },
    computed: {
      oppositeOrientation() {
        return this.orientation === 'left' ? 'right' : 'left';
      },
      peekWidth() {
        return this.size / 3;
      },
      style() {
        const style = {
          [this.oppositeOrientation]: this.isPeeking ? `-${this.peekWidth}px` : '0px',
          padding: `0px ${this.size / 10}px`,
          fontSize: `${this.size / 2}px`,
          textAlign: this.oppositeOrientation,
          width: `${this.size}px`,
          height: `${this.size}px`,
        };
        if (! this.isOpen) {
          style['position'] = 'fixed';
          style[this.oppositeOrientation] = 'auto';
          style[this.orientation] = `-${this.size - this.peekWidth}px`;
        }
        return style;
      },
    },
  };
</script>

<style lang="scss">
  .toggle-tab {
    transition: left 0.4s, right 0.4s, color 0.4s, opacity 0.4s;
    position: absolute;
    display: block;
    box-sizing: border-box;
    background: #222;
    color: #555;
    top: 50%;
    border-radius: 50%;
    line-height: 100%;
    text-align: left;
    transform: translateY(-50%);
    border: 1px solid #666;
    z-index: -1;
    &.closed {
      opacity: 0.3;
      position: fixed;
      &:hover {
        opacity: 1;
      }
    }
    &.peeking {
      color: #fff;
    }
  }

</style>
