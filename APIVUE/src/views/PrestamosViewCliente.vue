<template>
  <Navbar />
  <!-- Aquí va el navbar -->
  <div class="prestamos-container">
    <h2>Mis Préstamos</h2>
    <table v-if="prestamos.length">
      <thead>
        <tr>
          <th>Ejemplar</th>
          <th>Fecha Préstamo</th>
          <th>Fecha Devolución</th>
          <th>Devuelto</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="p in prestamos" :key="p.id_prestamo">
          <td align="center">{{ p.libro_titulo }}</td>
          <td align="center">{{ p.fecha_prestamo }}</td>
          <td align="center">{{ p.fecha_devolucion || '-' }}</td>
          <td align="center">{{ p.devuelto ? 'Sí' : 'No' }}</td>
        </tr>
      </tbody>
    </table>
    <p v-else>No tienes préstamos.</p>
  </div>
</template>

<script setup>
import Navbar from '@/components/Navbar.vue'
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const prestamos = ref([])
const error = ref('')
const router = useRouter()

onMounted(async () => {
  try {
    const token = localStorage.getItem('token')
    const res = await fetch('http://localhost:8000/PROJECT/prestamos', {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })
    if (res.status === 401) {
      // Token inválido o expirado
      router.push('/login')
      return
    }
    const data = await res.json()
    prestamos.value = data
  } catch (e) {
    error.value = 'No se pudo cargar los préstamos'
    console.error(e)
  }
})
</script>

<style scoped>
.prestamos-container {
  max-width: 800px;
  margin: 2rem auto;
}
table {
  width: 100%;
  border-collapse: collapse;
}
th,
td {
  border: 1px solid #ddd;
  padding: 0.5rem;
}
th {
  background: #f2f2f2;
}
</style>
