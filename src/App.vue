<template>
  <div id="app">
    <div class="container classForCenter">
      <div class="header">
        <span class="header__title classForCenter">File uploader</span>
      </div>

      <div class="content content_forMarginTop">
        <form ref="placeToDrop" class="classForCenter">
          <span class="content__clueTitle">Перенесите файл сюда!</span>
        </form>

        <div class="content__sendFilesBtn content__btnMarginTop classForCenter">
          ОТПРАВИТЬ
        </div>
      </div>

      <div class="footer">
        <span class="footer__title classForCenter" v-if="droppedFiles.length === 0">Здесь будут отображаться ваши загруженные файлы.</span>

        <div class="footer__preview" v-else>
          <div v-for="(file, key) in droppedFiles" :key="key" class="footer__listFiles">
            <img class="footer__imgPreview" :ref="`preview${parseInt(key)}`"/>
            {{ file.name }}
          </div>
        </div>
      </div>
    </div>
<!--    <img alt="Vue logo" src="./assets/logo.png">-->
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      droppedFiles: [],
      dragMoves: ['drag', 'drop', 'dragstart', 'dragend', 'dragover', 'dragenter', 'dragleave']
    }
  },
  mounted () {
    if (this.isDragAndDrop()) {
      this.dragMoves.forEach((e) => {
        this.$refs.placeToDrop.addEventListener(e, (event) => {
          event.preventDefault()
          event.stopPropagation()
        })
      })

      this.$refs.placeToDrop.addEventListener('drop', (e) => {
        for (let i = 0; i < e.dataTransfer.files.length; i++) {
          this.droppedFiles.push(e.dataTransfer.files[i])
        }
      })
    }
  },
  methods: {
    isDragAndDrop () {
      const element = document.querySelector('form')
      return (('draggable' in element) || ('ondragstart' in element && 'drop' in element)) && 'FormData' in window && 'FileReader' in window
    }
  }
}
</script>

<style lang="scss">
html {
  background-color: #F7F7F9;
}
body {
  margin: 0;
  padding: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.classForCenter {
  margin: 0 auto;
}
.container {
  max-width: 1920px;
  height: auto;
}
.header {
  width: 100%;
  padding: 20px 0;
  box-sizing: border-box;
  border-bottom: 1px solid #DBDEE4;
  &__title {
    font-weight: 900;
    font-size: 2rem;
  }
}
.content {
  height: 67vh;
  & form {
    width: 60%;
    height: 82%;
    display: flex;
    justify-content: center;
    align-items: center;
    border: 1px solid #DBDEE4;
    border-radius: 10px;
    box-shadow: 15px 15px 70px rgb(61 76 106 / 10%);
  }
  &__clueTitle, &__sendFilesBtn {
    font-weight: 400;
    font-size: 1.2rem;
  }
  &__sendFilesBtn {
    width: 210px;
    padding: 15px 0;
    box-sizing: border-box;
    background: #EDEDEF;
    border: 1px solid #D8DCE5;
    border-radius: 7px;
    box-shadow: 15px 15px 70px rgb(61 76 106 / 30%);
    color: #8932FF;
    cursor: pointer;
    transition: .2s;
    &:hover {
      box-shadow: unset;
      background-color: #e2e2e8;
    }
  }
  &_forMarginTop {
    margin-top: 80px;
  }
  &__btnMarginTop {
    margin-top: 20px;
  }
}
.footer {
  display: block;
  font-weight: 400;
  font-size: 1.2rem;
  padding: 30px 0;
  box-sizing: border-box;
  border-top: 1px solid #DBDEE4;
  border-bottom: 1px solid #DBDEE4;
  &__listFiles {
    width: 400px;
    margin: auto;
    padding: 10px;
    border-bottom: 1px solid #ddd;
  }
  &__imgPreview {
    height: 80px;
  }
}
</style>
