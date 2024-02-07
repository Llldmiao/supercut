<script setup lang="ts">
// Import Vue FilePond
import vueFilePond from 'vue-filepond'

// Import FilePond styles
import 'filepond/dist/filepond.min.css'

// Import FilePond plugins
// Please note that you need to install these plugins separately

// Import image preview plugin styles
import 'filepond-plugin-image-preview/dist/filepond-plugin-image-preview.min.css'

// Import image preview and file type validation plugins
import FilePondPluginFileValidateType from 'filepond-plugin-file-validate-type'
import FilePondPluginImagePreview from 'filepond-plugin-image-preview'
import FilePondPluginFileValidateSize from 'filepond-plugin-file-validate-size'

// Create component
const FilePond = vueFilePond(
  FilePondPluginFileValidateType,
  FilePondPluginImagePreview,
  FilePondPluginFileValidateSize,
)

const myFiles = reactive([])
const pond = ref('')
</script>

<template>
  <div>
    <FilePond
      ref="pond"
      name="test"
      label-idle="Drop files here or <span class='filepond--label-action'>Browse</span>"
      allow-multiple="true"
      max-files="10"
      accepted-file-types="*"
      :files="myFiles"
    />
  </div>
</template>

<style scoped>

</style>
