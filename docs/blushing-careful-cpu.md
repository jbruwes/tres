---
title:
description:
keywords:
  -
attrs:
  un-cloak: true
  class:
    - container
    - mx-auto
    - prose
    - prose-sm
    - sm:prose-base
    - dark:prose-invert
hidden: false
template: false
icon: twemoji:page-facing-up
script:
  - type: importmap
    innerHTML: |
      {
        "imports": {
          "@tresjs/core": "https://cdn.jsdelivr.net/npm/@tresjs/core@5.8.3",
          "@tresjs/cientos": "https://cdn.jsdelivr.net/npm/@tresjs/cientos@5.8.1",
          "@tresjs/post-processing": "https://cdn.jsdelivr.net/npm/@tresjs/post-processing@3.7.4",
          "@vueuse/core": "https://cdn.jsdelivr.net/npm/@vueuse/core@14.3.0/dist/index.js",
          "@vueuse/shared": "https://cdn.jsdelivr.net/npm/@vueuse/shared@14.3.0/dist/index.js",
          "@pmndrs/pointer-events": "https://cdn.jsdelivr.net/npm/@pmndrs/pointer-events@6.6.30/dist/index.js",
          "@vue/devtools-api": "https://cdn.jsdelivr.net/npm/@vue/devtools-api@8.2.1/dist/vue-devtools-api.esm-browser.js",
          "camera-controls": "https://cdn.jsdelivr.net/npm/camera-controls@3.1.2/dist/camera-controls.module.js",
          "three-stdlib": "https://cdn.jsdelivr.net/npm/three-stdlib@2.36.1/index.js",
          "radashi": "https://cdn.jsdelivr.net/npm/radashi@12.9.1/dist/radashi.js",
          "three": "https://cdn.jsdelivr.net/npm/three@0.184.0/build/three.module.js",
          "three/addons/": "https://cdn.jsdelivr.net/npm/three@0.184.0/examples/jsm/",
          "three/examples/": "https://cdn.jsdelivr.net/npm/three@0.184.0/examples/",
          "stats.js": "https://cdn.jsdelivr.net/npm/stats.js@0.17.0/src/Stats.js",
          "stats-gl": "https://cdn.jsdelivr.net/npm/stats-gl@4.2.3/dist/main.js",
          "three-mesh-bvh": "https://cdn.jsdelivr.net/npm/three-mesh-bvh@0.9.13/build/index.module.js",
          "three-custom-shader-material/vanilla": "https://cdn.jsdelivr.net/npm/three-custom-shader-material@6.4.0/vanilla/three-custom-shader-material.es.js",
          "potpack": "https://cdn.jsdelivr.net/npm/potpack@2.1.0",
          "fflate": "https://cdn.jsdelivr.net/npm/fflate@0.8.3/esm/browser.js"
        }
      }
---

<TresCanvas shadows alpha window-size>
<TresPerspectiveCamera :args="[45, 1, 0.1, 1000]" />
<OrbitControls />
</TresCanvas>

<script setup>
import { TresCanvas } from "@tresjs/core";
import { OrbitControls } from "@tresjs/cientos";
</script>