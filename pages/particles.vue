<script setup lang="ts">
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import * as dat from "lil-gui";
import { onMounted } from "vue";

onMounted(() => {
  /**
   * Base
   */
  // Debug
  const gui = new dat.GUI();

  // Canvas
  const canvas = document.querySelector("canvas.webgl");

  // Scene
  const scene = new THREE.Scene();

  /**
   * Textures
   */
  const textureLoader = new THREE.TextureLoader();

  const particleTexture = textureLoader.load("../assets/textures/1.png");
  // particles

  // Geometry
  const particlesGeometry = new THREE.BufferGeometry();
  const count = 5000;

  const positions = new Float32Array(count * 3);
  for (let i = 0; i < count * 3; i++) {
    positions[i] = (Math.random() - 0.5) * 10;
  }

  particlesGeometry.setAttribute("position", new THREE.BufferAttribute(positions, 3));

  // Material
  const particlesMaterial = new THREE.PointsMaterial({
    size: 0.1,
    sizeAttenuation: true,
  });
  particlesMaterial.color = new THREE.Color("#ff88cc");
  particlesMaterial.transparent = true;
  particlesMaterial.alphaMap = particleTexture;
  particlesMaterial.alphaTest = 0.001;

  // points
  const particles = new THREE.Points(particlesGeometry, particlesMaterial);
  scene.add(particles);

  /**
   * Sizes
   */
  const sizes = {
    width: window.innerWidth,
    height: window.innerHeight,
  };

  window.addEventListener("resize", () => {
    // Update sizes
    sizes.width = window.innerWidth;
    sizes.height = window.innerHeight;

    // Update camera
    camera.aspect = sizes.width / sizes.height;
    camera.updateProjectionMatrix();

    // Update renderer
    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  });

  /**
   * Camera
   */
  // Base camera
  const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 0.1, 100);
  camera.position.z = 3;
  scene.add(camera);

  // Controls
  const controls = new OrbitControls(camera, canvas);
  controls.enableDamping = true;

  /**
   * Renderer
   */
  const renderer = new THREE.WebGLRenderer({
    canvas: canvas,
  });
  renderer.setSize(sizes.width, sizes.height);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  /**
   * Animate
   */
  const clock = new THREE.Clock();

  const tick = () => {
    const elapsedTime = clock.getElapsedTime();

    // Update controls
    controls.update();

    // Render
    renderer.render(scene, camera);

    // Call tick again on the next frame
  };

  renderer.setAnimationLoop(tick);
  renderer.setSize(sizes.width, sizes.height);
  // console.log("window.devicePixelRatio:", window.devicePixelRatio);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.render(scene, camera);
});
</script>

<template>
  <div>
    <div class="home">shadow</div>
    <canvas style="outline: none; position: fixed; top: 0; left: 0" class="webgl"></canvas>
  </div>
</template>
