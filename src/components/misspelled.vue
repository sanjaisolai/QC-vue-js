<template>
 
 <div>
    <button @click.prevent="fetch_words">Fetch Misspelled Words</button>
    <div v-if="misspelled !== null" style="justify-content:center">
      <h2>Misspelled Words:</h2>
      <ul>
        <li v-for="(words, index) in misspelled" :key="index">
          Record {{ index }}:
          <ul>
            <li v-for="(word, field) in words" :key="field">
              <strong>{{ field }}:</strong>
               <li v-for="x in word">
                {{ x }} <button @click="add(x)">Add</button>
               </li>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <p v-else-if="misspelled===null && fetched">No Spell errors Detected</p>
  </div>
    
</template>

<script>
export default {
    name: 'Misspelled',
    data() {
        return {
            fetched: false,
            misspelled: null,
            word_to_be_added: 'sample'
        };
    },
    methods: {
        async fetch_words() {
            try {
                const response = await fetch(`http://localhost:8000/spell_check/${this.word_to_be_added}`, {
                    method: 'GET',
                });
                
                if (!response.ok) {
                    throw new Error('Failed to fetch data');
                }
                this.fetched=true
                const data = await response.json();
                this.misspelled = data;
                console.log('Fetched misspelled words:', this.misspelled);
            } catch (error) {
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
                    throw new Error('Failed to add word');
                }
                const data = await response.json();
                this.misspelled = data;
                }catch(error){
                    console.log(error)
                }
        }
    }
};
</script>

<style>
/* Your component styles */
</style>
