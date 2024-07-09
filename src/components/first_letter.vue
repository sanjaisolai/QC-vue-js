<template>
    <div v-if="!is_loaded">
      <div v-if="!issue">
          <loader />
      </div>
      <div v-else class="alert alert-danger" role="alert">
          <h1>Problem with the server</h1>
      </div>
    </div>
    <div v-else>
    <!-- <button @click.prevent="fetch_lines">Fetch Non title case names</button> -->
    <div v-if="first_letter && Object.keys(first_letter).length > 0" style="justify-content:center">
        <h2>Non Title case:</h2>
        <div class="container mt-4">
            <ul>
                <li v-for="(words, index) in first_letter" :key="index">
                    Record {{ index }}:
                    <ul class="list-group">
                        <li class="list-group-item" v-for="(word, field) in words" :key="field">
                            <strong>{{ field }}:</strong>
                            <ul>
                                <li v-for="x in word">
                                    {{ x }}
                                </li>
                            </ul>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
    <div v-else-if="first_letter !== null && fetched" class="alert alert-success" role="alert"><h1>Everything seems right</H1></div>
</div>

</template>

<script>
import loader from './loader.vue';
export default{
    name: 'first_letter',
    components:{
        loader,
    },
    data(){
        return{
            fetched: false,
            first_letter: null,
            is_loaded: false,
            issue: false
        }
    },
    mounted(){
      this.fetch_lines();
    },
    methods:{
      async fetch_lines(){
        try {
                const response = await fetch(`http://localhost:8000/First_Letter`, {
                    method: 'GET',
                });
                
                if (!response.ok) {
                    this.issue=true
                    throw new Error('Failed to fetch data');
                }
                this.fetched=true
                const data = await response.json();
                this.first_letter = data;
                this.is_loaded=true
                console.log('Fetched first_letter words:', this.first_letter);
            } catch (error) {
                this.issue=true
                console.error('Error fetching data:', error);
            }
        }
      }
    }


</script>

<style>
</style>