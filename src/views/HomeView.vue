<template>
  <div class="container">
    <h1>Lista de asistencias</h1>

    <div class="accordion" id="accordionExample">

      <div class="accordion-item">
        <h2 class="accordion-header">
          <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#collapseOne" aria-expanded="true" aria-controls="collapseOne">
            Registrar asistencia
          </button>
        </h2>
        <div id="collapseOne" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <div class="accordion-body">
            <form @submit.prevent="postAsistencia">

              <div class="mt-2">
                <label for="nombre" class="form-label">Nombre del empleado</label>
                <select class="form-select" size="4" aria-label="Default select example" id="nombre" v-model="asistenciaForm.id_empleado">
                  <option v-for="empleado in empleados.data" :key="empleado.id" :value="empleado.id">{{ empleado.name }}</option>
                </select>
              </div>

              <div class="mt-2">
                <label for="fecha" class="form-label">Fecha</label>
                <input type="date" class="form-control" id="fecha" v-model="asistenciaForm.fecha">
              </div>

              <div class="mt-2">
                <label for="punch_in" class="form-label">Punch In</label>
                <input type="time" class="form-control" id="punch_in" v-model="asistenciaForm.punch_in">
              </div>
              
              <div class="mt-2">
                <label for="punch_out" class="form-label">Punch Out</label>
                <input type="time" class="form-control" id="punch_out" v-model="asistenciaForm.punch_out">
              </div>
              <button type="submit" class="btn btn-primary mt-3">Añadir nuevo registro de asistencia</button>

            </form>
          </div>
        </div>
      </div>
      <div class="accordion-item">
        <h2 class="accordion-header">
          <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseTwo" aria-expanded="false" aria-controls="collapseTwo">
            Registro de asistencias con hoja de Excel
          </button>
        </h2>
        <div id="collapseTwo" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <div class="accordion-body">
            <form @submit.prevent="uploadFile">

              <div class="mt-2">
                <label for="formFile" class="form-label">Documento de Excel</label>
                <input class="form-control" type="file" id="formFile" @change="handleFileUpload">
              </div>
              <button type="submit" class="btn btn-primary mt-3">Cargar asistencias</button>

            </form>
          </div>
        </div>
      </div>
      

    </div>

    <table class="table table-hover">
      <thead>
        <tr>
          <th scope="col">Nombre</th>
          <th scope="col">Fecha</th>
          <th scope="col">Punch In</th>
          <th scope="col">Punch Out</th>
          <th scope="col">Acciones disponibles</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="asistencia in asistencias.data" :key="asistencia.id">
          <td>{{ asistencia.id_empleado_empleado.name }}</td>
          <td>{{ asistencia.fecha }}</td>
          <td>{{ asistencia.punch_in }}</td>
          <td>{{ asistencia.punch_out }}</td>
          <td>
            <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal" @click="selectRegistro(asistencia)"> modificar </button>
            <button class="btn btn-danger mx-2" @click="deleteAsistencia(asistencia.id)">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Modal -->
    <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Editar registro de {{ asistenciaInfo.id_empleado_empleado?.name }}</h1>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="updateAsistencia">
              
              <div class="mt-2">
                <label for="fecha" class="form-label">Fecha</label>
                <input type="date" class="form-control" id="fecha" v-model="asistenciaInfo.fecha">
              </div>

              <div class="mt-2">
                <label for="punch_in" class="form-label">Punch In</label>
                <input type="time" class="form-control" id="punch_in" v-model="asistenciaInfo.punch_in">
              </div>

              <div class="mt-2">
                <label for="punch_out" class="form-label">Punch Out</label>
                <input type="time" class="form-control" id="punch_out" v-model="asistenciaInfo.punch_out">
              </div>
              
            </form>
          </div>
          <span>{{ aviso }}</span>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" @click="()=>{aviso = ''}">Cerrar / Cancelar</button>
            <button type="button" class="btn btn-primary" @click="updateAsistencia(asistenciaInfo.id)">guardar cambios</button>
          </div>
        </div>
      </div>
    </div>

    
    



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
      asistencias: {},
      asistenciaForm: {},
      asistenciaInfo:{},
      archivo:[],
      aviso:''
    }
  },

  mounted(){
    this.getEmpleados()
    this.getAsistencias()

  },

  methods: {
    
    async getEmpleados() {
      try {
        const response = await axios.get(`${this.baseUrl}/empleados`)
        this.empleados = response.data
      } catch (error) {
        console.log(error);
      }
    },

    async getAsistencias() {
      try {
        const response = await axios.get(`${this.baseUrl}/asistencias`)
        this.asistencias = response.data
      } catch (error) {
        console.log(error);
      }
    },

    async postAsistencia() {
      try {
        const response = await axios.post(`${this.baseUrl}/asistencias`, this.asistenciaForm)
        //console.log(this.asistenciaForm);
        this.getAsistencias()
      } catch (error) {
        console.log(error);
      }
    },
    selectRegistro(registro){
      this.asistenciaInfo = {...registro}
    },

    

    async updateAsistencia() {
      try {
        console.log(this.asistenciaInfo);
        const response = await axios.put(`${this.baseUrl}/asistencias/${this.asistenciaInfo.id}`, this.asistenciaInfo)
        this.aviso = 'actualizado correctamente'
        this.getAsistencias()
      } catch (error) {
        console.log(error);
      }
    },

    async deleteAsistencia(id){
      try {
        const response = await axios.delete(`${this.baseUrl}/asistencias/${id}`)
        console.log(response.data);
        this.getAsistencias()
      } catch (error) {
        if (error.response.data.error){
          console.log(error.response.data.error);
        } else {
          console.log(error);
        }
      }
    },

    handleFileUpload(event) {
      this.archivo = event.target.files[0];
      
      console.log('Archivo seleccionado:', this.archivo);
    },
    async uploadFile() {
      try {
        const formulario = new FormData()
        formulario.append('hojaExcel',this.archivo)

        const response = await axios.post(`${this.baseUrl}/cargar-excel`, formulario)
        console.log(response.data);
        this.getAsistencias()
      } catch (error) {
        console.log(error);
      }
    },


  }
  
  

}
</script>
