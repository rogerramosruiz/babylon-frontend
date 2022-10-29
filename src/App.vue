<template>
  <header class=" flex bg-green-800 justify-center font-mono m-1">
    <h1 class="text-2xl ">
      UG<span>ICA</span>
    </h1>
  </header>
  <div class="overflow-x-hidden ">
    <div class="grid grid-cols-1 md: md:grid-cols-5 place-items-center">
        <div class="md:col-span-4 w-full h-max md: ml-5">
          <DisplayBabylon :modelURL='modelURL'/>
        </div>
      <div class="mr-2 w-full">
          <ListModels @changemodel='changemodel'/>
        </div>
    </div>

  </div>
</template>

<script>
import DisplayBabylon from './components/DisplayBabylon.vue'
import ListModels from './components/ListModels.vue';
export default {  
  name: 'App',
  components: {
    DisplayBabylon,
    ListModels
  },
  methods:{
      toggleOpen: function (index){
        this.faqs = this.faqs.map((faq, i) =>{
          if (index === i){
            faq.open = ! faq.open;
          }
          else{
            faq.open = false;
          }
          return faq
        });
      },
      changemodel(modelURL){
        this.modelURL = process.env.VUE_APP_API_URL+modelURL;
      }
  },
  data(){
    return {
      modelURL: process.env.VUE_APP_API_URL+'models/QUÍMICA/Sólido cúbico centrado.glb'
    }
  }
}
</script>

<style>

body{
  font-family: 'montserat', sans-serif;
  background-color: #EEE;
}

header h1{
  color: #eee;
}
header h1 span{
  color: #FB9B50;
  font-weight: 900;
}

</style>
