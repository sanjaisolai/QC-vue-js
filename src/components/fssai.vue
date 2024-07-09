<template>
    <div v-if="!is_loaded">
      <div v-if="!issue">
          <loader />
      </div>
      <div v-else>
          <h1>Problem with the server</h1>
      </div>
    </div>
    <div v-else>

        <div v-if="not_fssai && Object.keys(not_fssai).length>0" class="container mt-4">
            <div v-for="(fields, record) in not_fssai" :key="record" class="mb-4">
                <h2 class="record-title">Record {{ record }}</h2>
                <div class="container mt-4">
                    <ul class="list-group">
                        <li v-for="(words, field) in fields" :key="field" class="list-group-item">
                            <strong>{{ field }}</strong>
                            <br>
                            <div v-for="word in words" :key="word">
                                <div class="image-item">
                                    <img :src="word" alt="Image in doubt">
                                    
                                    <button class="btn btn-sm btn-success" @click="confirmAdd(word)">Add</button>
                                </div>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
        <div v-else-if="not_fssai && not_fssai !==null && fetched">
            <h1>Everything Seems right</h1>
        </div>
    </div>  

</template>

<script>
export default{
    name: 'fssai',
    data(){
        return{
            not_fssai: null,
            fetched: false,
            issue: false,
            is_loaded: false,
            image_to_be_added:'sample'
        }
    },
    mounted(){
        this.fetch_data();
    },
    methods:{
        async fetch_data(){
            try{
                const response=await fetch(`http://localhost:8000/fssai_check`,{
                    method: 'GET'
                })
                if(!response.ok){
                    this.issue=true
                    throw new Error('Cannot connect with server')
                }
                const data=await response.json()
                this.not_fssai=data
                this.is_loaded=true
                this.fetched=true
                console.log(this.not_fssai)
            }catch(error){
                this.issue=true
                console.log(error)
            }
        },
        confirmAdd(word) {
            if (confirm(`Are you sure you want to add the word "${word}"?`)) {
                this.add_image(word);
            }
        },
        async add_image(image){
            try{
                this.image_to_be_added=encodeURIComponent(image)
                const response=await fetch(`http://localhost:8000/add_image/${this.image_to_be_added}/`,{
                    method: 'POST'
                });
                if(!response.ok){
                    throw new Error('Failed to add word');
                }
                alert('Model trained to recognize this image the same image will not be shown in the future')
            }catch(error){
                console.log(error)
            }   

        }
    }
}
</script>

<style>
</style>