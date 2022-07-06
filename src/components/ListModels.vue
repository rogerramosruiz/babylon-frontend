<template>
    <div>
    <div v-for="(areaData, area) in areas" :key="area" class="area">
        {{area}}
        <div v-for="(modelData, name) in areaData" :key='name'>
            <button v-on:click="$emit('changemodel', modelData.modelURL)"> {{name}}</button >
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
button{
    background-color: orange;
    padding: 5px;
    border-radius: 5px;
    color: white;
}
</style>
