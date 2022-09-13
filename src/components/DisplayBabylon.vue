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
        const tamañoDiagonal = 0.1;
        var mp = meshes.length - 1;
        var sizeb = meshes[mp].getBoundingInfo().diagonalLength;
        var scale =  tamañoDiagonal / meshes[mp].getBoundingInfo().diagonalLength ;
        console.log(scale)
        if (sizeb < 1){
          var scl = 0;
          for(var i = 0; i < meshes.length;  i++){
              meshes[i].scaling.x /= sizeb;
              meshes[i].scaling.y /= sizeb;
              meshes[i].scaling.z /= sizeb;
              scl += meshes[i].scaling.x + meshes[i].scaling.y + meshes[i].scaling.z;
          }
          sizeb =  (scl/mp)/2;
        }

        
        // console.log(meshes[mp].position)
        // console.log("Scale", meshes[mp].scaling)
        
        var size = meshes[mp].getBoundingInfo().boundingBox.extendSize;

        // sizeb = meshes[mp].getBoundingInfo().diagonalLength;
        // console.log(camera.position);
        camera.position.x *=  sizeb ;
        camera.position.y *= sizeb ;
        camera.position.z *=  sizeb;

        camera.lowerRadiusLimit = sizeb / 1.5;
        
        camera.target = meshes[mp].getBoundingInfo()['boundingBox']['centerWorld'];

      // camera.parent = meshes[mp];
        //https://stackoverflow.com/questions/36285882/how-to-calculate-in-babylon-js-the-camera-position-to-the-loaded-model-intermedd
        
        this.engine.hideLoadingUI();
      },
      dispose(){
        this.scene.dispose();
        this.engine.dispose();
      }


  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  /* canvas {
      overflow: hidden;
      width: 75%;
      height: 75%; 
      aspect-ratio: 16 / 9;
      margin: 0;
      padding: 0;
}  */
.cnv{
  /* width: 75%;
  /* height: 100%; */
  position: relative;
  padding-right: 25px;
  padding-bottom: 0px;
}

.btn{
  bottom: 0;
  right: 25px;
  position: absolute;
  width: 15%;
}
</style>
