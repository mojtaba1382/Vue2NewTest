<template>
    <div ref="threeContainer" style="width: 100%; height: 100%;"></div>
  </template>
  
  <script>
  import * as THREE from 'three';
  
  export default {
    name: 'MyCube',
    mounted() {
      this.initThree();
    },
    methods: {
      initThree() {
        // Set up scene, camera, and renderer
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer();
  
        // Set renderer size and attach to DOM
        renderer.setSize(window.innerWidth, window.innerHeight);
        this.$refs.threeContainer.appendChild(renderer.domElement);
  
        // Create a cube
        const geometry = new THREE.BoxGeometry();
        const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
        const cube = new THREE.Mesh(geometry, material);
  
        // Add the cube to the scene
        scene.add(cube);
  
        // Position the camera
        camera.position.z = 5;
  
        // Animation loop
        const animate = () => {
          requestAnimationFrame(animate);
  
          // Rotate the cube
          cube.rotation.x += 0.01;
          cube.rotation.y += 0.01;
  
          renderer.render(scene, camera);
        };
  
        animate();
      },
    },
  };
  </script>
  
  <style>
  /* Optional: Add styles for your component */
  </style>
  