<template>
  <div id="app">
    <h1>Statues list</h1>
    <table class="table table-striped">
      <thead class="thead-dark">
        <tr>
          <th scope="col">#</th>
          <th scope="col">Person</th>
          <th scope="col">Height</th>
          <th scope="col">Price</th>
          <th scope="col">Methods</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="statue in statues" v-bind:key="statue.id">
          <td>{{ statue.id }}</td>
          <td>{{ statue.person }}</td>
          <td>{{ statue.height }}</td>
          <td>{{ statue.price }}</td>
          <td>
            <button @click="deleteStatue(statue.id)" class="btn btn-danger">Delete</button>
            <button @click="editStatue(statue.id)" class="btn btn-warning">Edit</button>
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
            <button v-if="mod_new" @click="newStatue" :disabled="saving" class="btn btn-success">Add new</button>
            <button v-if="!mod_new" @click="saveStatue" :disabled="saving" class="btn btn-success">Save</button>
            <button v-if="!mod_new" @click="cancelEdit" :disabled="saving" class="btn btn-dark">Back</button>
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
        id: 0,
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
      console.log(this.statue)
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
      this.resetForm()
    },
    cancelEdit () {
      this.resetForm()
    },
    resetForm() {
      this.statue = {
        id: null,
        person: '',
        height: '',
        price: ''
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
.mt-0 {
  margin-top: 0 !important;
}

h1 {
  text-align: center;
}

</style>
