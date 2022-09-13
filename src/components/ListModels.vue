<template>
    <div class="h-[27rem] md:h-[43rem] max-h-screen overflow-y-auto overflow-x-hidden flex flex-col flex-grow">
    <div  v-for="(areaData, area) in areas" :key="area" >
        <div class ="flex justify-center bg-green-500 text-white rounded-xl mx-2 mb-1 mt-2 w-5/6 ">
            {{area}}
        </div>
        <div class="grid w-full md:w-4/6 ml-6" v-for="(modelData, name) in areaData" :key='name'>
            <button class="bg-orange-400 p-1 mb-1 rounded-2xl text-white w-5/6 md:w-full" v-on:click="$emit('changemodel', modelData.modelURL)"> {{name}}</button >
        </div>
        </div>
    </div>
</template>

<script>

export default{
    name: 'ListModels',
    data(){
        return{
            areas: {},
        }
    },
    mounted(){
        this.getFiles();
    },
    methods:{
    async getFiles(){
        var api = process.env.VUE_APP_API_URL+'files'
        var data = await fetch(api);
        data = await data.json();
        this.areas = data;
      }
    },
}

</script>

<style scoped>
.area{
    justify-content: center;
    background-color: white;
    margin: 5px;
    border-radius: 12px;
}
/* button{
    background-color: orange;
    padding: 5px;
    border-radius: 5px;
    color: white;
} */
</style>
