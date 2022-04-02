<script setup>
import { render } from "@vue/runtime-dom";
import * as THREE from "three";
import * as dat from "dat.gui";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"

const gui = new dat.GUI();
const world = {
  plane: {
    width: 10,
    height: 10,
    widthSegments: 10,
    heightSegments: 10,
  },
};
gui.add(world.plane, "height", 1, 20).onChange(generatePlane);

gui.add(world.plane, "width", 1, 20).onChange(generatePlane);
gui.add(world.plane, "widthSegments", 1, 50).onChange(generatePlane);
gui.add(world.plane, "heightSegments", 1, 50).onChange(generatePlane);

function generatePlane() {
  planeMesh.geometry.dispose();
  planeMesh.geometry = new THREE.PlaneGeometry(
    world.plane.width,
    world.plane.height,
    world.plane.widthSegments,
    world.plane.heightSegments
  );
  const { array } = planeMesh.geometry.attributes.position;
  for (let index = 0; index < array.length; index += 3) {
    const element = array[index];
    const x = array[index];
    const y = array[index + 1];
    const z = array[index + 2];

    array[index + 2] = z + Math.random();
  }
}

const raycaster = new THREE.Raycaster();
console.log(raycaster)
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  75,
  innerWidth / innerHeight,
  0.1,
  1000
);
const renderer = new THREE.WebGLRenderer();

renderer.setSize(innerWidth, innerHeight);
renderer.setPixelRatio(devicePixelRatio);
document.body.appendChild(renderer.domElement);


new OrbitControls(camera,renderer.domElement)

camera.position.z = 5;

//Plane
const planeGeometry = new THREE.PlaneGeometry(5, 5, 10, 10);
const planeMaterial = new THREE.MeshPhongMaterial({
  color: 0xff0000,
  side: THREE.DoubleSide,
  flatShading: THREE.FlatShading,
});

const planeMesh = new THREE.Mesh(planeGeometry, planeMaterial);
const { array } = planeMesh.geometry.attributes.position;
for (let index = 0; index < array.length; index += 3) {
  const element = array[index];
  const x = array[index];
  const y = array[index + 1];
  const z = array[index + 2];

  array[index + 2] = z + Math.random();
}
scene.add(planeMesh);

//Light
const light = new THREE.DirectionalLight(0xffffff, 1);
light.position.set(0, 5, 100);
scene.add(light);

//Mouse 
const mouse = {
    x: undefined,
    y: undefined
}


function animate() {
  requestAnimationFrame(animate);
  renderer.render(scene, camera);
  planeMesh.updateMatrixWorld()
  raycaster.setFromCamera(mouse,camera)
  const intersects = raycaster.intersectObject(planeMesh)

  if (intersects.length > 0) {
      console.log(intersects);
  }

}
animate();

//Camera Zoom

// window.addEventListener("wheel", function (event) {
//   if (event.deltaY < 0) {
//     console.log("scrolling up");
//     camera.position.z -= 0.5;
//   } else if (event.deltaY > 0) {
//     camera.position.z += 0.5;
//     console.log("scrolling down");
//   }
// });



addEventListener('mousemove', (event) => {
    mouse.x = (event.clientX / innerWidth) * 2 - 1;
    mouse.y = (event.clientY / innerWidth) * 2 + 1;

})


</script>
<template></template>
