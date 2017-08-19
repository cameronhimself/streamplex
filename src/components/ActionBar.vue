<template>
  <div id="action-bar"
    :class="{ hidden: ! isOpen }"
    :style="{ width: `${width}px` }"
    @mouseover="toggleTabPeeking = true"
    @mouseleave="toggleTabPeeking = false">
    <div v-if="isOpen" id="action-bar-content" :style="{ width: `${width}px` }">
      <div id="action-bar-items">
        <div class="logo">
          <action-bar-item @click="$emit('goHome')" :size="width">
            <img slot="content" src="../assets/logo.png" />
            <span slot="tooltip">Home</span>
          </action-bar-item>
        </div>
        <div class="middle">
          <div class="section">
            <!-- <action-bar-item @click="$emit('showSettings')" :size="width">
              <icon slot="content" name="cog"></icon>
              <span slot="tooltip">Settings</span>
            </action-bar-item> -->
            <action-bar-item @click="$emit('toggleChat')" :size="width" :disabled="! isChatOpen">
              <icon slot="content" name="commenting"></icon>
              <span slot="tooltip">Toggle chat</span>
            </action-bar-item>
            <action-bar-item @click="$emit('addStreams')" :size="width">
              <icon slot="content" name="plus"></icon>
              <span slot="tooltip">Add stream(s)</span>
            </action-bar-item>
          </div>
        </div>
      </div>
    </div>
    <toggle-tab
      orientation="left"
      :isPeeking="toggleTabPeeking"
      :isOpen="isOpen"
      @click="isOpen = ! isOpen"
    ></toggle-tab>
  </div>
</template>

<script>
  import 'vue-awesome/icons/cog';
  import 'vue-awesome/icons/commenting';
  import 'vue-awesome/icons/plus';
  import Icon from 'vue-awesome/components/Icon.vue';
  import ActionBarItem from './ActionBarItem.vue';
  import ToggleTab from './ToggleTab.vue';

  export default {
    data() {
      return {
        toggleTabPeeking: false,
        isOpen: true,
      };
    },
    props: {
      width: { default: 10 },
      bottomHeight: {},
      isChatOpen: {},
    },
    components: { ActionBarItem, ToggleTab, Icon },
  };
</script>

<style lang="scss">
  #action-bar {
    position: relative;
    display: table-cell;
    height: 100vh;
    box-sizing: border-box;
    border-right: 1px solid #000;
    z-index: 1;
    &.hidden {
      width: 1px !important;
    }

  }
  #action-bar-content {
    position: relative;
    width: 100%;
    height: 100%;
    background: #090909;
    z-index: 1;
  }
  #action-bar-items {
    .logo {
      border: none;
      > .action-bar-item {
        background-color: rgba(255,255,255,0.04);
      }
    }
    .section {
      padding-top: 7px;
      padding-bottom: 7px;
      &:first-child {
        padding-top: 0px;
      }
    }
  }
</style>
