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
      // await this.loadShirt();
      this.createCanvas();
    },
    async loadShirt () {
      const loader = new GLTFLoader();

      const gltf = await loader.loadAsync('docs/threex/shirt/scene.gltf');
      this.scene.add(gltf.scene);
      this.shirt = gltf.scene.children[1];
    },
    createCanvas () {
      const canvasTexture = new THREE.CanvasTexture(cnvs);
      canvasTexture.wrapS = THREE.RepeatWrapping;
      canvasTexture.wrapT = THREE.RepeatWrapping;
      canvasTexture.repeat.set(2, 2);

      const geometry = new THREE.PlaneGeometry(10, 10, 20, 20);
      geometry.vertices.forEach(v => {
        v.z = Math.cos(v.x) * Math.sin(-v.y * 0.5) * 0.5;
      });
      geometry.computeFaceNormals();
      geometry.computeVertexNormals();

      const mesh = new THREE.Mesh(geometry, new THREE.MeshStandardMaterial({
        map: canvasTexture,
        metalness: 0.25,
        roughness: 0.25,
      }));

      this.scene.add(mesh);
      // the problem is here because he wants an ID
      // Cannot read property 'clearRect' of null
      const testCanvas = new fabric.Canvas('cnvs', {
        backgroundColor: 'white',
      });

      testCanvas.on("after:render", function() {
        mesh.material.map.needsUpdate = true;
      });

      const rect = new fabric.Rect({
        width: 50,
        height: 50,
        left: 0,
        top: 128,
        stroke: '#aaf',
        strokeWidth: 5,
        fill: '#faa',
        selectable: false,
        originX: 'center',
        originY: 'center',
      });

      testCanvas.add(rect);

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
