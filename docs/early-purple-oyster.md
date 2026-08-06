---
title: intro
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

:primitive{v-for="(object, key) in copies" :key="key" :object="object"}

:TresAxesHelper

:TresGridHelper

<script lang="js" setup>
import { inject, watch, markRaw, reactive } from "vue";
import { useFetch } from "@vueuse/core";
import { Vector3 } from "three";
import { useGLTF } from "@tresjs/cientos";

const size = 10,
  url = inject("url"),
  { isFinished, data } = useFetch(`${url}/terrain/${size}`),
  { state } = useGLTF("uploads/tiles/base/hex_grass.gltf");

const copies = reactive([]);

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

watch([state, isFinished], ([value, finished]) => {
  if (finished && value) {
    const matrix = JSON.parse(data.value);
    for (let x = -size; x < size + 1; x++)
      for (let z = -size; z < size + 1; z++) {
        const object = value.scene.clone();
        object.position.copy(new Vector3(...pointy_hex_to_pixel([x, (matrix[x][z] - 0.07) * 5, z])));
        copies.push(markRaw(object));
      }
  }
});
</script>