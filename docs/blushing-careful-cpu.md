---
title: tres
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
template: true
icon: twemoji:page-facing-up
script:
  - type: importmap
    innerHTML: |
      {
        "imports": {
          "@tresjs/core": "https://cdn.jsdelivr.net/npm/@tresjs/core@5.8",
          "@tresjs/cientos": "https://cdn.jsdelivr.net/npm/@tresjs/cientos@5.8",
          "@tresjs/post-processing": "https://cdn.jsdelivr.net/npm/@tresjs/post-processing@3.7",
          "@vueuse/core": "https://cdn.jsdelivr.net/npm/@vueuse/core@14.3/dist/index.js",
          "@vueuse/shared": "https://cdn.jsdelivr.net/npm/@vueuse/shared@14.3/dist/index.js",
          "@pmndrs/pointer-events": "https://cdn.jsdelivr.net/npm/@pmndrs/pointer-events@6.6/dist/index.js",
          "@vue/devtools-api": "https://cdn.jsdelivr.net/npm/@vue/devtools-api@8.2/dist/vue-devtools-api.esm-browser.js",
          "camera-controls": "https://cdn.jsdelivr.net/npm/camera-controls@3.1/dist/camera-controls.module.js",
          "three-stdlib": "https://cdn.jsdelivr.net/npm/three-stdlib@2.36/index.js",
          "radashi": "https://cdn.jsdelivr.net/npm/radashi@12.9/dist/radashi.js",
          "three": "https://cdn.jsdelivr.net/npm/three@0.184/build/three.module.js",
          "three/addons/": "https://cdn.jsdelivr.net/npm/three@0.184/examples/jsm/",
          "three/": "https://cdn.jsdelivr.net/npm/three@0.184/",
          "stats.js": "https://cdn.jsdelivr.net/npm/stats.js@0.17/src/Stats.js",
          "stats-gl": "https://cdn.jsdelivr.net/npm/stats-gl@4.2/dist/main.js",
          "three-mesh-bvh": "https://cdn.jsdelivr.net/npm/three-mesh-bvh@0.9/build/index.module.js",
          "three-custom-shader-material/vanilla": "https://cdn.jsdelivr.net/npm/three-custom-shader-material@6.4/vanilla/three-custom-shader-material.es.js",
          "potpack": "https://cdn.jsdelivr.net/npm/potpack@2.1",
          "fflate": "https://cdn.jsdelivr.net/npm/fflate@0.8/esm/browser.js"
        }
      }
---

<TresCanvas clear-color="#82DBC5" window-size>

:RouterView

</TresCanvas>


<script setup>
import { TresCanvas } from "@tresjs/core";
</script>