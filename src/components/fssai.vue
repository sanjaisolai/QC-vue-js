<template>
<button @click="fetch_data">fetch fssai</button>
<div v-if="fetched===true">
    <ul>
        <li v-for="x in not_fssai">
            {{ x }}
        </li>
    </ul>
</div>
</template>

<script>
export default{
    name: 'fssai',
    data(){
        return{
            not_fssai: null,
            fetched: false
        }
    },
    methods:{
        async fetch_data(){
            try{
                const response=await fetch('http://localhost:8000/fssai_check',{
                    method: 'GET'
                })
                if(!response.ok){
                    throw new Error('Cannot connect with server')
                }
                const data=await response.json()
                this.not_fssai=data.not_fssai
                this.fetched=true
            }catch(error){
                console.log('error')
            }
        }
    }
}
</script>

<style>
</style>