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
    <div v-if="misspelled_long && Object.keys(misspelled_long).length>0" style="justify-content:center">
      <h2>Misspelled Words:</h2>
      <div class="container mt-4">
      <ul>
        <li v-for="(words, index) in misspelled_long" :key="index">
          Record {{ index }}:
          <ul class="list-group">
            <li class="list-group-item" v-for="(word, field) in words" :key="field">
              <strong>{{ field }}:</strong>
              <ul>
                <li v-for="x in word">
                  <div class="word-item">
                    {{ x }}&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <button class="btn btn-sm btn-success" @click="confirmAdd(x)">Add</button><br>
                  </div>
                </li>
              </ul>
            </li>
          </ul>
        </li>
      </ul>
      </div>
    </div>
    <p v-else-if="misspelled_long !== null && fetched">No Spell errors Detected</p>
  </div>

</template>


<script>
import loader from './loader.vue';
export default{
    name: 'misspelled_long',
    components:{
        loader,
    },
    data(){
        return{
            fetched: false,
            misspelled_long: null,
            word_to_be_added: 'sample',
            is_loaded: false,
            issue: false
        }
    },
    mounted(){
        this.fetch_words();
    },
    methods:{
        async fetch_words() {
            try {
                const response = await fetch(`http://localhost:8000/spellLong/${this.word_to_be_added}`, {
                    method: 'GET',
                });
                
                if (!response.ok) {
                    this.issue=true;
                    throw new Error('Failed to fetch data');
                }
                this.fetched=true
                const data = await response.json();
                this.misspelled_long = data;
                this.is_loaded=true
                console.log('Fetched misspelled_long words:', this.misspelled_long);
            } catch (error) {
                this.issue=true
                console.error('Error fetching data:', error);
            }
        },
        async add(word){
                try{
                this.word_to_be_added=word
                const response = await fetch(`http://localhost:8000/spell_check/${this.word_to_be_added}`, {
                        method: 'POST',
                    });
                if(!response.ok){
                    this.issue=true
                    throw new Error('Failed to add word');
                }
                const data = await response.json();
                this.misspelled_long = data;
                }catch(error){
                    this.issue=true
                    console.log(error)
                }
        },
        confirmAdd(word) {
            if (confirm(`Are you sure you want to add the word "${word}"?`)) {
                this.add(word);
            }
        }
    }
}
</script>

<style scoped>

</style>