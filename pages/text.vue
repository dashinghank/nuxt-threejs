<script setup lang="ts">
import * as T from "three";
import { onMounted } from "vue";
import gsap from "gsap";
import { WaitMilliseconds } from "flag-waiter";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { FontLoader } from "three/examples/jsm/loaders/FontLoader.js";
import { TextGeometry } from "three/examples/jsm/geometries/TextGeometry.js";
import GUI from "lil-gui";

const cursor = {
  x: 0,
  y: 0,
};

// Textures

onMounted(async () => {
  // Canvas
  const canvas = document.querySelector("canvas.webgl");

  // Scene
  const scene = new T.Scene();

  // Fonts
  const fontLoader = new FontLoader();

  fontLoader.load("../assets/fonts/helvetiker_regular.typeface.json", (font) => {
    console.log("font:", font);
    const textGeometry = new TextGeometry("Hello Three.js", {
      font: font,
      size: 0.5,
      height: 0.2,
      curveSegments: 12,
      bevelEnabled: true,
      bevelThickness: 0.03,
      bevelSize: 0.02,
      bevelOffset: 0,
      bevelSegments: 5,
    });
    const textMaterial = new T.MeshBasicMaterial();
    const text = new T.Mesh(textGeometry, textMaterial);
    textMaterial.wireframe = true;
    scene.add(text);
  });

  // object
  // debug
  const gui = new GUI();
  console.log(gui);
  // Sizes
  const sizes = {
    width: window.innerWidth,
    height: window.innerHeight,
  };

  window.addEventListener("resize", () => {
    // console.log("window has been resized");

    // update sizes
    sizes.width = window.innerWidth;
    sizes.height = window.innerHeight;
    // update camera
    camera.aspect = sizes.width / sizes.height;
    camera.updateProjectionMatrix();

    // update renderer
    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  });

  window.addEventListener("dblclick", () => {
    const fullscreenElement = document.fullscreenElement || document.webkitFullscreenElement;

    // console.log("double click");
  });
  // Camera
  const camera = new T.PerspectiveCamera(75, sizes.width / sizes.height, 1, 1000);
  // const aspectRatio = sizes.width / sizes.height;
  // const camera = new THREE.OrthographicCamera(-1 * aspectRatio, 1 * aspectRatio, 1, -1, 0.1, 100);

  // camera.position.x = 2;
  // camera.position.y = 2;
  camera.position.z = 1;
  scene.add(camera);

  //OrbitControls
  const controls = new OrbitControls(camera, canvas);
  controls.enableDamping = true;

  // Renderer
  const renderer = new T.WebGLRenderer({
    canvas: canvas,
  });

  const clock = new T.Clock();
  const tick = (d) => {
    const elapsed = clock.getElapsedTime();
    controls.update();

    renderer.render(scene, camera);
  };
  renderer.setAnimationLoop(tick);
  renderer.setSize(sizes.width, sizes.height);
  // console.log("window.devicePixelRatio:", window.devicePixelRatio);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.render(scene, camera);

  //animations
});
</script>

<template>
  <div>
    <div class="home">3d text</div>
    <canvas style="outline: none; position: fixed; top: 0; left: 0" class="webgl"></canvas>
  </div>
</template>
