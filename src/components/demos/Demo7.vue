<template>
  <Renderer ref="renderer"
            antialias
            orbit-ctrl>
    <Camera :position="{ z: 1000 }"
            :fov="75"
            :aspect="aspect"
            :near="1"
            :far="10000"
            ref="camera"/>
    <Scene ref="scene">
    </Scene>
  </Renderer>
</template>

<script>
import * as THREE from 'three';

export default {
  data () {
    return {
      camera: null,
      scene: null,
      renderer: null,
      aspect: window.innerWidth / window.innerHeight,
      portalParticles: [],
      smokeParticles: [],
      portalLight: null,
      clock: null,
    };
  },
  methods: {
    init () {
      const sceneLight = new THREE.DirectionalLight(0xffffff, 0.5);
      sceneLight.position.set(0, 0, 1);
      this.scene.add(sceneLight);

      this.portalLight = new THREE.PointLight(0x062d89, 30, 600, 1.7);
      this.portalLight.position.set(0, 0, 250);
      this.scene.add(this.portalLight);

      this.particleSetup();
    },
    particleSetup () {
      const loader = new THREE.TextureLoader();

      loader.load('./docs/textures/smoke.png', (texture) => {
        const portalGeo = new THREE.PlaneBufferGeometry(350, 350);
        const portalMaterial = new THREE.MeshStandardMaterial({
          map: texture,
          transparent: true,
        });

        const smokeGeo = new THREE.PlaneBufferGeometry(1000, 1000);
        const smokeMaterial = new THREE.MeshStandardMaterial({
          map: texture,
          transparent: true,
        });

        for (let p = 880; p > 250; p--) {
          const particle = new THREE.Mesh(portalGeo, portalMaterial);
          particle.position.set(
            0.5 * p * Math.cos((4 * p * Math.PI) / 180),
            0.5 * p * Math.sin((4 * p * Math.PI) / 180),
            0.1 * p
          );
          particle.rotation.z = Math.random() * 360;
          this.portalParticles.push(particle);
          this.scene.add(particle);
        }

        for (let p = 0; p < 40; p++) {
          const particle = new THREE.Mesh(smokeGeo, smokeMaterial);
          particle.position.set(
            Math.random() * 1000 - 500,
            Math.random() * 400 - 200,
            25
          );
          particle.rotation.z = Math.random() * 360;
          particle.material.opacity = 0.6;
          this.portalParticles.push(particle);
          this.scene.add(particle);
        }

        this.clock = new THREE.Clock();
        this.animate();
      });
    },
    animate () {
      const delta = this.clock.getDelta();

      this.portalParticles.forEach(p => {
        p.rotation.z -= delta * 1.5;
      });

      this.smokeParticles.forEach(p => {
        p.rotation.z -= delta * 0.2;
      });

      if (Math.random() > 0.9) {
        this.portalLight.power = 350 + Math.random() * 500;
      }

      requestAnimationFrame(this.animate);
    },
  },
  mounted () {
    this.camera = this.$refs.camera;
    this.scene = this.$refs.scene;
    this.renderer = this.$refs.renderer.three.renderer;
    this.renderer.setClearColor(0x000000, 1);
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    this.init();
  },
};
</script>

<style scoped>
  body {
    margin: 0;
    padding: 0;
    overflow: hidden;
  }
</style>
