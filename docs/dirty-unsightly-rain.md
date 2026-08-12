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

<TresInstancedMesh  v-for="(asset, key) in assets" :key="key" ref="items" v-if="isEveryFinished && isFinished" :args="[gltf[asset].nodes[asset].geometry, gltf[asset].nodes[asset].material, (2 * size + 1) ** 2]" />

:TresAxesHelper

:TresGridHelper

<script setup lang="ts">
import { inject, watch, useTemplateRef, reactive } from "vue";
import { useGLTF } from "@tresjs/cientos";
import { InstancedMesh, Object3D, Vector3 } from "three";
import { useFetch, useArrayEvery } from "@vueuse/core";

// https://observablehq.com/@jrus/hexround
const axial_round = (x, y) => {
  const xgrid = Math.round(x), ygrid = Math.round(y);
  x -= xgrid, y -= ygrid;
  const dx = Math.round(x + 0.5 * y) * Number(x * x >= y * y);
  const dy = Math.round(y + 0.5 * x) * Number(x * x < y * y);
  return [xgrid + dx, ygrid + dy];
};


const assets = ["hex_grass", "hex_water"],
  gltf = reactive(Object.fromEntries(assets.map((asset) => [asset, useGLTF(`uploads/tiles/base/${asset}.gltf`)]))),
  isEveryFinished = useArrayEvery(Object.values(gltf), ({ isLoading }) => !isLoading),
  refs = useTemplateRef<InstancedMesh[]>("items");

const size = 10,
  url = inject("url"),
  { isFinished, data } = useFetch(`${url}/terrain/${size}`).json();

watch(refs, (meshes) => {

  const scale = Math.sqrt(3) / 2
  const count = Array(assets.length).fill(0);
  const dummy = new Object3D();
  dummy.scale.set(scale, 1, scale);

  // https://www.redblobgames.com/grids/hexagons/#hex-to-pixel-axial
  //const pointy_hex_to_pixel = ([q, y, r]) => [Math.sqrt(3) * (q + 1 / 2 * r), y, 3 / 2 * r];
  const pointy_hex_to_pixel = ([q, y, r]) => [Math.sqrt(3) * (q + 1 / 2 * (Math.abs(r) % 2)), y, 3 / 2 * r];
  // https://www.redblobgames.com/grids/hexagons/#pixel-to-hex-axial
  //const pixel_to_pointy_hex = ([x, z]) => axial_round((Math.sqrt(3) * x - z) / 3, 2 / 3 * z);
  const pixel_to_pointy_hex = ([x, z]) => axial_round((Math.sqrt(3) * x - Math.abs(z) % 2) / 3, 2 / 3 * z);

  for (let q = -size; q < size + 1; q++)
    for (let r = -size; r < size + 1; r++) {
      const { y, type } = data.value[q][r];
      const index = assets.indexOf(type);
      dummy.position.copy(new Vector3(...pointy_hex_to_pixel([q, y < 0 ? 0 : y, r])));
      //   dummy.rotation.set(Math.random() * Math.PI, Math.random() * Math.PI, 0)
      dummy.updateMatrix();
      meshes[index].setMatrixAt(count[index], dummy.matrix);
      count[index]++;
    }

  meshes.forEach((mesh, i) => {
    mesh.count = count[i];
    mesh.instanceMatrix.needsUpdate = true;
  });

}, { once: true })
</script>