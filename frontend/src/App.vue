<script setup lang="ts">
import UploadBubble from "@/components/UploadBubble.vue";
import { useAppConfigStore } from "@/stores/useAppConfigStore";

import { Button, Input, Select, Table } from "ant-design-vue";
import { onMounted } from "vue";
import { RouterView } from "vue-router";
import AppConfigProvider from "./components/AppConfigProvider.vue";
import AppSidebarMenu from "./components/AppSidebarMenu.vue";
import Breadcrumbs from "./components/Breadcrumbs.vue";
import InputDialogProvider from "./components/InputDialogProvider.vue";
import MyselfInfoDialog from "./components/MyselfInfoDialog.vue";
import { closeAppLoading, setLoadingTitle } from "./tools/dom";

const { hasBgImage, initAppTheme } = useAppConfigStore();

const GLOBAL_COMPONENTS = [InputDialogProvider, MyselfInfoDialog];

[Button, Select, Input, Table].forEach((element) => {
  element.props.size.default = "large";
});

onMounted(async () => {
  setLoadingTitle("Loading application settings...");
  await initAppTheme();
  closeAppLoading();
});
</script>

<template>
  <AppConfigProvider :has-bg-image="hasBgImage">
    <!-- App Container -->
    <div class="global-app-container global-app-container-with-sidebar">
      <aside class="left-sidebar">
        <div class="left-sidebar-menu">
          <AppSidebarMenu />
        </div>
      </aside>
      <main class="main-content">
        <!-- <AppHeader /> -->
        <Breadcrumbs />
        <RouterView :key="$route.fullPath" />
      </main>
      <aside class="right-sidebar"></aside>
      <UploadBubble />
    </div>

    <!-- Global Components -->
    <component :is="component" v-for="(component, index) in GLOBAL_COMPONENTS" :key="index" />
  </AppConfigProvider>
</template>

<style lang="scss" scoped>
.global-app-container-with-sidebar {
  display: flex;
  flex-wrap: nowrap;
  gap: 80px;
  min-height: 100vh;

  .left-sidebar-menu {
    top: 0;
    align-self: flex-start;
    position: fixed;
    width: 240px;
    min-height: 100vh;
    // height: 100%;
    text-align: left;
    // background-color: rgb(201, 201, 201);
    border-right: 1px solid var(--color-gray-5);
    background-image: url("@/assets/side.png");
    padding: 20px 24px;
  }

  .left-sidebar,
  .right-sidebar {
    flex: 1 1 0;
    min-width: 0;
  }

  .main-content {
    margin-top: 20px;
    flex: 0 1 1300px;
    min-width: 0;
  }
}
</style>
