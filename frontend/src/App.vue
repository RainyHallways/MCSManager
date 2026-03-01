<script setup lang="ts">
import UploadBubble from "@/components/UploadBubble.vue";
import { useAppConfigStore } from "@/stores/useAppConfigStore";

import { Button, Input, Select, Table } from "ant-design-vue";
import { computed, onMounted } from "vue";
import { RouterView } from "vue-router";
import AppConfigProvider from "./components/AppConfigProvider.vue";
import AppHeader from "./components/AppHeader.vue";
import AppSidebarMenu from "./components/AppSidebarMenu.vue";
import Breadcrumbs from "./components/Breadcrumbs.vue";
import InputDialogProvider from "./components/InputDialogProvider.vue";
import MyselfInfoDialog from "./components/MyselfInfoDialog.vue";
import { closeAppLoading, setLoadingTitle } from "./tools/dom";

const { hasBgImage, initAppTheme, sidebarPosition } = useAppConfigStore();

/** Whether to show the left sidebar; when false, only top header (AppHeader) is used. */
const useSidebarLayout = computed(() => sidebarPosition.value === "left");

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
    <AppSidebarMenu v-if="useSidebarLayout" />

    <!-- App Container -->
    <div class="global-app-container" :class="{ 'app-layout-header-only': !useSidebarLayout }">
      <main class="main-content">
        <AppHeader v-if="!useSidebarLayout" />
        <Breadcrumbs />
        <RouterView :key="$route.fullPath" />
      </main>

      <UploadBubble />
    </div>

    <!-- Global Components -->
    <component :is="component" v-for="(component, index) in GLOBAL_COMPONENTS" :key="index" />
  </AppConfigProvider>
</template>
