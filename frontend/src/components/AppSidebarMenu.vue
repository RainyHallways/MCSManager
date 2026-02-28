<script setup lang="ts">
import { useHeaderMenus } from "@/hooks/useHeaderMenus";
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

const { menus, appMenus, handleToPage } = useHeaderMenus();

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

const onRouteClick = (path: string) => {
  handleToPage(path);
};

type AppMenuItem = (typeof appMenus.value)[number];

const onAppMenuClick = (item: AppMenuItem, key?: Key) => {
  if (item.menus && key !== undefined) {
    item.click(String(key));
  } else if (!item.menus) {
    (item.click as () => void)();
  }
};

const onMenuClick = (item: AppMenuItem, info: { key: Key }) => {
  onAppMenuClick(item, info.key);
};
</script>

<template>
  <nav class="sidebar-menu">
    <!-- 路由菜单 -->
    <div class="sidebar-menu-section">
      <a
        v-for="item in menus"
        :key="item.path"
        class="sidebar-item"
        :class="item.customClass"
        @click.prevent="onRouteClick(item.path)"
      >
        <component :is="getRouteIcon(item.path)" class="sidebar-item-icon" />
        <span class="sidebar-item-text">{{ item.name }}</span>
      </a>
    </div>

    <!-- 分隔行 -->
    <div class="sidebar-divider" />

    <!-- 应用菜单 appMenus -->
    <div class="sidebar-menu-section">
      <template v-for="(item, index) in appMenus" :key="index">
        <a-dropdown v-if="item.menus && item.conditions" trigger="click" placement="topRight">
          <a class="sidebar-item" @click.prevent>
            <component :is="item.icon" v-if="item.icon" class="sidebar-item-icon" />
            <span class="sidebar-item-text">{{ item.title }}</span>
          </a>
          <template #overlay>
            <a-menu @click="(info) => onMenuClick(item, info)">
              <a-menu-item v-for="m in item.menus" :key="String(m.value)">
                {{ m.title }}
              </a-menu-item>
            </a-menu>
          </template>
        </a-dropdown>
        <a
          v-else-if="item.conditions"
          class="sidebar-item"
          :class="item.customClass"
          @click.prevent="onAppMenuClick(item)"
        >
          <component :is="item.icon" v-if="item.icon" class="sidebar-item-icon" />
          <span class="sidebar-item-text">{{ item.title }}</span>
        </a>
      </template>
    </div>
  </nav>
</template>

<style lang="scss" scoped>
.sidebar-menu {
  display: flex;
  flex-direction: column;
  align-items: end;
  padding: 12px 0;
  color: rgba(255, 255, 255, 0.85);
  min-height: 100%;
}

.sidebar-menu-section {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.sidebar-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 16px;
  color: inherit;
  text-decoration: none;
  cursor: pointer;
  border-radius: 6px;
  transition: background-color 0.2s ease;
  margin: 0 8px;

  &:hover {
    background-color: rgba(255, 255, 255, 0.08);
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
