<script setup lang="ts">
import * as T from "three";
import { onMounted } from "vue";
import gsap from "gsap";
import { WaitMilliseconds } from "flag-waiter";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import GUI from "lil-gui";

const cursor = {
  x: 0,
  y: 0,
};

// Textures

onMounted(async () => {
  const cubeTextureLoader = new T.CubeTextureLoader();
  const textureLoader = new T.TextureLoader();
  const colorTexture = textureLoader.load("../assets/imgs/color.jpg");
  const alphaTexture = textureLoader.load("../assets/imgs/alpha.jpg");
  const heightTexture = textureLoader.load("../assets/imgs/height.jpg");
  const normalTexture = textureLoader.load("../assets/imgs/normal.jpg");
  const ambientOcclusionTexture = textureLoader.load("../assets/imgs/ambientOcclusion.jpg");
  const metalnessTexture = textureLoader.load("../assets/imgs/metalness.jpg");
  const roughnessTexture = textureLoader.load("../assets/imgs/roughness.jpg");

  const matcapTexture = textureLoader.load("../assets/textures/1.png");
  const gradientTexture = textureLoader.load("../assets/gradients/3.jpg");
  gradientTexture.minFilter = T.NearestFilter;
  gradientTexture.magFilter = T.NearestFilter;
  gradientTexture.generateMipmaps = false;

  const environmentMapTexture = cubeTextureLoader.load(["../assets/environmentMaps/0/px.jpg", "../assets/environmentMaps/0/nx.jpg", "../assets/environmentMaps/0/py.jpg", "../assets/environmentMaps/0/ny.jpg", "../assets/environmentMaps/0/pz.jpg", "../assets/environmentMaps/0/nz.jpg"]);
  // Canvas
  const canvas = document.querySelector("canvas.webgl");

  // Scene
  const scene = new T.Scene();

  // Object
  // const material = new T.MeshBasicMaterial();
  // material.opacity = 0.5;
  // material.color = new T.Color(0x00ff00);
  // material.wireframe = true;
  // material.map = colorTexture;
  // material.transparent = true;
  // material.alphaMap = alphaTexture;
  // material.side = T.DoubleSide;

  //normal
  // const material = new T.MeshNormalMaterial();
  // // material.wireframe = true;
  // material.flatShading = true;

  //matcap
  // const material = new T.MeshMatcapMaterial();
  // material.matcap = matcapTexture;

  //depth
  // const material = new T.MeshDepthMaterial();

  // Lambert
  // const material = new T.MeshLambertMaterial();

  // PHONG
  // const material = new T.MeshPhongMaterial();
  // material.shininess = 100;
  // material.specular = new T.Color(0x1188ff);

  //Toon
  // const material = new T.MeshToonMaterial();
  // material.gradientMap = gradientTexture;

  // Standard
  // const material = new T.MeshStandardMaterial();
  // // material.metalness = 0.45;
  // // material.roughness = 0.65;
  // material.map = colorTexture;
  // material.aoMap = ambientOcclusionTexture;
  // material.aoMapIntensity = 1;
  // material.displacementMap = heightTexture;
  // material.displacementScale = 0.05;
  // material.metalnessMap = metalnessTexture;
  // material.roughnessMap = roughnessTexture;
  // material.normalMap = normalTexture;
  // material.normalScale.set(0.5, 0.5);
  // material.transparent = true;
  // material.alphaMap = alphaTexture;

  const material = new T.MeshStandardMaterial();
  material.metalness = 0.7;
  material.roughness = 0.2;
  material.envMap = environmentMapTexture;
  // debug
  const gui = new GUI();

  gui.add(material, "metalness", 0, 1, 0.0001).name("metalness");
  gui.add(material, "roughness", 0, 1, 0.0001).name("roughness");
  gui.add(material, "aoMapIntensity", 0, 10, 0.0001).name("aoMapIntensity");
  gui.add(material, "displacementScale", 0, 1, 0.0001).name("aoMapIntensity");

  const sphere = new T.Mesh(new T.SphereGeometry(0.5, 64, 64), material);
  sphere.geometry.setAttribute("uv2", new T.BufferAttribute(sphere.geometry.attributes.uv.array, 2));

  sphere.position.x = -1.5;
  const plane = new T.Mesh(new T.PlaneGeometry(1, 1, 100, 100), material);

  plane.geometry.setAttribute("uv2", new T.BufferAttribute(plane.geometry.attributes.uv.array, 2));
  const torus = new T.Mesh(new T.TorusGeometry(0.3, 0.2, 64, 128), material);
  torus.position.x = 1.5;
  torus.geometry.setAttribute("uv2", new T.BufferAttribute(torus.geometry.attributes.uv.array, 2));

  scene.add(sphere, plane, torus);

  //Lights
  const ambientLight = new T.AmbientLight(0xffffff, 0.5);
  scene.add(ambientLight);

  const pointLight = new T.PointLight(0xffffff, 0.5);
  pointLight.position.x = 2;
  pointLight.position.y = 3;
  pointLight.position.z = 4;
  scene.add(pointLight);

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
    sphere.rotation.y = 0.1 * elapsed;
    plane.rotation.y = 0.1 * elapsed;
    torus.rotation.y = 0.1 * elapsed;
    sphere.rotation.x = 0.15 * elapsed;
    plane.rotation.x = 0.15 * elapsed;
    torus.rotation.x = 0.15 * elapsed;

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
