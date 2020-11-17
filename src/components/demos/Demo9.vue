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
    createCanvasTexture () {
      /* this one, doesn't appear ...
      it was supposed to do the height & width dynamically using the font size */

      const text = "Hello World !";
      const fontSize = 16;
      const font = 'arial';
      const thatCanvas = document.createElement('canvas');
      thatCanvas.style.marginTop = '200px';
      thatCanvas.style.marginLeft = '300px';
      const ctx = thatCanvas.getContext('2d');
      ctx.font = 'Bold ' + fontSize + 'px ' + font;
      ctx.fillStyle = 'orange';

      thatCanvas.width = ctx.measureText(text).width;
      thatCanvas.height = fontSize * 1.3;

      console.log(thatCanvas.width, thatCanvas.height);

      ctx.font = 'Bold ' + fontSize + 'px ' + font;
      ctx.fillText(text, 0, thatCanvas.height / 1.3);

      const newTexture = new THREE.CanvasTexture(thatCanvas);
      newTexture.needsUpdate = true;

      return newTexture;
    },
    createCanvasTextureW () {
      const text = "Hello World !";
      const thatCanvas = document.createElement('canvas');
      thatCanvas.width = 1000; // testing purpose
      thatCanvas.height = 1000;
      const ctx = thatCanvas.getContext('2d');
      ctx.font = "Bold 36px serif";
      ctx.fillStyle = 'orange';
      ctx.strokeStyle = 'red';
      ctx.strokeRect(10, 10, 100, 100);
      ctx.fillText(text, 400, 1100);
      // how am I supposed to align my text at the middle of the shirt ???

      const newTexture = new THREE.CanvasTexture(thatCanvas);
      newTexture.needsUpdate = true;

      return newTexture;
    },
    incrustTextOnShirt () {
      const material = new THREE.MeshBasicMaterial({
        map: this.createCanvasTexture(),
        transparent: false,
        depthTest: false,
        depthWrite: false,
        polygonOffset: true,
        polygonOffsetFactor: -14,
        wireframe: true,
      });

      const position = new THREE.Vector3(0, 1, 0);
      const orientation = new THREE.Euler();
      const size = new THREE.Vector3(2, 2, 2);

      const mesh = new THREE.Mesh(new DecalGeometry(this.shirt.children[0], position, orientation, size), material);
      mesh.rotation.x = 1.5707964611537577;
      mesh.rotation.y = 0;
      mesh.rotation.z = -0;
      mesh.scale.x = 1;
      mesh.scale.y = 1;
      mesh.scale.z = 1;
      this.scene.add(mesh);
    },
    animate (time) {
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
