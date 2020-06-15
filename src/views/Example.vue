<template>
  <div id="example">
    <div class="container">
      <div id="bar-block">
        <div class="file-upload-block">
          <div>Название: <input type="text" class="file-upload-input" v-model="nameFile"></div>
          <label class="custom-file-upload">
            <input type="file"
            name="myFile"
            id="myFile"
            ref="myFile"
            @change="uploadFile"
            />
            <span id="upload">Выберите файл...</span>
          </label>
        </div>

        <div class="d-flex">

          <button class="btn--type2" @click="saveDesign">Сохранить разметку</button>
          <button class="btn--type2" @click="exportHtml">Сохранить HTML</button>
        </div>
      </div>

      <EmailEditor ref="emailEditor" @load="editorLoaded" :options="{locale: 'ru-RU'}" />
    </div>
  </div>
</template>

<script>
import { EmailEditor } from '../components'
import sample from '../data/sample.json';

export default {
  name: 'example',
  data: function () {
    return {
      nameFile: 'Верстка'
    }
  },
  components: {
    EmailEditor
  },
  methods: {
    uploadFile() {
      const fileUpload = this.$refs.myFile.files[0];
      const fileName = fileUpload.name;
      
      let reader = new FileReader();
      reader.onload = () => {
        let json = JSON.parse(reader.result);
        this.editorLoaded(json);
        document.getElementById('upload').innerHTML = "Файл: "+fileName;
        this.nameFile = fileName.split('.')[0];
      }
      reader.readAsText(fileUpload);
    },
    editorLoaded(json = sample) {
      this.$refs.emailEditor.editor.loadDesign(json);
    },
    getDate() {
      return new Date().toLocaleDateString();
    },
    saveDesign() {
      this.$refs.emailEditor.editor.saveDesign(
        (design) => {
          const dateNow = this.getDate();
          const nameFile = this.nameFile;

          const element = document.createElement("a");
          const file = new Blob([JSON.stringify(design)], {type: 'json/plain'});
          element.href = URL.createObjectURL(file);
          element.download = `${nameFile}.${dateNow}.json`;
          document.body.appendChild(element); // Required for this to work in FireFox
          element.click();
        }
      )
    },
    exportHtml() {
      this.$refs.emailEditor.editor.exportHtml(
        (data) => {
          const { html } = data;

          const dateNow = this.getDate();
          const nameFile = this.nameFile;
          
          const element = document.createElement("a");
          const file = new Blob([html], {type: 'html/plain'});
          element.href = URL.createObjectURL(file);
          element.download = `${nameFile}.${dateNow}.html`;
          document.body.appendChild(element); // Required for this to work in FireFox
          element.click();
        }
      )
    }
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
  height: 100%;
  font-family: Arial, "Helvetica Neue", Helvetica, sans-serif;
}

#app, #example {
  height: 100%;
}

#example .container {
  display: flex;
  flex-direction: column;
  position: relative;
  height: 100%;
}

#bar-block {
  flex: 1;
  background-color: #D6D9DC;
  color: #333;
  padding: 10px;
  display: flex;
  max-height: 40px;
  justify-content: space-between;
}

.btn--type2 {
  flex: 1;
  padding: 10px;
  margin-left: 10px;
  font-size: 14px;
  font-weight: bold;
  background-color: #298a34;
  color: #FFF;
  border: 0px;
  cursor: pointer;
  white-space: nowrap;
}
.btn--type1 {
  background-color: #165da6;
}
.custom-file-upload > input[type="file"] {
  display: none;
}
.custom-file-upload {
  flex: 1;
  padding: 10px;
  margin-left: 10px;
  font-size: 14px;
  font-weight: bold;
  background-color: #1671c1;
  color: #FFF;
  border: 0px;
  cursor: pointer;
  text-align: center;
  line-height: 1.5;
  white-space: nowrap;
}
.d-flex {
  display: flex;
}
.file-upload-block {
  display: flex;
  align-items: center;
}
.file-upload-input {
  padding: 10px;
  min-width: 300px;
}
</style>
