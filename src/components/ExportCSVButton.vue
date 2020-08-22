<template>
  <div>
    <button class="btn btn-warning" @click="generateCSVFile" :disabled=isDisabled>Save CSV</button>
    <a style="display: none;" href="" download="data.csv" ref="link"></a>
  </div>
</template>

<script>
export default {
  name: 'ExportCSVButton',
  props: {
    data: Object,
    fileName: String
  },
  computed: {
  isDisabled() {
    return this.data == null
    }
  },
  methods: {
    generateCSVFile(){
      let preparedArray = [this.data.header, ...this.data.body]
      let csvSeparator = "\t"

      let csvContent = preparedArray.map(e => e.join(csvSeparator)).join("\n");

      preparedArray = "SEP="+csvSeparator+" "+preparedArray

      let blob = new Blob([csvContent], {type: "text/csv;charset=UTF-8,%ef%bb%bf"});
      this.$refs.link.href = window.URL.createObjectURL(blob);
      this.$refs.link.download = this.changeExtension(this.fileName, ".csv")
      //window.open(href);

      this.$refs.link.click()
    },
    changeExtension(file, extension){
      return file + extension
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>
