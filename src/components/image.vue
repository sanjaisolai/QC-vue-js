<template>
    <div v-if="available===1">
        <div v-if="progress!==100">
            <div class="container mt-4 text-center">
                <h2 id="progress-text" class="text-center">Task is still processing: {{ progress }}%</h2>
                <div id="loader" class="d-flex justify-content-center align-items-center mt-4">
                    <div class="spinner-border" role="status">
                        <span class="visually-hidden">Loading...</span>
                    </div>
                </div>
            </div>
        </div>
        <div v-else>
            <div v-if="progress === 100 && not_hd_image && Object.keys(not_hd_image).length > 0 && broken_urls && broken_urls.length > 0">
                <div v-for="(fields, record) in not_hd_image" :key="record" class="mb-4">
                    <h2 class="record-title">Record {{ record }}</h2>
                    <ul class="list-group">
                    <li v-for="(words, field) in fields" :key="field" class="list-group-item">
                        <strong>Image:</strong>
                        <br>
                        <div v-for="word in words" :key="word" class="image-item">
                            <img :src="word" alt="Image in doubt">
                            <input type="checkbox" value="Done" class="ms-2">
                        </div>
                    </li>
                    </ul>
                </div>
                <div>
                    <h2>URLS that seem to be broken: </h2>
                    <ul>
                        <li v-for="x in broken_urls">
                            {{ x }}
                        </li>
                    </ul>
                </div>
            </div>
            <div v-else>
                <h2>All images are hd</h2>
            </div>
        </div>
        
    </div>
    <div v-else-if="available!==1 && fetched">
        <p>Images are not uploaded pls try again by uploading the excel sheet :(</p>
    </div>
</template>

<script>
import loader from './loader.vue';
export default{
    name: 'image_page',
    components:{
        loader
    },
    data(){
        return{
            fetched: false,
            not_hd_image: null,
            available: null,
            progress:0,
            broken_urls:null,
            interval: null
        }
    },
    mounted(){
        this.check_availability()
        
    },
    methods:{
        async check_availability(){
            try{
                const response = await fetch(`http://localhost:8000/image_quality`, {
                                        method: 'GET',
                                    });

                if(!response.ok){
                    throw new Error("Cannot communicate with the server")
                }
                this.fetched=true;
                const data = await response.json()
                this.available=data.response
                if(this.available===1){
                    this.startFetchingProgress()
                }
                            
            }catch(error){
                console.log(error)
            }
            
        },
        startFetchingProgress() {
            this.fetch_progress();
            this.interval = setInterval(() => this.fetch_progress(), 5000);
        },
        async fetch_progress(){
            try{
                const response=await fetch('http://localhost:8000/progress',{
                    method:'GET'
                })
                if(!response.ok){
                    throw new Error('Cannot communicate with the server')
                }
                const data=await response.json()
                this.progress=data.progress
                if(this.progress===100 && this.interval){
                    clearInterval(this.interval);
                    const displayresponse=await fetch('http://localhost:8000/display_image',{
                        method: 'GET'
                    });
                    if(!displayresponse.ok){
                        throw new Error('cant communicate')
                    }
                    let image_data=await displayresponse.json()
                    this.not_hd_image=image_data.wrong_image
                    this.broken_urls=image_data.wrong_urls
                    this.$emit('image_success',this.progress)
                }

            }catch(error){
                console.log(error)
            }

        }
    }
}
</script>

<style>
</style>