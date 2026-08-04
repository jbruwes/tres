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

::TresInstancedMesh{ref="instanceMeshRef" :args="[null, null, (2 * size + 1) ** 2]">}

:TresCylinderGeometry{:args="[1, 1, 1, 6]"}

:TresMeshBasicMaterial

<!--TresMeshLambertMaterial></TresMeshLambertMaterial-->

::

:TresAxesHelper

:TresGridHelper

<script lang="js" setup>
import { useLoop } from "@tresjs/core";
import { useTemplateRef, inject } from "vue";
import { useFetch, whenever } from "@vueuse/core";
import { Color, Matrix4, Vector3 } from "three";

const size = 10,
  url = inject("url"),
  { isFinished, data } = useFetch(`${url}/terrain/${size}`),
  instanceMesh = useTemplateRef("instanceMeshRef"),
  donutRef = useTemplateRef("donutRef"),
  { onBeforeRender } = useLoop();

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

onBeforeRender(({ elapsed }) => {
  if (donutRef.value) {
    donutRef.value.rotation.x = elapsed * 0.5;
    donutRef.value.rotation.y = elapsed * 0.3;
  }
});

whenever(isFinished, () => {
  const matrix = JSON.parse(data.value);

  let i = 0;
  for (let x = -size; x < size + 1; x++)
    for (let z = -size; z < size + 1; z++) {
      instanceMesh.value.setMatrixAt(
        i,
        new Matrix4().makeTranslation(new Vector3(...pointy_hex_to_pixel([x, (matrix[x][z] - 0.07) * 5, z])))
      );
      instanceMesh.value.setColorAt(i, new Color(0, matrix[x][z] + 0.5, 0));
      i += 1;
    }

  instanceMesh.value.instanceMatrix.needsUpdate = true;
  instanceMesh.value.instanceColor.needsUpdate = true;
});
</script>