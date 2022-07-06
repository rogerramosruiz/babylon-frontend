<template>
  <header>
    <h1>
      UG<span>ICA</span>
    </h1>
  </header>

  <div class="main-view">
      <DisplayBabylon :modelURL='modelURL'/>
      <div>
        <ListModels @changemodel='changemodel'/>
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
      modelURL: process.env.VUE_APP_API_URL+'models/VARIOS/Anquilosaurio.glb'
    }
  }
}
</script>

<style>
*{
  margin: 0px;
  padding: 0px;
  box-sizing: border-box;

}
body{
  font-family: 'montserat', sans-serif;
  background-color: #EEE;
}
header {
  background-color: #11224d9a;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px;
  margin-bottom: 5px;
}
header h1{
  color: #eee;
  font-size: 28px;
  font-weight: 300;
  text-transform: uppercase;
}
header h1 span{
  color: #FB9B50;
  font-weight: 900;
}

.main-view{
     display: flex;
    /* zoom: 1;  */
    /* *display: inline; */
}
</style>
