<script setup lang="ts">
const LayoutHeader = defineAsyncComponent(() => import('@/components/layouts/header.vue'))
const LayoutAside = defineAsyncComponent(() => import('@/components/layouts/aside.vue'))
</script>

<template>
  <main class="layout">
    <aside class="left">
      <layout-aside></layout-aside>
    </aside>
    <aside class="right">
      <layout-header></layout-header>
      <section class="content">
        <router-view v-slot="{ Component, route }">
          <keep-alive :max="30">
            <component :is="Component" v-if="route.meta.cache" />
          </keep-alive>
          <component :is="Component" v-if="!route.meta.cache" />
        </router-view>
      </section>
    </aside>
  </main>
</template>

<style lang="scss" scoped>
:global(#app) {
  -webkit-app-region: drag;
}

.layout {
  height: 100vh;
  display: flex;
  padding: $padding-small;
  padding-right: 0;
  @include themeify {
    background-color: themed('color-bg');
    color: themed('color-text');
  }
}

.left {
  height: calc(100vh - 2 * $padding-small);
  border-radius: $radius-small;
  padding: $padding-small 0;
  @include themeify {
    background-color: themed('color-bg-aside');
  }
}

.right {
  flex: 1;
  height: 100%;
  padding-left: $padding-base;
  display: flex;
  flex-direction: column;
  gap: $padding-mini;

  .content {
    -webkit-app-region: no-drag;
    height: calc(100% - 24px);
    padding-right: $padding-small;
    overflow: auto;
  }
}
</style>
