<template>
  <div id="app">

    <div>
      <h1 style="max-width: 300px; margin-bottom: 4.5rem;">
        TMX tool
        <small class="text-muted">really really basic "Translation Memory eXchange" to CSV exporter</small>
        <a class="github-link" href="https://github.com/sosias/tmx-tool" title="github project tmx-tool"> 
          <svg height="30" class="octicon octicon-mark-github fill-gray-light d-inline" aria-label="github-logo" viewBox="0 0 16 16" version="1.1" width="30" role="img">
            <path fill-rule="evenodd" d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0016 8c0-4.42-3.58-8-8-8z"></path>
          </svg>
        </a>
      </h1>

      <div style="display: flex; justify-content: center; margin-bottom: 40px;">
        <div class="custom-file" style="max-width: 500px;transition: all 0.5s ease;">
          <input type="file" class="custom-file-input" id="customFile" @change="onFileChange" />
          <label class="custom-file-label" for="customFile">{{fileSelectText}}</label>
        </div>
        <ExportCSVButton v-if="preparedToCSVData" :data=preparedToCSVData :fileName=fileSelectText style="margin-left: 20px;" />
      </div>

      <tmxViewer v-if="preparedToCSVData" :data=preparedToCSVData />
    </div>

    <Footer />
  </div>
</template>

<script>
import TmxViewer from './components/TmxViewer.vue'
import ExportCSVButton from './components/ExportCSVButton.vue'
import Footer from './components/Footer.vue'

export default {
  name: 'App',
  components: {
    TmxViewer,
    ExportCSVButton,
    Footer
  },
  data() {
    return {
      readXml: null,
      url: null,
      data: null,
      preparedToCSVData: null,
      fileSelectText: "Choose a .tmx file"
    }
  },
  methods: {
    onFileChange(e) {
      const file = e.target.files[0];
      this.fileSelectText = e.target.files[0].name
      var reader = new FileReader();
      reader.onload = function(e) {
          let domElement = this.getXMLdomElement(e.target.result)
          this.data = this.parseData(domElement)
          this.preparedToCSVData = this.prepareCSVArray(this.data)
      }.bind(this)
      reader.readAsText(file);
    },
    getXMLdomElement(xmlFile){
      var parser = new DOMParser();
      var doc = parser.parseFromString(xmlFile, "application/xml");
      return doc;
    },
    parseData(domElement){
      let languages = {}
      var body = domElement.getElementsByTagName("body")[0]
      var tus = body.getElementsByTagName("tu")

      var translations = Array.from(tus).map((el)=>{
        return Array.from(el.getElementsByTagName("tuv")).map((tuvEl)=>{

          let language = tuvEl.getAttribute("xml:lang").toUpperCase()
          let segElements = tuvEl.getElementsByTagName("seg")
          let content = segElements.length ? segElements[0].innerHTML : ""
          
          //save the current language label and increase the language "encounter" counter
          languages[language]===undefined? languages[language]=0 : languages[language]++

          return {lang: language, content: content}
        })
      })

      let data = {
        languages: languages,
        translations: translations
      }

      return data
    },
    prepareCSVArray(data){
      let langs = Object.keys(data.languages)

      let body = data.translations.map((dataRow)=>{
        let newRow = new Array(langs.length)

        dataRow.map((dataItem)=>{
          // insert the translation content in the rigth position of the row (respective to the language)
          // if doesn't exist the language, don't save it
          let entryIndex = langs.indexOf(dataItem.lang)
          entryIndex > -1 ? newRow[langs.indexOf(dataItem.lang)] = dataItem.content : console.error("some content doesn't have a respective language "+dataItem.content)
        })

        return newRow
      })

      return {header: langs, body: body}
    }
  }
}
</script>

<style lang="scss">
  #app {
    height: 100%;
    padding: 1.5em;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .octicon{
    fill: #80868c;
    margin-left: 5px;
    transition: all ease-in 0.3s;
  }

  .github-link{
    :hover{
      fill: #ffc107;
    }
  }
</style>
