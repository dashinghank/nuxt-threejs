<script setup lang="ts">
import * as THREE from "three";
import { onMounted } from "vue";
import gsap from "gsap";
import { WaitMilliseconds } from "flag-waiter";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import GUI from "lil-gui";
const clock = new THREE.Clock();

const cursor = {
  x: 0,
  y: 0,
};
onMounted(async () => {
  // Textures
  // const image = new Image();
  // const texture = new THREE.Texture(image);

  // image.onload = () => {
  //   texture.needsUpdate = true;
  // };
  // image.src = "../assets/imgs/color.jpg";
  const loadingManager = new THREE.LoadingManager();

  // loadingManager.onStart = () => {
  //   console.log("onStart");
  // };

  // loadingManager.onLoad = () => {
  //   console.log("onLoad");
  // };

  // loadingManager.onProgress = () => {
  //   console.log("onProgress");
  // };

  // loadingManager.onError = () => {
  //   console.log("onError");
  // };

  const textureLoader = new THREE.TextureLoader(loadingManager);
  const colorTexture = textureLoader.load("/imgs/color.jpg");
  // const alphaTexture = textureLoader.load("../assets/imgs/alpha.jpg");
  // const heightTexture = textureLoader.load("../assets/imgs/height.jpg");
  // const normalTexture = textureLoader.load("../assets/imgs/normal.jpg");
  // const ambientOcclusionTexture = textureLoader.load("../assets/imgs/ambientOcclusion.jpg");
  // const metalnessTexture = textureLoader.load("../assets/imgs/metalness.jpg");
  // const roughnessTexture = textureLoader.load("../assets/imgs/roughness.jpg");

  // colorTexture.repeat.x = 2;
  // colorTexture.repeat.y = 3;
  // colorTexture.wrapS = THREE.MirroredRepeatWrapping;
  // colorTexture.wrapT = THREE.MirroredRepeatWrapping;

  // colorTexture.offset.x = 0.5;
  // colorTexture.offset.y = 0.5;

  // colorTexture.rotation = Math.PI * 0.25;
  // colorTexture.center.x = 0.5;
  // colorTexture.center.y = 0.5;

  // colorTexture.minFilter = THREE.NearestFilter;
  colorTexture.magFilter = THREE.NearestFilter;
  const gui = new GUI();
  window.addEventListener("mousemove", (event) => {
    cursor.x = event.clientX / sizes.width - 0.5;
    cursor.y = -(event.clientY / sizes.height - 0.5);
    // console.log(cursor.x, cursor.y);
  });

  // Canvas
  const canvas = document.querySelector("canvas.webgl");

  // Scene
  const scene = new THREE.Scene();

  // Object
  const geometry = new THREE.BoxGeometry(1, 1, 1);
  console.log(geometry.attributes.uv);
  // const geometry = new THREE.SphereBufferGeometry(1, 32, 32);
  const material = new THREE.MeshBasicMaterial({ map: colorTexture });
  const mesh = new THREE.Mesh(geometry, material);
  scene.add(mesh);

  // debug
  gui.add(mesh.position, "y", -3, 3, 0.01).name("測試名字");
  gui.add(mesh, "visible");
  gui.addColor(material, "color");
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
  const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 1, 1000);
  // const aspectRatio = sizes.width / sizes.height;
  // const camera = new THREE.OrthographicCamera(-1 * aspectRatio, 1 * aspectRatio, 1, -1, 0.1, 100);

  // camera.position.x = 2;
  // camera.position.y = 2;
  camera.position.z = 3;
  camera.lookAt(mesh.position);
  scene.add(camera);

  //OrbitControls
  const controls = new OrbitControls(camera, canvas);
  controls.enableDamping = true;

  // Renderer
  const renderer = new THREE.WebGLRenderer({
    canvas: canvas,
  });

  const tick = (d) => {
    controls.update();
    // console.log(clock.getDelta());
    // console.log(clock.getElapsedTime());
    // mesh.rotation.y = clock.getElapsedTime();

    // Updata camera
    // camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 2;
    // camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 2;
    // camera.position.y = cursor.y * 3;
    // camera.lookAt(mesh.position);
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
    <div class="home">home</div>
    <canvas style="outline: none; position: fixed; top: 0; left: 0" class="webgl"></canvas>
  </div>
</template>
