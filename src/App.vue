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
            <button @click="editPainting(painting.id)">Edit</button>
          </td>
        </tr>
        <tr id="editor">
          <td> <input type="hidden" v-model="painting.id"> </td>
          <td> <input type="text" v-model="painting.title"> </td>
          <td> <input type="number" v-model="painting.year"> </td>
          <td> <input type="checkbox" v-model="painting.on_display"> </td>
          <td> <button v-if="mod_new" @click="newPainting" :disabled="saving">Létrehoz</button> </td>
          <td> <button v-if="!mod_new" @click="savePainting" :disabled="saving">Save</button> </td>
          <td> <button v-if="!mod_new" @click="cancelEdit" :disabled="saving">Cancel</button> </td>
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
      mod_new: true,
      saving: false,
      painting: {
        id: null,
        title: '',
        year: '',
        on_display: false
      },
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
    },

    async newPainting() {
      this.saving = 'disabled'
      await fetch('http://127.0.0.1:8000/api/paintings', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json', 'Accept': 'application/json'
        },
        body: JSON.stringify(this.painting)
      })
      await this.loadData()
      this.saving=false
      this.resetForm()
    },
    
    async editPainting(id) {
      location.hash = '#editor'
      let Response = await fetch(`http://127.0.0.1:8000/api/paintings/${id}`)
      let data = await Response.json()
      this.painting = {...data}
      this.mod_new = false;
    },

    cancelEdit() {
      location.hash = ''
      this.resetForm()
    },

    async savePainting() {
      this.saving = 'disabled'
      await fetch(`http://127.0.0.1:8000/api/paintings/${this.painting.id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json', 'Accept': 'application/json'
        },
        body: JSON.stringify(this.painting)
      })
      await this.loadData()
      this.saving=false
      this.resetForm()
    },

    resetForm() {
      this.painting = {
        id: null,
        title: '',
        year: '',
        on_display: false
      }
      this.mod_new = true
    }

  },
  
  mounted() {
    this.loadData()
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
