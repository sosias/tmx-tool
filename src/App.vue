<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>
      TMX tool
      <small class="text-muted">really really basic TMX to CSV exporter</small>
    </h1>

    <div class="custom-file">
      <input type="file" class="custom-file-input" id="customFile" @change="onFileChange" />
      <label class="custom-file-label" for="customFile">{{fileSelectText}}</label>
    </div>

    <ExportCSVButton :data=preparedToCSVData :fileName=fileSelectText />

    <tmxViewer v-if="preparedToCSVData" :data=preparedToCSVData />
  </div>
</template>

<script>
import TmxViewer from './components/TmxViewer.vue'
import ExportCSVButton from './components/ExportCSVButton.vue'

export default {
  name: 'App',
  components: {
    TmxViewer,
    ExportCSVButton
  },
  data() {
    return {
      readXml: null,
      url: null,
      data: null,
      preparedToCSVData: null,
      fileSelectText: "Choose a file"
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
  padding: 1.5em;
}
</style>
