<template>
  <div id="app">
    <div class="container classForCenter">
      <div class="header">
        <span class="header__title classForCenter">File uploader</span>
      </div>

      <div class="content content_forMarginTop">
        <form ref="placeToDrop" class="classForCenter">
          <div class="content__whileDrag" v-if="draggable">+</div>
          <span class="content__clueTitle" v-else>Перенесите файл сюда!</span>
        </form>

        <div
          @click="sendFiles()"
          class="content__sendFilesBtn content__btnMarginTop classForCenter"
          :class="{ content_btnDisable: droppedFiles.length === 0 }"
        >
          ОТПРАВИТЬ
        </div>
      </div>

      <div class="footer" :class="{ otherPadding: droppedFiles.length > 0 }">
        <span class="footer__title classForCenter" v-if="droppedFiles.length === 0">Здесь будут отображаться ваши загруженные файлы.</span>

        <div class="footer__preview" v-else>
          <div
            v-for="(file, key) in droppedFiles"
            :key="key"
            class="footer__listFiles"
          >
            <img
              class="footer__imgPreview"
              :ref="`preview${parseInt(key)}`"
              src="./assets/logo.png"
            />
            {{ file.name }}
          </div>
        </div>
      </div>
    </div>
    <div class="modalError" :class="{ modalError_modalVisible: wasError }">Повторно один и тот же файл добавлять нельзя</div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      wasError: false,
      draggable: false,
      droppedFiles: [],
      dragMoves: ['drag', 'drop', 'dragstart', 'dragend', 'dragover', 'dragenter', 'dragleave']
    }
  },
  computed: {
    isDragAndDrop () {
      const element = document.querySelector('form')
      return (('draggable' in element) || ('ondragstart' in element && 'drop' in element)) && 'FormData' in window && 'FileReader' in window
    }
  },
  mounted () {
    if (this.isDragAndDrop) {
      this.dragMoves.forEach((e) => {
        this.$refs.placeToDrop.addEventListener(e, (event) => {
          event.preventDefault()
          event.stopPropagation()

          if (e === 'dragover') this.draggable = true
          else if (e === 'dragleave' || e === 'drop') this.draggable = false
        })
      })

      this.$refs.placeToDrop.addEventListener('drop', (e) => {
        for (let i = 0; i < e.dataTransfer.files.length; i++) {
          if (this.droppedFiles.find((element) => element.name === e.dataTransfer.files[i].name)) {
            this.wasError = true
            setTimeout(() => {
              this.wasError = false
            }, 3000)
            return
          }
          this.droppedFiles.push(e.dataTransfer.files[i])
          this.displayPreview()
        }
      })
    }
  },
  methods: {
    displayPreview () {
      for (let i = 0; i < this.droppedFiles.length; i++) {
        if (/\.(jpe?g|png|gif)$/i.test(this.droppedFiles[i].name)) {
          const fileReader = new FileReader()

          fileReader.addEventListener('load', () => {
            this.$refs[`preview${parseInt(i)}`][0].src = fileReader.result
          })

          fileReader.readAsDataURL(this.droppedFiles[i])
        }
      }
    },
    sendFiles () {
      for (let i = 0; i < this.droppedFiles.length; i++) {
        if (/\.(jpe?g|png|gif)$/i.test(this.droppedFiles[i].name)) {
          console.log(this.$refs[`preview${parseInt(i)}`][0].src)
        }
      }
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
  overflow: hidden;
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
    transition: 3s;
    &:hover {
      box-shadow: unset;
      .content__clueTitle {
        color: #8932FF;
        font-size: 1.3rem;
      }
    }
  }
  &__whileDrag {
    font-size: 40px;
  }
  &__clueTitle {
    transition: 3s;
  }
  &__clueTitle, &__sendFilesBtn {
    font-weight: 400;
    font-size: 1.2rem;
  }
  &__sendFilesBtn {
    width: 210px;
    padding: 15px 0;
    box-sizing: border-box;
    background-color: #EDEDEF;
    border: 1px solid #D8DCE5;
    border-radius: 7px;
    box-shadow: 7px 7px 20px rgb(61 76 106 / 30%);
    color: #8932FF;
    cursor: pointer;
    transition: .2s;
    &:hover {
      box-shadow: unset;
      background-color: #e2e2e8;
    }
  }
  &_btnDisable {
    color: #8932ff59;
    box-shadow: unset;
    pointer-events: none;
    &:hover {
      background-color: #EDEDEF;
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
  &__preview {
    display: flex;
    justify-content: center;
    width: 100%;
    flex-wrap: wrap;
  }
  &__listFiles {
    width: auto;
    margin: 0 7px;
  }
  &__imgPreview {
    height: 60px;
    width: 80px;
  }
}
.otherPadding {
  padding: 8.5px 0;
}
.modalError {
  position: absolute;
  bottom: 18%;
  left: -450px;
  background: #F9FAFB;
  border: 1px solid #FF323E;
  padding: 15px;
  box-sizing: border-box;
  border-radius: 5px;
  font-weight: 400;
  font-size: 1rem;
  transition: .5s;
  &_modalVisible {
    left: 50px;
  }
}
</style>
