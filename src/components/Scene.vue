<template>
  <div class="container">
    <h1>the sphere</h1>
  </div>
  <canvas class="webgl" ref="webgl"></canvas>
  <section></section>
</template>

<script>
import * as THREE from "three";
import * as dat from "dat.gui";

export default {
  name: "Scene",

  data() {
    return {
      sizes: {
        width: window.innerWidth,
        height: window.innerHeight,
      },

      clock: new THREE.Clock(),

      mouse: {
        x: 0,
        y: 0,
      },
      target: {
        x: 0,
        y: 0,
      },
    };
  },

  mounted() {
    this.initialiseScene();
    this.tick();
    window.addEventListener("resize", this.onResize);
    window.addEventListener("mousemove", this.onMouseMove);
    window.addEventListener("scroll", this.onScroll);
  },

  methods: {
    initialiseScene() {
      this.gui = new dat.GUI({closed: true});
      // this.gui.closse();

      this.canvas = this.$refs.webgl;

      this.scene = new THREE.Scene();

      this.sphere = this.initializeSphere();
      this.scene.add(this.sphere);

      this.initializeLight();

      this.camera = this.initalizeCamera();
      this.scene.add(this.camera);

      this.initializeRenderer();
    },

    initializeSphere() {
      // Textures
      const textureLoader = new THREE.TextureLoader();

      const normalTexture = textureLoader.load("/textures/NormalMap.png");

      // Objects
      const geometry = new THREE.SphereBufferGeometry(0.5, 64, 64);

      // Materials
      const material = new THREE.MeshStandardMaterial();
      material.metalness = 0.7;
      material.roughness = 0.2;
      material.normalMap = normalTexture;

      material.color = new THREE.Color(0x292929);

      // Mesh
      return new THREE.Mesh(geometry, material);
    },

    initializeLight() {
      const pointLight = new THREE.PointLight(0xffffff, 0.1);
      pointLight.position.x = 2;
      pointLight.position.y = 3;
      pointLight.position.z = 4;
      this.scene.add(pointLight);

      const pointLight2 = new THREE.PointLight(0x8a307f, 2);
      pointLight2.position.set(-2.5, 2.15, -0.75);
      pointLight2.intensity = 6;
      this.scene.add(pointLight2);

      // Light3
      const pointLight3 = new THREE.PointLight(0x79a7d3, 2);
      pointLight3.position.set(2.5, -2.15, -0.75);
      pointLight3.intensity = 6;
      this.scene.add(pointLight3);

      // GUI
      const light2 = this.gui.addFolder("Light 2");
      light2.add(pointLight2.position, "x").min(-6).max(6).step(0.01);
      light2.add(pointLight2.position, "y").min(-3).max(3).step(0.01);
      light2.add(pointLight2.position, "z").min(-3).max(3).step(0.01);
      light2.add(pointLight2, "intensity").min(0).max(10).step(0.01);
      const light2Color = {
        color: 0x8a307f,
      };
      light2.addColor(light2Color, "color").onChange(() => {
        pointLight2.color.set(light2Color.color);
      });

      const pointLightHealper2 = new THREE.PointLightHelper(pointLight2, 1);
      this.scene.add(pointLightHealper2);

      const light3 = this.gui.addFolder("Light 3");
      light3.add(pointLight3.position, "x").min(-6).max(6).step(0.01);
      light3.add(pointLight3.position, "y").min(-3).max(3).step(0.01);
      light3.add(pointLight3.position, "z").min(-3).max(3).step(0.01);
      light3.add(pointLight3, "intensity").min(0).max(10).step(0.01);
      const light3Color = {
        color: 0x79a7d3,
      };
      light3.addColor(light3Color, "color").onChange(() => {
        pointLight3.color.set(light3Color.color);
      });

      const pointLightHealper3 = new THREE.PointLightHelper(pointLight3, 1);
      this.scene.add(pointLightHealper3);
    },

    initalizeCamera() {
      const camera = new THREE.PerspectiveCamera(
        75,
        this.sizes.width / this.sizes.height,
        0.1,
        100
      );
      camera.position.x = 0;
      camera.position.y = 0;
      camera.position.z = 2;
      return camera;
    },

    initializeRenderer() {
      this.renderer = new THREE.WebGLRenderer({
        canvas: this.canvas,
        alpha: true,
      });

      // -12 to cancel overflow
      this.renderer.setSize(this.sizes.width - 12, this.sizes.height - 12);
      this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    },

    tick() {
      // mouseControl
      this.target.x = this.mouse.x * 0.001;
      this.target.y = this.mouse.y * 0.001;

      const elapsedTime = this.clock.getElapsedTime();

      // Update objects
      this.sphere.rotation.y = 0.5 * elapsedTime;

      this.sphere.rotation.y += 0.5 * (this.target.x - this.sphere.rotation.y);
      this.sphere.rotation.x += 0.5 * (this.target.y - this.sphere.rotation.x);
      this.sphere.position.z = -0.5 * this.target.y;
      // Update Orbital Controls
      // controls.update()

      // Render
      this.renderer.render(this.scene, this.camera);

      // Call tick again on the next frame
      window.requestAnimationFrame(this.tick);
    },

    onResize() {
      // Update sizes
      this.sizes.width = window.innerWidth;
      this.sizes.height = window.innerHeight;

      // Update camera
      this.camera.aspect = this.sizes.width / this.sizes.height;
      this.camera.updateProjectionMatrix();

      // Update renderer
      this.renderer.setSize(this.sizes.width - 12, this.sizes.height - 12);
      this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    },

    onMouseMove(event) {
      this.mouse.x = event.clientX - this.sizes.width / 2;
      this.mouse.y = event.clientY - this.sizes.height / 2;
    },

    onScroll() {
      this.sphere.position.y = window.scrollY * 0.001;
    },
  },
};
</script>

<style scoped>
.webgl {
  position: absolute;
  top: 0;
  left: 0;
  outline: none;
}

.container {
  height: 100vh;
  display: grid;
  place-items: center;
}

h1 {
  position: relative;
  z-index: 1;
  font-size: clamp(20px, 4vw, 40px);
  text-transform: uppercase;
  color: white;
  font-family: sans-serif;
  margin: 10px;
  text-align: center;
}

section {
  height: 100vh;
}
</style>