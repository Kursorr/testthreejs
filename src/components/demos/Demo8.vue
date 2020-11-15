<template>
  <Renderer ref="renderer"
            antialias
            orbit-ctrl>
    <Camera :position="{ x: 0, y: 80, z: 0 }"
            :fov="40"
            :aspect="aspect"
            :near="0.1"
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
      objects: [],
      camera: null,
      scene: null,
      renderer: null,
      aspect: window.innerWidth / window.innerHeight,
    };
  },
  methods: {
    init () {
      const solarSystem = new THREE.Object3D();
      this.scene.add(solarSystem);
      this.objects.push(solarSystem);

      const earthOrbit = new THREE.Object3D();
      earthOrbit.position.x = 10;
      solarSystem.add(earthOrbit);
      this.objects.push(earthOrbit);

      const sphereGeometry = new THREE.SphereBufferGeometry(1, 6, 6);
      const sunMaterial = new THREE.MeshPhongMaterial({ emissive: 0xFFFF00 });
      const sunMesh = new THREE.Mesh(sphereGeometry, sunMaterial);
      sunMesh.scale.set(5, 5, 5);
      solarSystem.add(sunMesh);
      this.objects.push(sunMesh);

      const color = 0xFFFFFF;
      const intensity = 3;
      const light = new THREE.PointLight(color, intensity);
      this.scene.add(light);

      const earthMaterial = new THREE.MeshPhongMaterial({ emissive: 0x112244, color: 0x2233FF });
      const earthMesh = new THREE.Mesh(sphereGeometry, earthMaterial);
      this.objects.push(earthMesh);
      earthOrbit.add(earthMesh);

      const moonOrbit = new THREE.Object3D();
      moonOrbit.position.x = 2;
      earthOrbit.add(moonOrbit);

      const moonMaterial = new THREE.MeshPhongMaterial({ color: 0x888888, emissive: 0x222222 });
      const moonMesh = new THREE.Mesh(sphereGeometry, moonMaterial);
      moonMesh.scale.set(0.5, 0.5, 0.5);
      moonOrbit.add(moonMesh);
      this.objects.push(moonMesh);

      const marsMaterial = new THREE.MeshPhongMaterial({ color: 0xc1440e, emissive: 0xc1440e });
      const marsMesh = new THREE.Mesh(sphereGeometry, marsMaterial);
      marsMesh.position.x = 20;
      solarSystem.add(marsMesh);

      this.objects.push(marsMesh);

      this.animate();
    },
    animate (time) {
      time *= 0.001;

      this.objects.forEach(obj => {
        obj.rotation.y = time;
      });

      requestAnimationFrame(this.animate);
    },
  },
  mounted () {
    this.camera = this.$refs.camera;
    this.scene = this.$refs.scene;
    this.renderer = this.$refs.renderer.three.renderer;
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
