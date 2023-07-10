<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  
  <header>
    
    <div class="wrapper">
      <nav class="navbar navbar-expand-lg bg-body-tertiary">
        <div class="container-fluid">
          <ul class="navbar-nav">
            <li class="nav-item">
              <RouterLink class="nav-link" to="/">Lista de asistencias</RouterLink>
            </li>
            <li class="nav-item">
              <RouterLink class="nav-link" to="/empleados">Empleados</RouterLink>

            </li>
          </ul>

        </div>
      </nav>
    </div>
  </header>

  <RouterView />

  
</template>

<style scoped>

</style>
