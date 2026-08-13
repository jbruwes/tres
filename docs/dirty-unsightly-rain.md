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
---

<Sky :rayleigh="1" />

<TresInstancedMesh  v-for="(asset, key) in assets" :key="key" ref="items" v-if="isEveryFinished && isFinished" :args="[gltf[asset].nodes[asset].geometry, gltf[asset].nodes[asset].material, doubleSize ** 2]" />

:TresAxesHelper

:TresGridHelper

<script setup lang="ts">
import { inject, watch, useTemplateRef, reactive } from "vue";
import { useGLTF, Sky } from "@tresjs/cientos";
import { InstancedMesh, Object3D } from "three";
import { useFetch, useArrayEvery } from "@vueuse/core";
import { defineHex, hexToPoint, Grid, rectangle } from "honeycomb-grid";

const [xRadius, yRadius] = Array(2).fill(2 / Math.sqrt(3)),
  dimensions = { xRadius, yRadius },
  assets = ["hex_grass", "hex_water"],
  gltf = reactive(Object.fromEntries(assets.map((asset) => [asset, useGLTF(`uploads/tiles/base/${asset}.gltf`)]))),
  isEveryFinished = useArrayEvery(Object.values(gltf), ({ isLoading }) => !isLoading),
  refs = useTemplateRef<InstancedMesh[]>("items"),
  size = 50,
  halfSize = Math.floor(size / 2),
  doubleSize = size * 2 + 1,
  url = inject("url"),
  { isFinished, data } = useFetch(`${url}/terrain/${size}`).json(),
  grid = new Grid(defineHex({ dimensions }), rectangle({ width: doubleSize, height: doubleSize, start: { q: -halfSize, r: -size } }));

watch(refs, (meshes) => {

  const count = Array(assets.length).fill(0),
    dummy = new Object3D();

  grid.forEach((hex) => {
    const { y, type } = data.value[hex.q + halfSize + size][hex.r + size],
      index = assets.indexOf(type),
      { x, y: z } = hexToPoint(hex);

    dummy.position.set(x, y < 0 ? 0 : y, z);
    dummy.updateMatrix();
    meshes[index].setMatrixAt(count[index], dummy.matrix);
    count[index]++;
  });

  meshes.forEach((mesh, i) => {
    mesh.count = count[i];
    mesh.instanceMatrix.needsUpdate = true;
  });

}, { once: true })
</script>