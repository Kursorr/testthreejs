<template>
  <Renderer ref="renderer" antialias
            orbit-ctrl
            :mouseMove="true"
            background="#FFF">
    <Camera :position="{ x: 10, y: 10, z: 10 }" ref="camera"/>
    <Scene background="#FFF" ref="scene">
      <PointLight color="#ff6000" :intensity="3" :position="{ x: 1, y: 29}" />
      <PointLight color="#ff6000" :intensity="3" :position="{ x: -1, y: -29}" />
      <PointLight color="#ff6000" :intensity="3" :position="{ x: 0, y: 40}" />
      <!--<Plane ref="plane"
             :position="{ x: 0, y: 0, z: 0 }"
             :scale="{x: 10, y: 10, z: 10}"
             :rotation="{x: -Math.PI / 2, y: 0, z: 0}"
             :widthSegments="10"
             :heightSegments="10"
             :castShadow="true"
             :receiveShadow="true">
        <StandardMaterial :displacement-scale="0.1">
          <Texture src="https://troisjs.github.io/trois/textures/Wood_Tiles_002_basecolor.jpg" />
        </StandardMaterial>
      </Plane>-->
    </Scene>
  </Renderer>
</template>

<script>
import * as THREE from 'three';
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader.js';
import { DecalGeometry } from 'three/examples/jsm/geometries/DecalGeometry.js';

const group = new THREE.Group();
const secondGroup = new THREE.Group();

let object;
export default {
  data () {
    return {
      scene: null,
      renderer: null,
      spaceship: null,
      plane: null,
      texture: null,
    };
  },
  methods: {
    loadModel () {
      object.traverse(child => {
        child.position.x = 0;
        child.position.y = 1;

        this.spaceship = child;

        if (child.isMesh) {
          child.material.map = this.texture;
        }
      });

      this.$refs.scene.add(object);
      this.addDetailOnShip();
      // this.loadFont();
      this.loadPlane();
      this.moveGroup();
      this.moveSecondGroup();
      this.addInteraction();
    },
    moveGroup () {
      const geometry = new THREE.BoxBufferGeometry(1, 1, 1);
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });

      const cubeA = new THREE.Mesh(geometry, material);
      cubeA.position.set(10, 10, 0);

      const cubeB = new THREE.Mesh(geometry, material);
      cubeB.position.set(-10, -10, 0);

      group.add(cubeA);
      group.add(cubeB);

      group.position.y = 5;

      this.$refs.scene.add(group);
    },
    moveSecondGroup () {
      secondGroup.position.y = 1;

      this.$refs.scene.add(secondGroup);
    },
    loadPlane () {
      const geometry = new THREE.SphereGeometry(1, 32, 32);
      const material = new THREE.MeshBasicMaterial({
        map: new THREE.TextureLoader().load('https://troisjs.github.io/trois/textures/Wood_Tiles_002_basecolor.jpg'),
        side: THREE.DoubleSide,
      });
      const plane = new THREE.Mesh(geometry, material);
      plane.position.x = 0;
      plane.position.y = 0;
      plane.position.z = 0;
      plane.rotation.x = -Math.PI / 2;
      plane.scale.x = 10;
      plane.scale.y = 10;
      plane.scale.z = 10;
      // this.$refs.scene.add(plane);
      this.plane = plane;

      secondGroup.add(plane);

      this.incrustTextOnPlane();
    },
    createCanvasTexture () {
      const text = "Hello World !";
      const thatCanvas = document.createElement('canvas');
      thatCanvas.width = 500;
      thatCanvas.height = 500;
      const ctx = thatCanvas.getContext('2d');
      ctx.font = "Bold 45px serif";
      ctx.fillStyle = 'orange';
      ctx.fillText(text, 130, 500);

      const newTexture = new THREE.CanvasTexture(thatCanvas);
      newTexture.needsUpdate = true;

      return newTexture;
    },
    incrustTextOnPlane () {
      const decalMaterial = new THREE.MeshPhongMaterial({
        specular: 0x444444,
        map: this.createCanvasTexture(),
        normalScale: new THREE.Vector2(1, 1),
        shininess: 30,
        transparent: true,
        depthTest: true,
        depthWrite: false,
        polygonOffset: true,
        polygonOffsetFactor: -10,
        wireframe: false,
      });

      const position = new THREE.Vector3(0, 1, 0);
      const orientation = new THREE.Euler();
      const size = new THREE.Vector3(2, 2, 2);

      const mesh = new THREE.Mesh(new DecalGeometry(this.plane, position, orientation, size), decalMaterial);
      mesh.rotateX(-Math.PI / 2);
      mesh.scale.x = 10;
      mesh.scale.y = 10;
      mesh.scale.z = 10;
      secondGroup.add(mesh);
      // this.$refs.scene.add(mesh);
    },
    loadFont () {
      const plane = new THREE.Mesh(this.$refs.plane.geometry, this.$refs.plane.material);

      const matDark = new THREE.MeshStandardMaterial({
        map: this.createCanvasTexture(),
        color: 0xffffff,
      });

      // const geometry = new THREE.BoxGeometry(15, 15, 15);
      // const test = new THREE.Mesh(geometry, matDark);
      // test.position.set(0, 15, 0);
      // this.$refs.scene.add(test);

      const position = new THREE.Vector3(0, 0, 0);
      const orientation = new THREE.Euler();
      const size = new THREE.Vector3(10, 10, 10);

      const mesh = new THREE.Mesh(new DecalGeometry(plane, position, orientation, size), matDark);
      this.$refs.scene.add(mesh);

      /* Add a text inside scene
      const loader = new THREE.FontLoader();

      loader.load('./docs/fonts/helvetiker_regular.typeface.json', font => {
        geometry = new THREE.TextGeometry(text, {
          font,
          size: 2,
          height: 0.1,
          curveSegments: 12,
          bevelEnabled: false,
          bevelThickness: 0.1,
          bevelSize: 0.1,
          bevelSegments: 0.1,
        });

        const textMat = new THREE.MeshPhongMaterial({ color: 0xff00ff });
        const textMesh = new THREE.Mesh(geometry, textMat);
        textMesh.rotation.x = -Math.PI / 2;

        this.$refs.scene.add(textMesh);
      }); */
    },
    newTexture () {
      // texture
      const manager = new THREE.LoadingManager(this.loadModel);
      const loader = new OBJLoader(manager);

      const textureLoader = new THREE.TextureLoader(manager);
      this.texture = textureLoader.load('./docs/threex/spaceships/F03_512.jpg');

      loader.load('./docs/threex/spaceships/SpaceFighter03.obj', obj => {
        object = obj;
      });

      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    },
    addDetailOnShip () {
      const textureLoader = new THREE.TextureLoader();
      const decalDiffuse = textureLoader.load('./docs/decals/decal-diffuse.png');
      const decalNormal = textureLoader.load('./docs/decals/decal-normal.jpg');

      const decalMaterial = new THREE.MeshPhongMaterial({
        specular: 0x444444,
        map: decalDiffuse,
        normalMap: decalNormal,
        normalScale: new THREE.Vector2(1, 1),
        shininess: 30,
        transparent: true,
        depthTest: true,
        depthWrite: false,
        polygonOffset: true,
        polygonOffsetFactor: -4,
        wireframe: false,
      });

      const position = new THREE.Vector3(0, 1, 0);
      const orientation = new THREE.Euler();
      const size = new THREE.Vector3(10, 10, 10);

      const material = decalMaterial.clone();
      material.color.setHex(Math.random() * 0xffffff);

      const m = new THREE.Mesh(new DecalGeometry(this.spaceship, position, orientation, size), material);

      m.position.set(0, 2, 0);
      m.scale.set(1, 1, 1);

      this.$refs.scene.add(m);
    },
    addInteraction () {
      window.addEventListener('pointerdown', this.onDocumentMouseDown, false);
      window.requestAnimationFrame(this.$refs.renderer.three.render);
    },
    onDocumentMouseDown (event) {
      const raycaster = new THREE.Raycaster();
      const mouse = new THREE.Vector2();

      mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

      raycaster.setFromCamera(mouse, this.$refs.camera.camera);

      // calculate objects intersecting the picking ray
      const intersects = raycaster.intersectObjects(this.$refs.scene.scene.children, true);

      for (let i = 0; i < intersects.length; i++) {
        intersects[i].object.material.color.set(0xff0000);
      }

      this.$refs.renderer.three.render(this.$refs.scene, this.$refs.camera);
    },
  },
  mounted () {
    this.renderer = this.$refs.renderer;
    this.newTexture();
  },
};
</script>
