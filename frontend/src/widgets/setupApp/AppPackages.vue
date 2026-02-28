<script setup lang="ts">
import FadeUpAnimation from "@/components/FadeUpAnimation.vue";
import Loading from "@/components/Loading.vue";
import { SEARCH_ALL_KEY, useMarketPackages } from "@/hooks/useMarketPackages";
import { t } from "@/lang/i18n";
import type { QuickStartPackages } from "@/types";
import { ArrowLeftOutlined } from "@ant-design/icons-vue";
import { computed, onMounted } from "vue";
import PackageDetailTable from "./PackageDetailTable.vue";

const props = defineProps<{
  title?: string;
  btnText?: string;
  showCustomBtn?: boolean;
  onlyDockerTemplate?: boolean;
}>();

const emit = defineEmits<{
  "handle-select-template": [item: QuickStartPackages | null];
}>();

const {
  searchForm,
  appListLoading,
  filteredList: appList,
  languageOptions: appLangList,
  gameTypeOptions: appGameTypeList,
  categoryOptions: appCategoryList,
  platformOptions: appPlatformList,
  handleReset,
  handleGameTypeChange,
  handleLanguageChange,
  handlePlatformChange,
  handleSelectTopCategory,
  fetchTemplate
} = useMarketPackages({
  onlyDockerTemplate: props.onlyDockerTemplate
});

/** Whether we are in category view (category cards); otherwise in detail list view */
const isCategoryView = computed(() => searchForm.gameType === SEARCH_ALL_KEY);

/** Detail list: filtered template list when not in category view */
const detailList = computed(() => (isCategoryView.value ? [] : appList.value));

const handleBackToCategory = () => {
  searchForm.gameType = SEARCH_ALL_KEY;
};

onMounted(() => {
  fetchTemplate();
});

defineExpose({
  appList,
  fetchTemplate
});
</script>

<template>
  <!-- Loading state - shows loading spinner while fetching package data -->
  <a-row v-if="appListLoading" :gutter="[24, 24]" style="height: 100%">
    <a-col :span="24">
      <div
        style="
          height: 50vh;
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: center;
        "
      >
        <div>
          <Loading />
        </div>
        <div style="margin-top: 20px; color: var(--color-gray-12)">
          {{ t("TXT_CODE_7fca723a") }}
        </div>
      </div>
    </a-col>
  </a-row>

  <!-- Main content - package marketplace interface -->
  <a-row v-else :gutter="[16, 16]" style="height: 100%">
    <a-col v-if="showCustomBtn" :span="24" :md="24" class="justify-end">
      <a-button
        type="link"
        style="margin: 0; padding: 0"
        @click="emit('handle-select-template', null)"
      >
        {{ t("TXT_CODE_181c72ba") }}
      </a-button>
    </a-col>

    <!-- Empty state - shown when no packages match current filters -->
    <a-col v-if="appList.length === 0" :span="24">
      <div style="display: flex; justify-content: center; align-items: center; height: 40vh">
        <a-typography-paragraph :style="{ color: 'var(--color-gray-7)' }">
          {{ t("TXT_CODE_7356e569") }}
        </a-typography-paragraph>
      </div>
    </a-col>

    <!-- Detail list: table with back-to-category -->
    <template v-else-if="detailList.length > 0">
      <a-col :span="24">
        <a-button type="text" class="mb-3" @click="handleBackToCategory">
          <template #icon>
            <ArrowLeftOutlined />
          </template>
          {{ t("TXT_CODE_c14b2ea3") }}
        </a-button>
      </a-col>
      <a-col :span="24">
        <PackageDetailTable
          :data-source="detailList"
          :btn-text="btnText"
          @select="(item) => emit('handle-select-template', item)"
        />
      </a-col>
    </template>

    <!-- Category view: card grid -->
    <fade-up-animation v-else>
      <a-col v-for="item in appList" :key="item.key" :span="24" :sm="24" :md="12" :lg="8">
        <div
          class="package-image-container-summary global-card-container-shadow"
          style="overflow: hidden; cursor: pointer"
          @click="handleSelectTopCategory(item)"
        >
          <div class="package-image-container" style="border-radius: 0">
            <img
              class="package-image cursor-pointer"
              style="height: 220px; border-radius: 0"
              :src="item.image"
              alt=""
              srcset=""
            />
          </div>
          <a-typography-title :level="5" class="flex-center package-subtitle mb-0">
            <span>{{ item.title }}</span>
          </a-typography-title>
        </div>
      </a-col>
    </fade-up-animation>
  </a-row>
</template>
