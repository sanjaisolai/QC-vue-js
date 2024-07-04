<template>
    <div>
        <button @click.prevent="fetch_longWords">Fetch long words</button>
        <div v-if="not_length !== null" style="justify-content:center">
          <h2>Long Words:</h2>
          <ul>
            <li v-for="(words, index) in not_length" :key="index">
              Record {{ index }}:
              <ul>
                <li v-for="(word, field) in words" :key="field">
                  <strong>{{ field }}:</strong>
                   <li v-for="x in word">
                    {{ x + '&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;length: ' + x.length }}

                   </li>
                </li>
              </ul>
            </li>
          </ul>
        </div>
        <p v-else-if="not_length===null && fetched">No Spell errors Detected</p>
      </div>
</template>

<script>
export default{
    name: 'not_length',
    data(){
        return{
            fetched: false,
            not_length: null,
            word_to_be_added: 'sample'
        }
    },
    methods:{
      async fetch_longWords(){
        try {
                const response = await fetch(`http://localhost:8000/length`, {
                    method: 'GET',
                });
                
                if (!response.ok) {
                    throw new Error('Failed to fetch data');
                }
                this.fetched=true
                const data = await response.json();
                this.not_length = data;
                console.log('Fetched not_length words:', this.not_length);
            } catch (error) {
                console.error('Error fetching data:', error);
            }
        }
      }
    }


</script>

<style>
</style>