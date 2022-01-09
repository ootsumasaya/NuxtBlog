<template>
  <v-app>
    <navi />
    <v-main>
      <h1>GLBファイルの表示テスト</h1>
      <div id="WebGL-output"></div>
    </v-main>
  </v-app>
</template>

<script>
import navi from "/components/navi";
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

export default {
  components: {
    navi,
  },
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      model: null,
      controls: null,
      mixer: null,
    };
  },
  methods: {
    init() {
      // コンテナの指定
      let container = document.getElementById("WebGL-output");

      //シーンの作成
      this.scene = new THREE.Scene();

      //カメラの作成
      this.camera = new THREE.PerspectiveCamera(
        30,
        container.clientWidth / container.clientHeight,
        0.1,
        1000
      );
      //カメラセット
      this.camera.position.set(0, 0, 1);
      this.camera.lookAt(new THREE.Vector3(0, 0, 0));

      //レンダラー
      this.renderer = new THREE.WebGLRenderer({
        alpha: true,
        antialias: true,
      });
      this.renderer.setClearColor(new THREE.Color(0xffffff));
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      container.appendChild(this.renderer.domElement);

      // 滑らかにカメラコントローラーを制御する
      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      this.controls.enableDamping = true;
      this.controls.dampingFactor = 0.2;

      //光源
      const dirLight = new THREE.SpotLight(0xffffff, 1.5); //color,強度
      dirLight.position.set(0, 5, 5);
      this.scene.add(dirLight);

      // 軸の表示
      const axis = new THREE.AxesHelper(1000);
      axis.position.set(0, 0, 0);
      this.scene.add(axis);

      //glbファイルの読み込み
      const loader = new GLTFLoader();
      // こうしないとfunction内でsceneがないって言われる
      var scene_tmp = this.scene;
      var mixer_tmp = this.mixer;
      loader.load(
        "/test.glb",
        function (gltf) {
          // Model
          let model = gltf.scene;
          model.rotation.y = THREE.Math.DEG2RAD * 90;
          model.rotation.x = THREE.Math.DEG2RAD * -90;
          model.rotation.z = THREE.Math.DEG2RAD * 0;
          scene_tmp.add(model);

          // Animation
          let animations = gltf.animations;
          mixer_tmp = new THREE.AnimationMixer(model);
          let action = mixer_tmp.clipAction(animations[0]);
          action.play();
        },
        undefined,
        function (e) {
          console.error(e);
        }
      );
      this.scene = scene_tmp;
      this.mixer = mixer_tmp;
    },

    animate() {
      requestAnimationFrame(this.animate);
      if (this.mixer) this.mixer.update(0.03);
      this.renderer.render(this.scene, this.camera);
      // Controlの更新
      this.controls.update();
    },
  },
  mounted() {
    this.init();
    this.animate();
  },
};
</script>

<style scoped>
#WebGL-output {
  width: 1000px;
  height: 1000px;
}
</style>
