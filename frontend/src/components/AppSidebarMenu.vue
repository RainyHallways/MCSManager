<script setup lang="ts">
import {
  useHeaderMenus,
  type SidebarAppDropdownEntry,
  type SidebarEntry
} from "@/hooks/useHeaderMenus";
import { useAppConfigStore } from "@/stores/useAppConfigStore";
import {
  ApartmentOutlined,
  AppstoreOutlined,
  DashboardOutlined,
  LinkOutlined,
  LoginOutlined,
  MenuOutlined,
  SettingOutlined,
  ShopOutlined,
  ShoppingOutlined,
  TeamOutlined,
  UserOutlined
} from "@ant-design/icons-vue";
import type { Key } from "ant-design-vue/es/table/interface";
import type { Component } from "vue";

const { sidebarItems, handleToPage } = useHeaderMenus();

/** 路由 path 对应的侧边栏图标 */
const routePathIcons: Record<string, Component> = {
  "/instances": AppstoreOutlined,
  "/market": ShopOutlined,
  "/overview": DashboardOutlined,
  "/users": TeamOutlined,
  "/node": ApartmentOutlined,
  "/settings": SettingOutlined,
  "/customer": UserOutlined,
  "/login": LoginOutlined,
  "/shop": ShoppingOutlined,
  "/_open_page": LinkOutlined
};

const getRouteIcon = (path: string): Component => {
  return routePathIcons[path] ?? MenuOutlined;
};

const getItemKey = (entry: SidebarEntry, index: number): string => {
  if (entry.type === "divider") return "sidebar-divider";
  if (entry.type === "route") return entry.path;
  return `app-${index}-${entry.title}`;
};

const onAppDropdownClick = (item: SidebarAppDropdownEntry, info: { key: Key }) => {
  item.click(String(info.key));
};
const { logoImage } = useAppConfigStore();
</script>

<template>
  <nav class="sidebar-menu">
    <div>
      <a href=".">
        <div class="logo">
          <img :src="logoImage" style="height: 18px" />
        </div>
      </a>
    </div>
    <div class="sidebar-menu-section">
      <template v-for="(entry, index) in sidebarItems" :key="getItemKey(entry, index)">
        <!-- 分隔线 -->
        <div v-if="entry.type === 'divider'" class="sidebar-divider" />

        <!-- 路由链接 -->
        <a
          v-else-if="entry.type === 'route'"
          class="sidebar-item"
          :class="entry.customClass"
          @click.prevent="handleToPage(entry.path)"
        >
          <component :is="getRouteIcon(entry.path)" class="sidebar-item-icon" />
          <span class="sidebar-item-text">{{ entry.name }}</span>
        </a>

        <!-- 应用菜单（下拉） -->
        <a-dropdown v-else-if="entry.type === 'app-dropdown'" trigger="click" placement="topRight">
          <a class="sidebar-item" @click.prevent>
            <component :is="entry.icon" v-if="entry.icon" class="sidebar-item-icon" />
            <span class="sidebar-item-text">{{ entry.title }}</span>
          </a>
          <template #overlay>
            <a-menu @click="(info) => onAppDropdownClick(entry, info)">
              <a-menu-item v-for="m in entry.menus" :key="String(m.value)">
                {{ m.title }}
              </a-menu-item>
            </a-menu>
          </template>
        </a-dropdown>

        <!-- 应用菜单（单点） -->
        <a
          v-else-if="entry.type === 'app'"
          class="sidebar-item"
          :class="entry.customClass"
          @click.prevent="entry.click()"
        >
          <component :is="entry.icon" v-if="entry.icon" class="sidebar-item-icon" />
          <span class="sidebar-item-text">{{ entry.title }}</span>
        </a>
      </template>
    </div>
  </nav>
</template>

<style lang="scss" scoped>
.logo {
  margin-bottom: 20px;
  margin-right: 10px;
}

.sidebar-menu {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding: 14px 0;
  color: rgba(255, 255, 255, 0.85);
  min-height: 100%;
}

.sidebar-menu-section {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.sidebar-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 32px 12px 20px;
  color: inherit;
  text-decoration: none;
  cursor: pointer;
  border-radius: 6px;
  transition: all 0.4s ease;
  margin: 0 8px;

  &:hover {
    background-color: rgba(255, 255, 255, 0.178);
  }

  .sidebar-item-icon {
    font-size: 16px;
    flex-shrink: 0;
  }

  .sidebar-item-text {
    font-size: 14px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
}

.sidebar-divider {
  height: 1px;
  background-color: rgba(255, 255, 255, 0.12);
  margin: 12px 12px;
  flex-shrink: 0;
}

/* 与 AppHeader 一致的语义化高亮 */
:deep(.nav-button-warning:hover) {
  background-color: rgba(255, 193, 7, 0.2) !important;
}

:deep(.nav-button-success:hover) {
  background-color: rgba(64, 156, 216, 0.15) !important;
}

:deep(.nav-button-danger:hover) {
  background-color: rgba(255, 25, 17, 0.25) !important;
}
</style>
