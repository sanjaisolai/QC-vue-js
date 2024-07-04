<template>
    <div>
    <button @click.prevent="fetch_words">Fetch Misspelled Words</button>
    <div v-if="misspelled_long !== null" style="justify-content:center">
      <h2>Misspelled Words:</h2>
      <ul>
        <li v-for="(words, index) in misspelled_long" :key="index">
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
    <p v-else-if="misspelled_long===null && fetched">No Spell errors Detected</p>
  </div>
</template>


<script>
export default{
    name: 'misspelled_long',
    data(){
        return{
            fetched: false,
            misspelled_long: null,
            word_to_be_added: 'sample'
        }
    },
    methods:{
        async fetch_words() {
            try {
                const response = await fetch(`http://localhost:8000/spellLong/${this.word_to_be_added}`, {
                    method: 'GET',
                });
                
                if (!response.ok) {
                    throw new Error('Failed to fetch data');
                }
                this.fetched=true
                const data = await response.json();
                this.misspelled_long = data;
                console.log('Fetched misspelled_long words:', this.misspelled_long);
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
                this.misspelled_long = data;
                }catch(error){
                    console.log(error)
                }
        }
    }
}
</script>

<style scoped>

</style>