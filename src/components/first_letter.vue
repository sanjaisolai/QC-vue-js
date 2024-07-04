<template>
    <div>
        <button @click.prevent="fetch_lines">Fetch Non title case names</button>
        <div v-if="first_letter !== null" style="justify-content:center">
          <h2>Non Title case:</h2>
          <ul>
            <li v-for="(words, index) in first_letter" :key="index">
              Record {{ index }}:
              <ul>
                <li v-for="(word, field) in words" :key="field">
                  <strong>{{ field }}:</strong>
                   <li v-for="x in word">
                    {{ x }} 
                   </li>
                </li>
              </ul>
            </li>
          </ul>
        </div>
        <p v-else-if="first_letter===null && fetched">No Spell errors Detected</p>
      </div>
</template>

<script>
export default{
    name: 'first_letter',
    data(){
        return{
            fetched: false,
            first_letter: null,
            word_to_be_added: 'sample'
        }
    },
    methods:{
      async fetch_lines(){
        try {
                const response = await fetch(`http://localhost:8000/First_Letter`, {
                    method: 'GET',
                });
                
                if (!response.ok) {
                    throw new Error('Failed to fetch data');
                }
                this.fetched=true
                const data = await response.json();
                this.first_letter = data;
                console.log('Fetched first_letter words:', this.first_letter);
            } catch (error) {
                console.error('Error fetching data:', error);
            }
        }
      }
    }


</script>

<style>
</style>