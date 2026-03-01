<script setup lang="ts">
import UploadBubble from "@/components/UploadBubble.vue";
import { useAppConfigStore } from "@/stores/useAppConfigStore";

import { Button, Input, Select, Table } from "ant-design-vue";
import { onMounted } from "vue";
import { RouterView } from "vue-router";
import AppConfigProvider from "./components/AppConfigProvider.vue";
import AppHeader from "./components/AppHeader.vue";
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
    <AppSidebarMenu />

    <!-- App Container -->
    <div class="global-app-container">
      <main class="main-content">
        <AppHeader />
        <Breadcrumbs />
        <RouterView :key="$route.fullPath" />
      </main>

      <UploadBubble />
    </div>

    <!-- Global Components -->
    <component :is="component" v-for="(component, index) in GLOBAL_COMPONENTS" :key="index" />
  </AppConfigProvider>
</template>
