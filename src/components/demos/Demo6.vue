<template>
  <div>
    <h1>ThreeJS Rocks!</h1>
    <Renderer ref="renderer" antialias
              orbit-ctrl
              :mouseMove="true"
              background="#E5E5E5">
      <Camera :position="{ z: 5 }"
              :fov="75"
              :aspect="aspect"
              :near="0.1"
              ref="camera"/>
      <Scene background="#E5E5E5" ref="scene">
      </Scene>
    </Renderer>
  </div>
</template>

<script>
import * as THREE from 'three';
import { Expo, TimelineMax } from 'gsap';

export default {
  data () {
    return {
      renderer: null,
      aspect: window.innerWidth / window.innerHeight,
    };
  },
  methods: {
    init () {
      const geometry = new THREE.BoxGeometry(1, 1, 1);
      const material = new THREE.MeshLambertMaterial({ color: 0xF7F7F7 });

      let meshX = -10;
      for (let i = 0; i < 15; i++) {
        const mesh = new THREE.Mesh(geometry, material);
        mesh.position.x = (Math.random() - 0.5) * 10;
        mesh.position.y = (Math.random() - 0.5) * 10;
        mesh.position.z = (Math.random() - 0.5) * 10;
        this.$refs.scene.add(mesh);
        meshX += 1;
      }

      const light = new THREE.PointLight(0xFFFFFF, 1, 1000);
      light.position.set(0, 0, 0);
      this.$refs.scene.add(light);

      const secondLight = new THREE.PointLight(0xFFFFFF, 2, 1000);
      secondLight.position.set(0, 0, 25);
      this.$refs.scene.add(secondLight);

      this.renderer.setClearColor("#e5e5e5");
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    },
    addInteraction () {
      window.addEventListener('mousemove', this.onMouseMove, false);
      window.requestAnimationFrame(this.$refs.renderer.three.render);
    },
    onMouseMove(event) {
      event.preventDefault();
      const raycaster = new THREE.Raycaster();
      const mouse = new THREE.Vector2();

      mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

      raycaster.setFromCamera(mouse, this.$refs.camera.camera);

      const intersects = raycaster.intersectObjects(this.$refs.scene.scene.children, true);
      for (let i = 0; i < intersects.length; i++) {
        this.tl = new TimelineMax();
        this.tl.to(intersects[i].object.scale, 1, { x: 2, ease: Expo.easeOut });
        this.tl.to(intersects[i].object.scale, 0.5, { x: 0.5, ease: Expo.easeOut });
        this.tl.to(intersects[i].object.position, 0.5, { x: 2, ease: Expo.easeOut });
        this.tl.to(intersects[i].object.rotation, 0.5, { y: Math.PI * 0.5, ease: Expo.easeOut }, "=-1.5");
      }
    },
  },
  mounted () {
    this.renderer = this.$refs.renderer.three.renderer;
    this.init();
    this.addInteraction();
  },
};
</script>

<style scoped>
body {
  margin: 0;
  height: 100vh;
}

canvas {
  display: block;
}

h1 {
  position: absolute;
  top: 2em;
  left: 2em;
  font-family: 'Montserrat';
  font-size: 7em;
  text-transform: uppercase;
  width: auto;
  line-height: .8em;
  border: 5px solid black;
  padding: .2em;
}
</style>
