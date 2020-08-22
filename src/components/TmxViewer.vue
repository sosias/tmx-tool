<template>

  <table class="viewer table table-striped">
    <thead >
      <tr>
        <th scope="col">id</th>
        <th :key=key v-for="(item, key) in data.header" scope="col">{{item}}</th>
      </tr>
    </thead>
    <tbody>
      <template v-for="(row, key) in data.body">
          <tr :key=key sope="row">
            <td>{{key}}</td>
            <td v-for="(item, key) in row" :key=key>{{item}}</td>
          </tr>
      </template>
    </tbody>
  </table>
</template>

<script>
export default {
  name: 'TmxViewer',
  props: {
    data: Object
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>
