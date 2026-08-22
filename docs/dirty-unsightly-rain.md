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

<TresInstancedMesh  v-for="[, , name] in assets" :key="name" ref="items" v-if="isEveryFinished && isFinished" :args="[gltf[name].nodes[name].geometry, gltf[name].nodes[name].material, doubleSize ** 2]" />

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
  assets = [
    ["tiles", "base", "hex_grass"],
    ["tiles", "base", "hex_water"],
    ["tiles", "base", "hex_grass_bottom"],
    ["tiles", "base", "hex_grass_sloped_low"],
    ["tiles", "base", "hex_grass_sloped_high"],
    ["tiles", "coast", "hex_coast_A"],
    ["tiles", "coast", "hex_coast_B"],
    ["tiles", "coast", "hex_coast_C"],
    ["tiles", "coast", "hex_coast_D"],
  ],
  gltf = reactive(Object.fromEntries(assets.map((asset) => [asset[2], useGLTF(`uploads/${asset.join("/")}.gltf`)]))),
  isEveryFinished = useArrayEvery(Object.values(gltf), ({ isLoading }) => !isLoading),
  refs = useTemplateRef<InstancedMesh[]>("items"),
  size = 50,
  halfSize = Math.floor(size / 2),
  doubleSize = size * 2 + 1,
  url = inject("url"),
  { isFinished, data } = useFetch(`${url}/terrain/${size}`).json(),
  grid = new Grid(defineHex({ dimensions }), rectangle({ width: doubleSize, height: doubleSize, start: { q: -halfSize, r: -size } })),
  direction2radians = (direction: number) => 2 * Math.PI * ((5 - direction + Math.floor(direction / 4) + 6) % 6) / 6;

watch(refs, (meshes) => {

  const count = Array(assets.length).fill(0),
    dummy = new Object3D();

  grid.forEach((hex) => {
    const { y, type, direction } = data.value[hex.q + halfSize + size][hex.r + size],
      index = assets.findIndex(([, , name]) => name === type),
      { x, y: z } = hexToPoint(hex);

    dummy.scale.set(1, 1, 1);
    dummy.position.set(x, y < 0 ? 0 : y / 2, z);
    dummy.rotation.set(0, direction2radians(direction), 0);
    dummy.updateMatrix();
    meshes[index].setMatrixAt(count[index], dummy.matrix);
    count[index]++;

    if (y > 0) {
      const bottomIndex = assets.findIndex(([, , name]) => name === "hex_grass_bottom");
      dummy.scale.set(1, y / 2, 1);
      dummy.position.set(x, y / 2 - 1, z);
      dummy.rotation.set(0, 0, 0);
      dummy.updateMatrix();
      meshes[bottomIndex].setMatrixAt(count[bottomIndex], dummy.matrix);
      count[bottomIndex]++;
    }

  });

  meshes.forEach((mesh, i) => {
    mesh.count = count[i];
    mesh.instanceMatrix.needsUpdate = true;
  });

}, { once: true })
</script>