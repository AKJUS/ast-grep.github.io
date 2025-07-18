<script setup lang="ts">
import { onMounted, onUnmounted, shallowRef } from 'vue'
import Intro from './components/Intro.vue'
import Toast from './components/utils/Toast.vue'
// vitepress SSR does not support Monaco, lazy load on client side
let playground = shallowRef<unknown>(null)

// setup global style
import styles from './style.css?inline'
const styleId = 'playground-style'
function insertStyle() {
  const style = document.createElement('style')
  style.id = styleId
  style.textContent = styles
  document.head.appendChild(style)
}

onMounted(async () => {
  // apply playground style override, see style.css
  insertStyle()
  document.body.classList.add('playground')
  playground.value = (await import('./components/Playground.vue')).default
})
onUnmounted(() => {
  // remove playground style override
  document.body.classList.remove('playground')
  const style = document.getElementById(styleId)
  style?.remove()
})
</script>

<template>
  <div class="root">
    <Intro />
    <Suspense>
      <component v-if="playground" :is="playground" />
      <div class="loading" v-else>
        <svg
          class="lightning-icon"
          xmlns="http://www.w3.org/2000/svg"
          xmlns:xlink="http://www.w3.org/1999/xlink"
          width="100"
          height="120"
          viewBox="0 10 100 120"
        >
          <g transform="matrix(1,0,0,1,0,22)">
            <polygon
              xmlns="http://www.w3.org/2000/svg"
              points="100,0 25,50 43.75,62.5 0,100 75,62.5 56.25,50"
            >
            </polygon>
          </g>
        </svg>
        <h2>
          Loading Editor and Parser...
        </h2>
      </div>
    </Suspense>
    <Toast />
  </div>
</template>

<style scoped>
.logo {
  height: 9em;
  padding: 1.5em;
  will-change: filter;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #3e699166);
}
.root {
  width: 100%;
  height: calc(100vh - var(--vp-nav-height) - 10px);
  display: flex;
  flex-direction: column;
  padding: 0 2rem;
  box-sizing: border-box;
}

@media only screen and (max-width: 780px) {
  .root {
    padding: 0 0.5rem;
  }
}

.description {
  text-align: left;
}
.loading {
  display: flex;
  height: 100%;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

h2 {
  font-size: 20px;
  margin-top: 5vh;
}

@keyframes lightning-flash {
  0% {
    stroke-dashoffset: 1000;
    fill: #ffe91000;
  }
  20% {
    stroke-dashoffset: 800;
    fill: #ffe91000;
  }
  50% {
    stroke-dashoffset: 0;
    fill: #ffe910ff;
    opacity: 1;
  }
  60% {
    filter: drop-shadow(0 0 0px #fcf1ab00);
    opacity: 0;
  }
  70% {
    opacity: 1;
  }
  80% {
    opacity: 0;
  }
  90% {
    opacity: 1;
  }
  100% {
    filter: drop-shadow(0 0 20px #fcf1abff);
  }
}
.lightning-icon {
  fill: #ffe910ff;
  stroke-dasharray: 1000;
  stroke-width: 2px;
  stroke: var(--theme-highlight3);
  transform: rotate(-10deg);
  animation: linear 4s lightning-flash forwards infinite;
}
</style>
