<template>
<div>
    <div v-if="type === 'raw'" class="container mt-4">
        <div class="alert alert-warning" role="alert">
            <h4 class="alert-heading">Note:</h4>
            <p>Do not interchange and upload the files.</p>
        </div>
        <h1 class="mb-4">Masterfile:</h1>
        <form @submit.prevent="uploadFile" enctype="multipart/form-data">
            <div class="mb-3">
                <label for="packaged" class="form-label">Packaged Food Item:</label>
                <select v-model="packaged" class="form-select" id="packaged" name="packaged">
                    <option value="1">Packaged food item</option>
                    <option value="0">Not a Packaged Food item</option>
                </select>
            </div>
            <div class="mb-3">
                <label for="excel_file" class="form-label">Choose Excel File:</label>
                <input class="form-control" type="file" id="excel_file" name="excel_file" accept=".xlsx, .xls" @change="handleFileUpload" />
            </div>
            <button type="submit" class="btn btn-primary">Upload</button>
            <p>{{ message }}</p>
        </form>
    </div>
    <div v-else-if="type === 'long'" class="container mt-4">
        <h1 class="mb-4">Long Description:</h1>
        <form @submit.prevent="uploadFile" enctype="multipart/form-data">
            <div class="mb-3">
                <label for="excel_file" class="form-label">Choose Excel File:</label>
                <input class="form-control" type="file" id="excel_file" name="excel_file" accept=".xlsx, .xls" @change="handleFileUpload" />
            </div>
            <button type="submit" class="btn btn-primary">Upload</button>
            <p>{{ message }}</p>
        </form>
    </div>
</div>

</template>

<script>
export default {
  name:'file_upload',
  data() {
    return {
      file: null,
      message: '',
      type: 'raw',
      packaged: '1'
    };
  },
  methods: {
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file && file.type === 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet') {
        this.file = file;
        this.message = '';
      } else {
        this.file = null;
        this.message = 'Please select a valid Excel file (.xlsx format)';
      }
    },
    async uploadFile() {
      if (!this.file) {
        this.message = 'Please select a file';
        return;
      }
      const formData = new FormData();
      formData.append('excel_file', this.file);
      try {
        const response = await fetch(`http://localhost:8000/api/upload/${this.type}/${this.packaged}/`, {
          method: 'POST',
          body: formData,
        });
        if (response.ok) {
          const data = await response.json();
          console.log(data)
          if(data.template==='Wrong Template pls Upload the correct one'){
            this.message=data.template
          }
          else{
            this.message='';
            if (this.type === 'long') {
              this.$emit('upload_success',this.packaged); 
            }
            if (this.type === 'raw') {
              this.type = 'long'; 
            }
          }
        } else {
          this.message = 'File upload failed';
        }
      } catch (error) {
        console.log(error)
      }
      
    },
  },
};
</script>

<style>
/* Your component styles */
</style>
