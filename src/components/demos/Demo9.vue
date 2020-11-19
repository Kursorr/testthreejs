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
      <AmbientLight color="#ffffff" :intensity="0.5"/>
      <canvas id="cnvs" height="256" width="256"></canvas>
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
      this.createCanvas();
    },
    async loadShirt () {
      const loader = new GLTFLoader();

      const gltf = await loader.loadAsync('docs/threex/shirt/scene.gltf');
      this.scene.add(gltf.scene);
      this.shirt = gltf.scene.children[1];
    },
    createCanvas () {
      const canvasTexture = new THREE.CanvasTexture(cnvs, THREE.UVMapping, THREE.RepeatWrapping, THREE.RepeatWrapping);
      canvasTexture.repeat.set(1, 1);

      console.log(this.shirt.children[0].geometry);

      const mesh = new THREE.Mesh(this.shirt.children[0].geometry, new THREE.MeshPhongMaterial({
        map: canvasTexture,
        transparent: false,
        depthTest: true,
        depthWrite: false,
        polygonOffset: true,
        polygonOffsetFactor: -14,
        wireframe: false,
      }));

      mesh.rotation.x = 1.5707964611537577;
      mesh.rotation.y = 0;
      mesh.rotation.z = -0;
      mesh.scale.x = 0.1;
      mesh.scale.y = 0.1;
      mesh.scale.z = 0.1;
      this.scene.add(mesh);

      const testCanvas = new fabric.Canvas('cnvs', {
        backgroundColor: 'white',
        transparent: false,
        depthTest: false,
        depthWrite: false,
        polygonOffset: true,
        polygonOffsetFactor: -14,
      });

      testCanvas.on("after:render", function() {
        mesh.material.map.needsUpdate = true;
      });

      const text = new fabric.IText('Three.js\n+\nFaBric.js', {
        fontSize: 40,
        textAlign: 'center',
        fontWeight: 'bold',
        left: 128,
        top: 128,
        angle: 30,
        originX: 'center',
        originY: 'center',
        shadow: 'blue -5px 6px 5px',
        styles: {
          0: {
            0: {
              fontSize: 60,
              fontFamily: 'Impact',
              fontWeight: 'normal',
              fill: 'orange',
            },
          },
          1: {
            0: {
              fill: "blue",
            },
          },
          2: {
            0: {
              textBackgroundColor: 'red',
            },
            2: {
              fill: 'fuchsia',
              stroke: 'orange',
              strokeWidth: 1,
            },
          },
        },
      });
      text.setSelectionStyles({
        fontStyle: 'italic',
        fill: '',
        stroke: 'red',
        strokeWidth: 2,
      }, 1, 5);
      testCanvas.add(text);
      testCanvas.setActiveObject(text);
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
