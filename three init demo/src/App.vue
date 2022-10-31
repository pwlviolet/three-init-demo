<template>
  <div id="container"></div>
</template>

<script setup>
import * as THREE from "three";
import Stats from "three/examples/jsm/libs/stats.module.js";
import { FBXLoader } from "three/examples/jsm/loaders/FBXLoader.js";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { Octree } from "three/examples/jsm/math/Octree.js";
import { OctreeHelper } from "three/examples/jsm/helpers/OctreeHelper.js";
import { Capsule } from "three/examples/jsm/math/Capsule.js";
import { GUI } from "three/examples/jsm/libs/lil-gui.module.min.js";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { reactive, onMounted, ref, watch } from "vue";
import * as dat from "dat.gui";
import CameraControls from "camera-controls";
CameraControls.install({ THREE: THREE });
//全局
let scene,
  renderer,
  camera,
  fov,
  near,
  far,
  cameracontrols,
  clock,
  container,
  width,
  height,
  raycaster,
  cube,
  light;
scene = new THREE.Scene();
renderer = new THREE.WebGLRenderer({
  antialias: true,
});
raycaster = new THREE.Raycaster();
const gridHelper = new THREE.GridHelper(50, 50);
gridHelper.position.y = -1;
scene.add(gridHelper);

const mesh = new THREE.Mesh(
  new THREE.BoxGeometry(1, 1, 1),
  new THREE.MeshBasicMaterial({ color: 0xff0000, wireframe: true })
);
mesh.onBeforeRender = function () {
  this.rotation.x += 0.01;
};
scene.add(mesh);

onMounted(() => {
  container = document.getElementById("container");
  width = container.clientWidth;
  height = container.clientHeight;
  fov = 60;
  near = 0.01;
  far = 10000;
  camera = new THREE.PerspectiveCamera(fov, width / height, near, far);
  camera.position.set(0, 0, 5);
  renderer.setSize(width, height);
  renderer.setPixelRatio(2);
  renderer.shadowMap.enabled=true
  renderer.render(scene, camera);
  container.appendChild(renderer.domElement);

  clock = new THREE.Clock();

  cameracontrols = new CameraControls(camera, renderer.domElement);

  let animate = function () {
    cameracontrols.update(clock.getDelta());
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
  };

  animate();
  renderer.domElement.addEventListener("click", mouseClick, false);
});
function mouseClick(e) {
      e.preventDefault();
      let mouse = new THREE.Vector2();
      // 通过鼠标点击的位置计算出raycaster所需要的点的位置，以屏幕中心为原点，值的范围为-1到1.
      mouse.x = ((e.clientX - renderer.domElement.getBoundingClientRect().left) / width) * 2 - 1;
      mouse.y = -((e.clientY - renderer.domElement.getBoundingClientRect().top )/ height) * 2 + 1;
      raycaster.setFromCamera(mouse, camera);
      const intersects = raycaster.intersectObjects(scene.children);
      console.log(intersects);
    }
// window.addEventListener("resize", () => {
//       let sizes = new Object();
//       sizes.width = width;
//       sizes.height = height;
//       // 更新渲染
//       renderer.setSize(width, height);
//       renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
//       // 更新相机
//       camera.aspect = width / height;
//       camera.updateProjectionMatrix();
//     });
// function render(){
//   if(resizeRendererToDisplaySize(renderer))
//   {
//     const canvas = renderer.domElement;
//     camera.aspect = canvas.clientWidth / canvas.clientHeight;
//     camera.updateProjectionMatrix();
//   }
//   renderer.render(scene, camera);
// }

// function resizeRendererToDisplaySize(renderer) {
//   const canvas = renderer.domElement;
//   const pixelRatio = window.devicePixelRatio;
//   camera.aspect = canvas.clientWidth / canvas.clientHeight;
//   const width = (canvas.clientWidth * pixelRatio) | 0;
//   const height = (canvas.clientHeight * pixelRatio) | 0;
//   const needResize = canvas.width !== width || canvas.height !== height;
//   if (needResize) {
//     renderer.setSize(width, height, false);
//   }
//   return needResize;
// }
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
#container {
  width: 100vw;
  height: 100vh;
}
</style>
