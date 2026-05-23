<style lang="css">
@import "/node_modules/modern-normalize/modern-normalize.css";
@import "/css/icons.css";

:root {
	--popup-gradient-bg: radial-gradient(hsl(240deg 16% 22%), hsl(240deg 16% 8%));
	--slide-anim: cubic-bezier(0, 1.15, 0.7, 1);
	--button-anim: cubic-bezier(0.2, 0.95, 0.25, 1);
	--primary-color: #338cec;
	--text-dark: hsl(218deg 14% 36%);
	--text-light: hsl(0deg 0% 82%);
	--bg-light: hsl(252deg 16% 94%);
	--bg-dark: #000;
}

/**
general
**/
html, body {
	color: var(--text-dark);
	font: 15px/1.7 "Lato", Arial, sans-serif;
	overflow: hidden;
	width: 100%;
	height: 100%;
	margin: 0;
	padding: 0;
}

body.col_resize {
	cursor: col-resize !important;
}

#app {
	display: flex; 
	flex-direction: row;
	height: 100%;
	width: 100%;
}

a {
	color: var(--primary-color);
	text-decoration: none;
	transition: text-decoration 0.2s ease;
}

a:hover {
	text-decoration: underline;
}

a:focus-visible {
	outline: 2px solid var(--primary-color);
	outline-offset: 2px;
}

.tooltip {
	background: hsl(240deg 17% 23% / .9);
	position: fixed;
	color: #fff;
	border-radius: 4px;
	pointer-events: none;
	padding: 6px 12px;
	font-size: 14px;
	font-weight: 500;
	animation: 0.15s var(--slide-anim) tooltip_fade;
	z-index: 99999;
	box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

/* Scrollbar Styling */
::-webkit-scrollbar {
	width: 12px;
	height: 12px;
	background: transparent;
}

::-webkit-scrollbar-thumb {
	background: #0004;
	border: 4px solid var(--bg-light);
	border-radius: 10px;
	transition: background 0.2s ease;
}

::-webkit-scrollbar-thumb:hover {
	background: #0006;
}

::-webkit-scrollbar-track {
	background: transparent;
}

/* Dark Mode */
html.dark ::-webkit-scrollbar-thumb {
	background: #fff6;
	border-color: hsl(250 9% 16% / 1);
}

html.dark ::-webkit-scrollbar-thumb:hover {
	background: #fff9;
}

html.dark {
	--popup-gradient-bg: radial-gradient(#1e1d25, #0e0d11);
	--text-dark: var(--text-light);
	--bg-light: hsl(250 9% 16% / 1);
	--bg-dark: #000;
}

html.dark, html.dark > body {
	background: var(--bg-dark);
	color: var(--text-light);
}

/* Animations */
@keyframes tooltip_fade {
	0% {
		opacity: 0;
		transform: translateY(-10px);
	}
	100% {
		opacity: 1;
		transform: translateY(0);
	}
}

/* Font Faces */
@font-face {
	font-family: "Lato";
	font-style: normal;
	font-weight: 400;
	font-display: swap;
	src: url(/fonts/latonormal400.woff2) format("woff2");
}

@font-face {
	font-family: "Lato";
	font-style: normal;
	font-weight: 700;
	font-display: swap;
	src: url(/fonts/latonormal700.woff2) format("woff2");
}

@font-face {
	font-family: "Lato";
	font-style: italic;
	font-weight: 700;
	font-display: swap;
	src: url(/fonts/latoitalic700.woff2) format("woff2");
}

/* Print Styles */
@media print {
	body {
		background: white;
		color: black;
	}
}
</style>

<script setup lang="ts">
import { onMounted, onErrorCaptured } from "vue";
import { useRouter } from "vue-router";

/**
 * Handle redirect parameter from URL
 */
const params = new URLSearchParams(window.location.search);
if (params.get("redirect")) {
	const router = useRouter();
	const to = params.get("redirect") as string;
	router.push(to).catch(err => {
		console.error("Navigation error:", err);
	});
}

/**
 * Remove noscript element after Vue mounts
 */
onMounted(() => {
	const noscriptElement = document.getElementById("noscript");
	if (noscriptElement) {
		noscriptElement.remove();
	}
});

/**
 * Global error handling
 */
onErrorCaptured((err) => {
	console.error("Vue Error:", err);
	return false;
});
</script>

<template>
	<RouterView />
	
	<!-- Fallback for JavaScript disabled -->
	<noscript id="noscript">
		<div style="padding: 20px; text-align: center;">
			<h1>Wrapper: Offline</h1>
			<h2>JavaScript Required</h2>
			<p>This application requires JavaScript to function properly.</p>
			<p>Please enable JavaScript in your browser settings and refresh the page.</p>
		</div>
	</noscript>
</template>
