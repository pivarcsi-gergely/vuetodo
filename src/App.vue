<template>
  <div id="app">
    <table>
      <thead>
        <tr>
          <th>Id</th>
          <th>Title</th>
          <th>Year</th>
          <th>On Display</th>
          <th>Operations</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="painting in paintings" v-bind:key="painting.id">
          <td>{{ painting.id }}</td>
          <td>{{ painting.title }}</td>
          <td>{{ painting.year }}</td>
          <td>{{ painting.on_display }}</td>
          <td>
            <button @click="deletePainting(painting.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
    <button @click="loadData">Adatok betöltése</button>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      paintings: []
    }
  },
  methods: {
    async loadData() {
    let Response = await fetch('http://127.0.0.1:8000/api/paintings')
    let data = await Response.json()
    this.paintings = data;
    },

    async deletePainting(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/paintings/${id}`, {
        method: 'DELETE'
      })
      console.log(Response)
      await this.loadData()
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
</style>
