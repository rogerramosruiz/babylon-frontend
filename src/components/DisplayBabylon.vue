<template>
  <div class="mr-10 overflow-auto" id="cnvid">
      <canvas class="w-full max-h-[40rem] "/>
     <div>
      <button class="bg-green-400 text-white px-2 m-1 rounded-full" v-on:click="fullScreen" id="view-fullscreen">Pantalla completa</button>
    </div> 
  </div>
</template>

<script>

import { 
    Scene, 
    Engine, 
    ArcRotateCamera,
    Vector3, 
    HemisphericLight,
    Color3,  
    SceneLoader
} from 'babylonjs';

import 'babylonjs-loaders';

export default {
  name: 'DisplayBabylon',
  props: ['modelURL'],
  watch:{
    modelURL:function(){
      this.dispose();
      this.ModelLoader();
    }
  },
  mounted(){
    this.canvas = document.querySelector('canvas');
    this.ModelLoader();

  },
  data(){
    return {
      engine: null,
      scene: null,
      camera: null,
      canvas: null
    }
  },
  methods:{
    fullScreen(){
      const canvas = document.querySelector("canvas");
      // const canvas = document.querySelector(".cnv");

      if (canvas.requestFullScreen) {
        canvas.requestFullScreen();
      } else if (canvas.webkitRequestFullScreen) {
          canvas.webkitRequestFullScreen();
      } else if (canvas.mozRequestFullScreen) {
          canvas.mozRequestFullScreen();
      } else if (canvas.msRequestFullscreen) {
          canvas.msRequestFullscreen();
      } else if (canvas.webkitEnterFullscreen) {
        canvas.webkitEnterFullscreen(); 
      }
    },
    
    ModelLoader(){
      this.engine = new Engine(this.canvas, true);
      const scene = this.createScene();
      this.scene = scene;

      window.addEventListener("resize", () => {
          this.engine.resize();
      });
      this.engine.runRenderLoop(()=>{
          scene.render();
      });
    },

    createScene(){
        var scene = new Scene(this.engine);
        scene.clearColor = Color3.Black();
        var camera = new ArcRotateCamera("camera", Math.PI/2, Math.PI / 2 , 2, Vector3.Zero(), scene)
        camera.attachControl(this.canvas, true);
        camera.wheelPrecision = 15;
        camera.pinchDeltaPercentage *= -1;
        camera.pinchPrecision = 75;
        const light = new HemisphericLight("light", new Vector3(0, 1, 0), scene);

        scene.registerBeforeRender( ()=> {
            light.position = camera.position;
        });
        this.loadModel(scene, camera);

        return scene;
    },

    async loadModel(scene, camera){
      
        this.engine.displayLoadingUI();
        const {meshes, animationGroups, url} = await SceneLoader.ImportMeshAsync("", '', this.modelURL, scene);       
        var boundingBox = 1;
        var size = meshes[boundingBox].getBoundingInfo().diagonalLength;
        if (size < 1){
          var scl = 0;
          for(var i = 0; i < meshes.length;  i++){
              meshes[i].scaling.x /= size;
              meshes[i].scaling.y /= size;
              meshes[i].scaling.z /= size;
              scl += meshes[i].scaling.x + meshes[i].scaling.y + meshes[i].scaling.z;
          }
          size =  (scl/boundingBox)/2;
        }
        camera.position.x *=  size ;
        camera.position.y *= size ;
        camera.position.z *=  size;

        camera.lowerRadiusLimit = size / 1.2;
        camera.target = meshes[boundingBox].getBoundingInfo()['boundingBox']['centerWorld'];        
      
        this.engine.hideLoadingUI();
      
      },
      dispose(){
        this.scene.dispose();
        this.engine.dispose();
      }


  }

}
</script>

<style scoped>
</style>
