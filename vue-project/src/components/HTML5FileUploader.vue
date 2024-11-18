<template>
  <div
    class="file-uploader"
    @dragover.prevent
    @drop.prevent="handleDrop"
    @click="triggerFileInput"
  >
    <input type="file" ref="fileInput" multiple @change="handleFiles" hidden />
    <p>Перетащите файлы сюда или нажмите, чтобы выбрать файлы</p>
  </div>
  <div class="container text-center">
    <ul>
      <li v-for="(file, index) in files" :key="index">
        {{ file.name }}
        <button @click="removeFile(index)">Удалить</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      files: []
    };
  },
  methods: {
    handleDrop(event) {
      const droppedFiles = Array.from(event.dataTransfer.files);
      this.files.push(...droppedFiles);
    },
    handleFiles(event) {
      const selectedFiles = Array.from(event.target.files);
      this.files.push(...selectedFiles);
    },
    triggerFileInput() {
      this.$refs.fileInput.click();
    },
    removeFile(index) {
      this.files.splice(index, 1);
    },
  }
};
</script>

<style scoped>
.file-uploader {
  border: 2px dashed #ccc;
  border-radius: 10px;
  padding: 20px;
  text-align: center;
  cursor: pointer;
}
.file-uploader:hover {
  border-color: #aaa;
}
button {
  margin-left: 10px;
  background-color: #ff4d4d;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
button:hover {
  background-color: #ff1a1a;
}
</style>
