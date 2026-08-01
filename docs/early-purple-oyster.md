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

:TresPerspectiveCamera{:position="[7,7,7]" :look-at="[0,0,0]"}

::TresMesh{ref="donutRef" :position="[0,2,0]"}

:TresTorusGeometry{:args="[1,0.4,16,32]"}

:TresMeshBasicMaterial{color="#ff6b35"}

::

::TresInstancedMesh{ref="instanceMeshRef" :args="[null, null, count]">}

:TresCylinderGeometry{:args="[1, 1, 1, 6]"}

:TresMeshBasicMaterial

<!--TresMeshLambertMaterial></TresMeshLambertMaterial-->

::

:TresAxesHelper

:TresGridHelper

<script lang="js" setup>
import { useLoop } from "@tresjs/core";
import { ref } from "vue";

import { watch, useTemplateRef } from "vue";

import { ImprovedNoise } from 'three/addons/math/ImprovedNoise.js';
import { Color, Matrix4, Vector3 } from "three";

defineOptions({ inheritAttrs: false });

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

let count = 0;

const generateHeight = ([{ x1, x2 }, { z1, z2 }]) => {
  const data = {}, perlin = new ImprovedNoise(), z = Math.random() * 100;
  for (let x = x1; x < x2 + 1; x++) {
    data[x] = {};
    for (let y = z1; y < z2 + 1; y++) {
      data[x][y] = perlin.noise(x / 10, y / 10, z) * 0.5;
      //if (data[x][y] > 0)
      count += 1;
    }
  }
  return data;
};

const x1 = -100;
const x2 = 100;
const z1 = -100;
const z2 = 100;

const data = generateHeight([{ x1, x2 }, { z1, z2 }]);
const instanceMesh = useTemplateRef("instanceMeshRef");

watch(instanceMesh, (mesh) => {
  let i = 0;
  for (let x = x1; x < x2 + 1; x++) {
    for (let z = z1; z < z2 + 1; z++) {
      //if (data[x][z] > 0) {
      mesh.setMatrixAt(
        i,
        new Matrix4().makeTranslation(new Vector3(...pointy_hex_to_pixel([x, (data[x][z] - 0.07) * 5, z])))
      );
      mesh.setColorAt(i, new Color(0, data[x][z] + 0.5, 0));
      i += 1;
      //  }
    }
  }

});

const donutRef = ref(),
  { onBeforeRender } = useLoop();

onBeforeRender(({ elapsed }) => {
  if (donutRef.value) {
    donutRef.value.rotation.x = elapsed * 0.5;
    donutRef.value.rotation.y = elapsed * 0.3;
  }
});

</script>