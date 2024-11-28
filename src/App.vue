<template>
  <div ref="cubeContainer" class="cube-container">
    <canvas ref="webglCanvas"></canvas> <!-- WebGL Canvas for rendering -->
  </div>
</template>

<script>
import * as THREE from 'three';

export default {
  name: 'MyCube',
  data() {
    return {
      scene: null,
      camera: null,
      renderer: null,
      cube: null,
      wireframeCube: null, // Wireframe version of the cube
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

      // Create a cube geometry
      const geometry = new THREE.BoxGeometry();

      // Create the cube material (with color)
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
      this.cube = new THREE.Mesh(geometry, material);

      // Create the wireframe material
      const wireframeMaterial = new THREE.MeshBasicMaterial({
        color: 0x000000, // Color of the wireframe (black border)
        wireframe: true, // Enable wireframe mode
      });
      this.wireframeCube = new THREE.Mesh(geometry, wireframeMaterial);

      // Add both the cube and the wireframe to the scene
      this.scene.add(this.cube);
      this.scene.add(this.wireframeCube);

      // Set the camera position
      this.camera.position.z = 5;

      // Start the render loop
      this.animate();
    },

    animate() {
      requestAnimationFrame(this.animate);

      // Render the scene
      this.renderer.render(this.scene, this.camera);
    },

    onKeyDown(event) {
      // Check which key was pressed and adjust the cube's rotation accordingly
      switch (event.key) {
        case 'ArrowLeft': // Rotate cube around Y-axis to the left
          this.cube.rotation.y -= this.rotationSpeed;
          this.wireframeCube.rotation.y -= this.rotationSpeed;
          break;
        case 'ArrowRight': // Rotate cube around Y-axis to the right
          this.cube.rotation.y += this.rotationSpeed;
          this.wireframeCube.rotation.y += this.rotationSpeed;
          break;
        case 'ArrowUp': // Rotate cube around X-axis upwards
          this.cube.rotation.x -= this.rotationSpeed;
          this.wireframeCube.rotation.x -= this.rotationSpeed;
          break;
        case 'ArrowDown': // Rotate cube around X-axis downwards
          this.cube.rotation.x += this.rotationSpeed;
          this.wireframeCube.rotation.x += this.rotationSpeed;
          break;
        case 'a': // Rotate cube around Z-axis (rotate left)
          this.cube.rotation.z -= this.rotationSpeed;
          this.wireframeCube.rotation.z -= this.rotationSpeed;
          break;
        case 'd': // Rotate cube around Z-axis (rotate right)
          this.cube.rotation.z += this.rotationSpeed;
          this.wireframeCube.rotation.z += this.rotationSpeed;
          break;
      }
    },

    onMouseScroll(event) {
      // Adjust camera's z position to zoom in or out
      const delta = event.deltaY * this.zoomSpeed * 0.01; // Adjust zoom speed
      this.camera.position.z += delta;

      // Clamp zoom levels to prevent zooming too far in or out
      this.camera.position.z = Math.max(2, Math.min(10, this.camera.position.z));
    },
  },
};
</script>

<style scoped>
.cube-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f0f0f0;
}

canvas {
  border: 1px solid black;
}
</style>
