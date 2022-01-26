<template>
  <v-app>
    <navi />
    <v-main>
      <h1>JSON読み込みテスト</h1>
      <div id="json-output"></div>
    </v-main>
  </v-app>
</template>

<script>
import navi from "/components/navi";
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

export default {
  components: {
    navi,
  },
  data() {
    return {
      files: [],
      frame: 0,
      meshes: [],
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
      let container = document.getElementById("json-output");

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
      this.camera.position.set(100, 100, 100);
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
      dirLight.position.set(200, 200, 200);
      this.scene.add(dirLight);

      // 軸の表示
      const axis = new THREE.AxesHelper(1000);
      axis.position.set(0, 0, 0);
      this.scene.add(axis);

      // JSONの読み込み
      const context = require.context("../static/test/", false, /\.json$/);
      const filenames = context.keys();
      for (let name of filenames) {
        this.files.push(
          JSON.parse(JSON.stringify(require("../static/test" + name.substr(1))))
        );
      }

      // ジオメトリを作成
      var geometry = new THREE.BoxGeometry(1, 1, 1);
      // マテリアルにテクスチャーを設定
      var material = new THREE.MeshNormalMaterial();

      for (let point = 0; point < 25; point++) {
        // メッシュの作成
        var mesh = new THREE.Mesh(geometry, material);
        // シーンに追加
        this.scene.add(mesh);
        // リストにメッシュを追加
        this.meshes.push(mesh);
      }
    },

    animate() {
      requestAnimationFrame(this.animate);
      // フレームのループ
      if (this.files.length - 1 == this.frame) {
        this.frame = 0;
      } else {
        this.frame += 1;
      }

      // キーポイントごとにオブジェクトを移動
      for (let point = 0; point < 25; point++) {
        // 腰(point=8)の座標を(0,0)に正規化
        const x = this.files[this.frame].people[0].pose_keypoints_2d[point * 3] - this.files[0].people[0].pose_keypoints_2d[8*3];
        const y =
          this.files[this.frame].people[0].pose_keypoints_2d[point * 3 + 1] - this.files[0].people[0].pose_keypoints_2d[8*3+1];
        // オブジェクトの移動と大きさの調整
        this.meshes[point].position.x = x*0.1;
        this.meshes[point].position.y = y*0.1*-1;
      }

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
#json-output {
  width: 500px;
  height: 500px;
}
</style>
