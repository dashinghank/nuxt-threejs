<script setup lang="ts">
import { Vector3, Vector2 } from "three";
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

// Textures
const myCanvas = ref();
onMounted(async () => {
  // Scene
  const scene = new THREE.Scene(); //建立場景

  // Object
  const planeGeometry = new THREE.PlaneGeometry(1, 1, 5, 5);
  console.log(planeGeometry.attributes);

  for (let i = 0; i < 36; i++) {
    console.log(math(new Vector2(planeGeometry.attributes.position.getX(i), planeGeometry.attributes.position.getY(i)), new Vector2(0, 0)));
  }

  const geometry = new THREE.BoxGeometry(1, 1, 1);

  // const material = new THREE.MeshBasicMaterial({ color: 0xff0000 });
  const material = new THREE.MeshBasicMaterial();
  // material.wireframe = true;
  // Mesh
  const mesh = new THREE.Mesh(planeGeometry, material);

  scene.add(mesh);

  // Sizes
  const sizes = {
    width: window.innerWidth,
    height: window.innerHeight,
  };
  console.log(sizes);

  // Camera
  const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height);
  camera.position.z = 3;
  scene.add(camera);

  // Renderer
  const renderer = new THREE.WebGLRenderer({
    canvas: myCanvas.value,
    antialias: true,
  });
  renderer.setSize(sizes.width, sizes.height);

  // Control

  //以下為最簡短操作需求
  const orbitControl = new OrbitControls(camera, myCanvas.value);

  // Animate
  const tick = () => {
    orbitControl.update();

    // Render
    renderer.render(scene, camera);
  };

  window.addEventListener("resize", () => {
    //Controls
    orbitControl.update();
    // Update sizes
    sizes.width = window.innerWidth;
    sizes.height = window.innerHeight;
    // Update renderer
    renderer.setSize(sizes.width, sizes.height);
    // Update camera
    camera.aspect = sizes.width / sizes.height; //這個值是預防圖像扭曲
    camera.updateProjectionMatrix(); //然後執行這個來更新camera內部數值
  });
  renderer.setAnimationLoop(tick);
});

function math(v0: Vector2, v1: Vector2) {
  var result = 0;

  result = Math.sqrt(Math.pow(v0.x - v1.x, 2) + Math.pow(v0.y - v1.y, 2));

  return result;
}
</script>

<template>
  <div>
    <div class="home">home</div>
    <canvas ref="myCanvas" style="outline: none; position: fixed; top: 0; left: 0" class="webgl"></canvas>
  </div>
</template>
