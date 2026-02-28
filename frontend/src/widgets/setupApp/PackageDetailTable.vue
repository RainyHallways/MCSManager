<script setup lang="ts">
import { t } from "@/lang/i18n";
import type { QuickStartPackages } from "@/types";
import type { AntTableCell } from "@/types/ant";
import { DownloadOutlined } from "@ant-design/icons-vue";
import { Flex } from "ant-design-vue";
import type { PackageTableColumnDef } from "./usePackageTableColumns";
import { usePackageTableColumns } from "./usePackageTableColumns";

withDefaults(
  defineProps<{
    dataSource: QuickStartPackages[];
    btnText?: string;
  }>(),
  { btnText: "" }
);

const emit = defineEmits<{
  select: [item: QuickStartPackages];
}>();

const { columns, columnDefs } = usePackageTableColumns();

function getColumnDef(key: string): PackageTableColumnDef | undefined {
  return columnDefs.value.find((c) => c.key === key);
}

function getCellText(record: QuickStartPackages, dataIndex: string): string {
  const v = record[dataIndex as keyof QuickStartPackages];
  if (v == null) return "";
  if (Array.isArray(v)) return v.join(", ");
  return String(v);
}

function platformDisplayText(platform: string): string {
  return String(platform).toLowerCase() === "all" ? t("TXT_CODE_all_platform") : platform;
}
</script>

<template>
  <a-table
    :data-source="dataSource"
    :columns="columns"
    :scroll="{ x: 'max-content' }"
    :pagination="{
      pageSize: 10,
      showSizeChanger: false,
      showTotal: (total: number) => t('TXT_CODE_TOTAL_ITEMS', { total })
    }"
    :row-key="
      (r: QuickStartPackages) => r.key ?? `${r.targetLink ?? ''}-${r.title ?? ''}-${r.language}`
    "
    size="middle"
  >
    <template #bodyCell="{ column, record }: AntTableCell">
      <!-- Image column -->
      <template v-if="getColumnDef(String(column.key ?? ''))?.isImage">
        <div class="package-table-image-cell">
          <template v-if="record.image">
            <a-popover trigger="hover" placement="top">
              <template #content>
                <div class="package-table-image-popover">
                  <img :src="record.image" alt="" class="package-table-image-popover-img" />
                </div>
              </template>
              <a-avatar
                :src="record.image"
                :size="48"
                shape="square"
                class="package-table-avatar"
              />
            </a-popover>
          </template>
          <span v-else class="package-table-thumb-placeholder">—</span>
        </div>
      </template>

      <!-- Title column -->
      <template v-else-if="column.key === 'title'">
        <div>
          <Flex>
            <a-typography-text style="font-size: 13px; font-weight: 600">
              {{ record.title }}
            </a-typography-text>
            <a-tag v-if="record?.setupInfo?.docker?.image" color="cyan" class="ml-8">
              {{ record?.setupInfo?.docker?.image }}
            </a-tag>
          </Flex>
          <a-tooltip :title="record.description" placement="topLeft">
            <a-typography-text type="secondary" class="package-table-description-cell">
              {{ record.description }}
            </a-typography-text>
          </a-tooltip>
        </div>
      </template>

      <template v-else-if="column.key === 'remark'">
        <a-tooltip :title="record.remark || '—'" placement="topLeft">
          <a-typography-text type="secondary" class="package-table-description-cell">
            {{ record.remark }}
          </a-typography-text>
        </a-tooltip>
      </template>

      <!-- Tags -->
      <template
        v-else-if="['runtime', 'hardware', 'platform', 'tags'].includes(String(column.key))"
      >
        <div class="ml-8">
          <a-tag color="blue">{{ getCellText(record, String(column.dataIndex)) }}</a-tag>
        </div>
      </template>
      <template v-else-if="column.key === 'author'">
        <div class="ml-8">
          <a-tag color="green">{{ getCellText(record, String(column.dataIndex)) }}</a-tag>
        </div>
      </template>

      <!-- Action column -->
      <template v-else-if="column.key === 'action'">
        <a-button type="primary" size="small" @click="emit('select', record)">
          <template #icon>
            <DownloadOutlined />
          </template>
          {{ btnText || t("TXT_CODE_1704ea49") }}
        </a-button>
      </template>

      <!-- Text column: ellipsis with full text on hover -->
      <template v-else>
        <a-tooltip
          :title="getCellText(record, String(column.dataIndex)) || '—'"
          placement="topLeft"
        >
          <span class="package-table-ellipsis-cell">
            {{ getCellText(record, String(column.dataIndex)) || "—" }}
          </span>
        </a-tooltip>
      </template>
    </template>
  </a-table>
</template>

<style scoped>
.package-table-image-cell {
  display: flex;
  align-items: center;
  justify-content: center;
}
.package-table-avatar {
  border-radius: 4px;
  cursor: pointer;
}
.package-table-avatar :deep(img) {
  object-fit: cover;
}
.package-table-image-popover {
  padding: 4px;
}
.package-table-image-popover-img {
  width: 500px;
  height: auto;
  max-height: 500px;
  object-fit: contain;
  display: block;
  border-radius: 4px;
}
.package-table-thumb-placeholder {
  color: var(--color-gray-6);
  font-size: 12px;
}
.package-table-ellipsis-cell {
  display: block;
  max-width: 100%;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.package-table-description-cell {
  font-size: 12px;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: normal;
}
</style>
