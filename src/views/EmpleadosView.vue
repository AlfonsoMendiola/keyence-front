<template>
  <div class="container">
    <h2>Empleados</h2>

    <form @submit.prevent="postEmpleado">
      <div class="mb-4">
        <label for="name" class="form-label">Nombre del empleado</label>
        <input type="text" class="form-control mb-1" id="name" required v-model="empleadoForm.name">
        <button type="submit" class="btn btn-primary">Añadir nuevo empleado</button>
      </div>
    </form>

    <hr>
    
    <table class="table table-hover">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Nombre</th>
          <th scope="col">Acciones disponibles</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="empleado in empleados.data" :key="empleado.id">
          <th>{{ empleado.id }}</th>
          <td>{{ empleado.name }}</td>
          <td><button class="btn btn-danger" @click="deleteEmpleado(empleado.id)">Eliminar</button></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'App',
  data() {
    return{
      baseUrl: import.meta.env.VITE_API_URL,
      empleados: {},
      empleadoForm: {}
    }
  },

  mounted(){
    this.getEmpleados()
  },

  methods: {
    
    async getEmpleados() {
      try {
        const response = await axios.get(`${this.baseUrl}/empleados`)
        this.empleados = response.data
        this.empleados.data.reverse()
      } catch (error) {
        console.log(error);
      }
    },
    async postEmpleado() {
      try {
        const response = await axios.post(`${this.baseUrl}/empleados`, this.empleadoForm)
        this.empleadoForm.name = ''
        this.getEmpleados()
      } catch (error) {
        console.log(error);
      }
    },

    async deleteEmpleado(id){
      try {
        const response = await axios.delete(`${this.baseUrl}/empleados/${id}`)
        console.log(response.data);
        this.getEmpleados()
      } catch (error) {
        if (error.response.data.error){
          console.log(error.response.data.error);
        } else {
          console.log(error);
        }
      }
    }
  }
  
  

}
</script>

