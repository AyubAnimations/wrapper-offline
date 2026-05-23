<style lang="css" scoped>
#right_page_container {
	background: #1c1c27;
	user-select: none;
	overflow: hidden;
	display: flex;
	flex-direction: column;
	transition: background-color 0.2s ease;
	min-height: 100vh;
	width: 100%;
}

.page_contents {
	background: hsl(252deg 16% 94%);
	overflow: auto;
	flex-grow: 1;
	padding: 0;
	margin: 0;
	transition: background-color 0.2s ease;
	min-width: 0;
}

.page_contents::-webkit-scrollbar {
	width: 12px;
}

.page_contents::-webkit-scrollbar-thumb {
	background: rgba(0, 0, 0, 0.2);
	border-radius: 6px;
	border: 3px solid hsl(252deg 16% 94%);
}

.page_contents::-webkit-scrollbar-thumb:hover {
	background: rgba(0, 0, 0, 0.3);
}

/* Loading State */
.page_contents.loading {
	opacity: 0.7;
	pointer-events: none;
}

/* Dark Mode Support */
html.dark #right_page_container {
	background: hsl(250 10% 9% / 1);
}

html.dark .page_contents {
	background: hsl(250 9% 16% / 1);
}

html.dark .page_contents::-webkit-scrollbar-thumb {
	background: rgba(255, 255, 255, 0.3);
	border-color: hsl(250 9% 16% / 1);
}

html.dark .page_contents::-webkit-scrollbar-thumb:hover {
	background: rgba(255, 255, 255, 0.5);
}

/* Responsive Design */
@media (max-width: 768px) {
	#right_page_container {
		width: 100% !important;
	}
}

/* Reduced Motion */
@media (prefers-reduced-motion: reduce) {
	#right_page_container,
	.page_contents {
		transition: none;
	}
}
</style>

<script setup lang="ts">
import { ref, computed, watch } from "vue";
import { useRouter } from "vue-router";
import { useTemplateRef } from "vue";
import Sidebar from "./components/Sidebar.vue";
import useLocalSettings from "./composables/useLocalSettings";

/**
 * Type definition for Sidebar component
 */
type SidebarType = InstanceType<typeof Sidebar>;

/**
 * Local Settings
 */
const localSettings = useLocalSettings();
const router = useRouter();

/**
 * Template references
 */
const sidebar = useTemplateRef<SidebarType>("sidebar");

/**
 * State
 */
const isLoading = ref(false);

/**
 * Initialize dark mode on mount
 */
const initializeDarkMode = () => {
	if (localSettings.darkMode === true) {
		document.documentElement.classList.add("dark");
	} else {
		document.documentElement.classList.remove("dark");
	}
};

initializeDarkMode();

/**
 * Watch for dark mode changes
 */
watch(
	() => localSettings.darkMode,
	(newValue) => {
		if (newValue) {
			document.documentElement.classList.add("dark");
		} else {
			document.documentElement.classList.remove("dark");
		}
	},
	{ immediate: true }
);

/**
 * Watch router to handle loading state
 */
watch(
	() => router.currentRoute.value,
	() => {
		isLoading.value = true;
		setTimeout(() => {
			isLoading.value = false;
		}, 300);
	}
);

/**
 * Computed sidebar width with validation
 */
const sidebarWidth = computed(() => {
	if (!sidebar.value) return 0;
	const width = sidebar.value.width ?? 0;
	const margin = sidebar.value.slideMode?.margin ?? 0;
	return Math.max(0, width + margin);
});
</script>

<template>
	<!-- Sidebar Component -->
	<Sidebar ref="sidebar">
		<!-- Page specific content slot -->
		<template #page_specific>
			<slot name="hi" />
		</template>
	</Sidebar>

	<!-- Main page container with dynamic width -->
	<RouterView
		id="right_page_container"
		:class="{ loading: isLoading }"
		:style="{ 
			width: `calc(100% - ${sidebarWidth}px)`,
			minWidth: '0'
		}"
	/>
</template>
