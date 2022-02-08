<template>
  <div id="app">

    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Person</th>
          <th>Height</th>
          <th>Price</th>
          <th>Methods</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="statue in statues" v-bind:key="statue.id">
          <td>{{ statue.id }}</td>
          <td>{{ statue.person }}</td>
          <td>{{ statue.height }}</td>
          <td>{{ statue.price }}</td>
          <td>
            <button @click="deleteStatue(statue.id)">Delete</button>
            <button @click="editStatue(statue.id)">Edit</button>
          </td>
        </tr>
        <tr>
          <td>
            <input type="hidden"  v-model="statue.id">
          </td>
          <td>
            <input type="text" placeholder="Statue name" v-model="statue.person">
          </td>
          <td>
            <input type="number" placeholder="Statue height" v-model="statue.height">
          </td>
          <td>
            <input type="number" placeholder="Statue price" v-model="statue.price">
          </td>
          <td>
            <button v-if="mod_new" @click="newStatue" :disabled="saving">Add new</button>
            <button v-if="!mod_new" @click="saveStatue" :disabled="saving">Save</button>
            <button v-if="!mod_new" @click="cancelEdit" :disabled="saving">Back</button>
          </td>
        </tr>
      </tbody>
    </table>
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
      statue: {
        id: null,
        person: '',
        height: '',
        price: ''
      },
      statues: []
    }
  },
  methods: {
    async loadData () {
     let Response = await fetch('http://127.0.0.1:8000/api/statues')
     let data = await Response.json()
     this.statues = data
    },
    async deleteStatue(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`, {
        method: 'DELETE'
      })
      console.log(Response)
      await this.loadData()
    },
    async newStatue() {
      this.saving='disabled'
     await fetch('http://127.0.0.1:8000/api/statues', {
       method: 'POST',
       headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
       },
       body: JSON.stringify(this.statue) 
     })
     await this.loadData()
     this.saving=false
     this.resetForm()
    },
    async saveStatue() {
      this.saving='disabled'
     await fetch(`http://127.0.0.1:8000/api/statues/${this.statue.id}`, {
       method: 'PATCH',
       headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
       },
       body: JSON.stringify(this.statue) 
     })
     await this.loadData()
     this.saving=false
     this.resetForm()
    },
    async editStatue(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`)
      let data = await Response.json()
      this.statue = {...data};
      this.mod_new = false
    },
    cancelEdit () {
      this.resetForm()
    },
    resetForm() {
      this.statue = {
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
