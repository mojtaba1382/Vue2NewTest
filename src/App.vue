<template>
  <div ref="cubeContainer" class="cube-container">
    <canvas ref="webglCanvas"></canvas> <!-- WebGL Canvas for rendering -->
  </div>
</template>

<script>
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';

export default {
  name: 'MyModel',
  data() {
    return {
      scene: null,
      camera: null,
      renderer: null,
      model: null,
      rotationSpeed: 0.05, // Rotation speed (adjustable)
      zoomSpeed: 1, // Zoom speed (adjustable)
    };
  },
  mounted() {
    // Initialize Three.js scene, camera, and renderer
    this.initThreeJS();

    // Listen for keyboard and mouse wheel events
    window.addEventListener('keydown', this.onKeyDown);
    window.addEventListener('wheel', this.onMouseScroll);
  },
  beforeDestroy() {
    // Remove event listeners when the component is destroyed
    window.removeEventListener('keydown', this.onKeyDown);
    window.removeEventListener('wheel', this.onMouseScroll);
  },
  methods: {
    initThreeJS() {
      // Create the scene
      this.scene = new THREE.Scene();

      // Create the camera (field of view, aspect ratio, near and far planes)
      this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);

      // Create the WebGLRenderer
      this.renderer = new THREE.WebGLRenderer({ canvas: this.$refs.webglCanvas });
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer.setPixelRatio(window.devicePixelRatio);
      window.addEventListener('resize', () => {
        this.camera.aspect = window.innerWidth / window.innerHeight;
        this.camera.updateProjectionMatrix();
        this.renderer.setSize(window.innerWidth, window.innerHeight);
      });

      const loader = new GLTFLoader();
      loader.load('/MyModels/MyModel.glb', (gltf) => {
        this.model = gltf.scene;
        this.scene.add(this.model);
        gltf.scene.position.set(0, 0, 0); // Ensure the model is at the origin
        gltf.scene.scale.set(1, 1, 1);
      }, undefined, (error) => {
        console.error('Error loading GLTF model:', error);
      });
      const light = new THREE.DirectionalLight(0xffffff, 1);
      light.position.set(5, 5, 5); // Position the light
      this.scene.add(light);

      // Optional: Add ambient light for overall illumination
      const ambientLight = new THREE.AmbientLight(0x404040, 1); // Soft white light
      this.scene.add(ambientLight);
      // Set the camera position
      this.camera.position.set(0, 0, 10); // Move the camera back to see the scene
      this.camera.lookAt(this.scene.position);
      this.scene.background = new THREE.Color(0xaaaaaa); // Light gray background
      // Start the render loop
      this.animate();
    },

    animate() {
      requestAnimationFrame(this.animate);
      if (this.model) {
        this.model.rotation.y += this.rotationSpeed * 0.01;
      }
      // Render the scene
      this.renderer.render(this.scene, this.camera);
    },

    onKeyDown(event) {
      // Check which key was pressed and adjust the cube's rotation accordingly
      if (!this.model) return;
      switch (event.key) {
        case 'ArrowLeft': // Rotate model around Y-axis to the left
          this.model.rotation.y -= this.rotationSpeed;
          break;
        case 'ArrowRight': // Rotate model around Y-axis to the right
          this.model.rotation.y += this.rotationSpeed;
          break;
        case 'ArrowUp': // Rotate model around X-axis upwards
          this.model.rotation.x -= this.rotationSpeed;
          break;
        case 'ArrowDown': // Rotate model around X-axis downwards
          this.model.rotation.x += this.rotationSpeed;
          break;
        case 'a': // Rotate model around Z-axis (rotate left)
          this.model.rotation.z -= this.rotationSpeed;
          break;
        case 'd': // Rotate model around Z-axis (rotate right)
          this.model.rotation.z += this.rotationSpeed;
          break;
      }
    },

    onMouseScroll(event) {
      // Adjust camera's z position to zoom in or out
      const delta = event.deltaY * this.zoomSpeed * 0.01; // Adjust zoom speed
      this.camera.position.z += delta;

      // Clamp zoom levels to prevent zooming too far in or out
      //this.camera.position.z = Math.max(2, Math.min(10, this.camera.position.z));
    },
  },
};
</script>

<style scoped>

canvas {
  display: block;
  width: 100%;
  height: 100%;
}
</style>
