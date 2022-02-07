<template>
  <div id="app">
    <div>
      <h2>Select an image</h2>
      <input multiple type="file" @change="uploadFiles" class="button">
      <button v-if="images.length > 0" @click="changeOrder" class="button">Изменить порядок</button>
    </div>

    <div v-if="images" class="list">
      <div v-for="(image, index) in images" :key="index" class="item">
        <figure class="figure">
          <img :src="image" :alt="image.title" class="image"/>
        </figure>
        <button @click="removeImage(index)" class="button">Удалить изображение</button>
      </div>
    </div>

    <button v-if="images.length > 0" @click="submitFile()" class="button">Отправить</button>

    <div v-if="!status" class="message">
      {{ message }}
      <strong @click="close" class="close">X</strong>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      images: [],
      status: true,
      message: null
    }
  },
  methods: {
    uploadFiles(e) {
      let files = e.target.files || e.dataTransfer.files;
      if (!files.length) return;
      this.createImage(files);
    },

    createImage(files) {
      for (let index = 0; index < files.length; index++) {
        let newReader = new FileReader();
        newReader.onload = event => {
          const imageUrl = event.target.result;
          this.images.push(imageUrl);
        }
        newReader.readAsDataURL(files[index]);
      }
    },

    removeImage(index) {
      this.images.splice(index, 1)
    },

    changeOrder() {
      this.images.sort(() => .5 - Math.random());
    },

    submitFile() {
      const data = new FormData()

      for (let i = 0; i < this.images.length; i++) {
        let image = this.images[i];
        data.append(`images['${i}']`, image);
      }

      fetch('/my-images', {
        method: 'POST',
        body: data,
      }).then(response => {
        if (response.ok) {
          this.message = 'SUCCESS'
          this.status = true

        } else {
          this.message = 'Network response was not ok.'
          this.status = false
        }
      }).catch(console.error)
    },

    close() {
      this.status = true
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.list {
  display: flex;
  flex-wrap: wrap;
  max-width: 960px;
  margin: 0 auto;
}

.item {
  flex: 1 1 30%;
  padding: 0.2rem;
  box-shadow: 0 0 11px 0 #e1e1e1;
  margin: 0.2rem;
  border-radius: 6px;
}

.figure {
  display: flex;
  align-items: center;
  justify-content: center;
  max-width: 375px;
  height: 200px;
}

.image {
  display: block;
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}

.button {
  padding: 0.5rem;
  margin: 0.5rem;
  font-size: 1rem;
}

.message {
  padding: 2rem;
}

.close {
  cursor: pointer;
}

</style>
