<template>
    <div class="container mt-4 text-center">
        <h2 id="progress-text" class="text-center">Task is still processing</h2>
        <div id="loader" class="d-flex justify-content-center align-items-center mt-4">
            <div class="spinner-border" role="status">
                <span class="visually-hidden">Loading...</span>
            </div>
        </div>
    </div>
</template>

<script>
export default{
    name: 'loader'
}
</script>

<style>
</style>