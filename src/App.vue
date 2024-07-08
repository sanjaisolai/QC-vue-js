<template>
  <div>
    <header :class="[`bg-dark`]">
      <h1 style="padding-left: 30px;">QC CHECK</h1>
    </header>

    <div v-if="!upload">
      <file_upload @upload_success="upload_status" />
    </div>
    <div v-else>
      <nav :class="['navbar', `navbar-dark`, `bg-dark`]">
        <div class="container-fluid">
          <ul class="nav">
            <li class="nav-item" @click="refresh">
              <a class="nav-link active" aria-current="page" href="#">upload</a>
            </li>
            <li class="nav-item" @click="master_spell=true; long_spell=false;first_letter=false;not_length= false;image_page=false;fssai_display=false">
              <a class="nav-link active" aria-current="page" href="#">master file spell check</a>
            </li>
            <li class="nav-item" @click="long_spell=true; master_spell=false;first_letter=false;not_length= false;image_page=false;fssai_display=false">
              <a class="nav-link active" aria-current="page" href="#">Long Description spell check</a>
            </li>
            <li class="nav-item" @click="first_letter=true; master_spell=false;long_spell=false;not_length= false;image_page=false;fssai_display=false">
              <a class="nav-link active" aria-current="page" href="#">Title case check</a>
            </li>
            <li class="nav-item" @click="not_length=true; master_spell=false;long_spell=false;first_letter=false;image_page=false;fssai_display=false">
              <a class="nav-link active" aria-current="page" href="#">Length check</a>
            </li>
            <li class="nav-item" @click="image_page=true; master_spell=false;long_spell=false;first_letter=false;not_length=false;fssai_display=false">
              <a class="nav-link active" aria-current="page" href="#">Image</a>
            </li>
            <div v-if="packaged==='1' && progress===100">
            <li class="nav-item" @click="fssai_display=true; master_spell=false;long_spell=false;first_letter=false;not_length=false;image_page=false">
              <a class="nav-link active" aria-current="page" href="#">fssai</a>
            </li>
          </div>
          </ul>
        </div>
      </nav>

      <!-- Display components based on conditions -->
      <div v-if="master_spell">
        <misspelled />
      </div>
      <div v-else-if="long_spell">
        <misspelled_long />
      </div>
      <div v-else-if="first_letter">
        <first_letter />
      </div>
      <div v-else-if="not_length">
        <length />
      </div>
      <div v-else-if="image_page">
        <image_page @image_success="call_fssai" />
      </div>
      <div v-else-if="fssai_display">
        <fssai />
      </div>
    </div>
  </div>
</template>

<script>
import file_upload from './components/file_upload.vue';
import misspelled from './components/misspelled.vue';
import misspelled_long from './components/misspelled_long.vue';
import first_letter from './components/first_letter.vue';
import length from './components/length.vue';
import image_page from './components/image.vue';
import fssai from './components/fssai.vue';

export default {
  name: 'App',
  components: {
    file_upload,
    misspelled,
    misspelled_long,
    first_letter,
    length,
    image_page,
    fssai
  },
  data() {
    return {
      upload: false,
      master_spell: false,
      long_spell: false,
      first_letter: false,
      not_length: false,
      image_page: false,
      fssai_display: false,
      packaged: '1',
      progress: 0
    };
  },
  methods: {
    upload_status(is_packaged) {
      this.upload = true; // Update upload status to true on successful upload
      this.packaged=is_packaged
    },
    refresh(){
      location.reload();
    },
    call_fssai(progress){
      this.progress=progress
    }
  }
};
</script>

<style scoped>
header {
  padding: 1em 0;
  color: white;
}


.navbar .container-fluid {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.nav {
  display: flex;
  list-style-type: none; /* Remove bullet points from list items */
  padding: 0; /* Remove default padding */
}

.nav-item {
  margin-right: 1em; /* Adjust spacing between navigation items */
}

.nav-link {
  color: white;
  text-decoration: none;
}

.nav-link.active {
  font-weight: bold;
}

.nav-link:hover {
  background-color: black;
  border-radius: 10px;
}
</style>
