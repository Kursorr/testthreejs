<template>
  <Renderer ref="renderer"
            antialias
            orbit-ctrl>
    <Camera :position="{ x: 0, y: 0, z: 1 }"
            :fov="40"
            :aspect="aspect"
            :near="0.1"
            :far="1000"
            ref="camera"/>
    <Scene ref="scene">
      <AmbientLight color="#ffffff" :intensity="1"/>
    </Scene>
  </Renderer>
</template>

<script>
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import { DecalGeometry } from 'three/examples/jsm/geometries/DecalGeometry';

export default {
  data () {
    return {
      objects: [],
      camera: null,
      scene: null,
      renderer: null,
      aspect: window.innerWidth / window.innerHeight,
      shirt: null,
    };
  },
  methods: {
    async init () {
      await this.loadShirt();
      this.incrustTextOnShirt();
    },
    async loadShirt () {
      const loader = new GLTFLoader();

      const gltf = await loader.loadAsync('docs/threex/shirt/scene.gltf');
      this.scene.add(gltf.scene);
      this.shirt = gltf.scene.children[1];
    },
    incrustTextOnShirt () {
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
