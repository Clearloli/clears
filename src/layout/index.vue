<script lang="ts" setup>
import { computed } from 'vue';
import { useAppStore, DeviceType } from '@/store/modules/app';
import { useSettingsStore } from '@/store/modules/settings';
import { AppMain, NavigationBar, Settings, Sidebar, TagsView, RightPanel } from './components';
import useResize from './hooks/useResize';

const appStore = useAppStore();
const settingsStore = useSettingsStore();

/** Layout 布局响应式 */
useResize();

const classObj = computed(() => {
  return {
    hideSidebar: !appStore.sidebar.opened,
    openSidebar: appStore.sidebar.opened,
    withoutAnimation: appStore.sidebar.withoutAnimation,
    mobile: appStore.device === DeviceType.Mobile,
    showGreyMode: showGreyMode.value,
    showColorWeakness: showColorWeakness.value,
  };
});

const showSettings = computed(() => {
  return settingsStore.showSettings;
});
const showTagsView = computed(() => {
  return settingsStore.showTagsView;
});
const fixedHeader = computed(() => {
  return settingsStore.fixedHeader;
});
const showGreyMode = computed(() => {
  return settingsStore.showGreyMode;
});
const showColorWeakness = computed(() => {
  return settingsStore.showColorWeakness;
});
const handleClickOutside = () => {
  appStore.closeSidebar(false);
};
</script>

<template>
  <div :class="classObj" class="app-wrapper">
    <div v-if="classObj.mobile && classObj.openSidebar" class="drawer-bg" @click="handleClickOutside" />
    <Sidebar class="sidebar-container" />
    <div :class="{ hasTagsView: showTagsView }" class="main-container">
      <div :class="{ 'fixed-header': fixedHeader }">
        <NavigationBar />
        <TagsView v-if="showTagsView" />
      </div>
      <AppMain />
      <RightPanel v-if="showSettings">
        <Settings />
      </RightPanel>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import "@/styles/mixins.scss";

.app-wrapper {
	position: relative;
	width: 100%;

@include clearfix;
}

.showGreyMode {
	filter: grayscale(1);
}

.showColorWeakness {
	filter: invert(.8);
}

.drawer-bg {
	position: absolute;
	top: 0;
	z-index: 999;
	width: 100%;
	height: 100%;
	background-color: #000;
	opacity: .3;
}

.main-container {
	position: relative;
	margin-left: var(--v3-sidebar-width);
	min-height: 100%;
	transition: margin-left .28s;
}

.sidebar-container {
	position: fixed;
	top: 0;
	bottom: 0;
	left: 0;
	z-index: 1001;
	overflow: hidden;
	width: var(--v3-sidebar-width) !important;
	height: 100%;
	font-size: 0;
	transition: width .28s;
}

.fixed-header {
	position: fixed;
	top: 0;
	right: 0;
	z-index: 9;
	width: calc(100% - var(--v3-sidebar-width));
	transition: width .28s;
}

.hideSidebar {
	.main-container {
		margin-left: var(--v3-sidebar-hide-width);
	}
	.sidebar-container {
		width: var(--v3-sidebar-hide-width) !important;
	}
	.fixed-header {
		width: calc(100% - var(--v3-sidebar-hide-width));
	}
}

// for mobile response 适配移动端
.mobile {
	.main-container {
		margin-left: 0;
	}
	.sidebar-container {
		width: var(--v3-sidebar-width) !important;
		transition: transform .28s;
	}
	&.openSidebar {
		position: fixed;
		top: 0;
	}
	&.hideSidebar {
		.sidebar-container {
			transition-duration: .3s;
			transform: translate3d(calc(0px - var(--v3-sidebar-width)), 0, 0);
			pointer-events: none;
		}
	}

	.fixed-header {
		width: 100%;
	}
}

.withoutAnimation {
	.main-container,
	.sidebar-container {
		transition: none;
	}
}
</style>
