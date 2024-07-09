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
    <!-- <button @click.prevent="fetch_longWords">Fetch long words</button> -->
    <div v-if="not_length && Object.keys(not_length).length>0" style="justify-content:center">
        <h2>Long Words:</h2>
        <div class="container mt-4">
            <ul>
                <li v-for="(words, index) in not_length" :key="index">
                    Record {{ index }}:
                    <ul class="list-group">
                        <li class="list-group-item" v-for="(word, field) in words" :key="field">
                            <strong>{{ field }}:</strong>
                            <ul>
                                <li v-for="x in word">
                                    {{ x }}&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;length: {{ x.length }}
                                </li>
                            </ul>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
    <p v-else-if="not_length !== null && fetched">No Spell errors Detected</p>
</div>

</template>

<script>
import loader from './loader.vue';
export default{
    name: 'not_length',
    components:{
        loader,
    },
    data(){
        return{
            fetched: false,
            not_length: null,
            is_loaded: false,
            issue: false
        }
    },
    mounted(){
      this.fetch_longWords();
    },
    methods:{
      async fetch_longWords(){
        try {
                const response = await fetch(`http://localhost:8000/length`, {
                    method: 'GET',
                });
                
                if (!response.ok) {
                    this.issue=true
                    throw new Error('Failed to fetch data');
                }
                this.fetched=true
                const data = await response.json();
                this.not_length = data;
                this.is_loaded=true
                console.log('Fetched not_length words:', this.not_length);
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