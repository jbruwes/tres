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

<TresInstancedMesh v-if="!isLoading && isFinished" ref="instancedMeshRef" :args="[nodes[targetMeshName].geometry, nodes[targetMeshName].material, (2 * size + 1) ** 2]" />

<script setup lang="ts">
import { inject, watch, useTemplateRef } from "vue";
import { useGLTF } from "@tresjs/cientos";
import { InstancedMesh, Object3D, Vector3 } from "three";
import { useFetch } from "@vueuse/core";

// https://observablehq.com/@jrus/hexround
const axial_round = (x, y) => {
  const xgrid = Math.round(x), ygrid = Math.round(y);
  x -= xgrid, y -= ygrid;
  const dx = Math.round(x + 0.5 * y) * Number(x * x >= y * y);
  const dy = Math.round(y + 0.5 * x) * Number(x * x < y * y);
  return [xgrid + dx, ygrid + dy];
};

// https://www.redblobgames.com/grids/hexagons/#hex-to-pixel-axial
//const pointy_hex_to_pixel = ([q, y, r]) => [Math.sqrt(3) * (q + 1 / 2 * r), y, 3 / 2 * r];
const pointy_hex_to_pixel = ([q, y, r]) => [Math.sqrt(3) * (q + 1 / 2 * (Math.abs(r) % 2)), y, 3 / 2 * r];
// https://www.redblobgames.com/grids/hexagons/#pixel-to-hex-axial
//const pixel_to_pointy_hex = ([x, z]) => axial_round((Math.sqrt(3) * x - z) / 3, 2 / 3 * z);
const pixel_to_pointy_hex = ([x, z]) => axial_round((Math.sqrt(3) * x - Math.abs(z) % 2) / 3, 2 / 3 * z);


const size = 10,
  url = inject("url"),
  { isFinished, data } = useFetch(`${url}/terrain/${size}`),
  { nodes, isLoading } = useGLTF("uploads/tiles/base/hex_grass.gltf"),
  instancedMeshRef = useTemplateRef<InstancedMesh>("instancedMeshRef"),
  targetMeshName = "hex_grass";

watch(instancedMeshRef, (mesh) => {
  const dummy = new Object3D(),
    matrix = JSON.parse(data.value as string);
  let i = 0;
  for (let x = -size; x < size + 1; x++)
    for (let z = -size; z < size + 1; z++) {
      dummy.position.copy(new Vector3(...pointy_hex_to_pixel([x, (matrix[x][z] - 0.07) * 5, z])));
      //   dummy.rotation.set(Math.random() * Math.PI, Math.random() * Math.PI, 0)
      dummy.updateMatrix();
      mesh.setMatrixAt(i, dummy.matrix);
      i++;
    }
  mesh.instanceMatrix.needsUpdate = true
}, { once: true })
</script>