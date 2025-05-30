<template>
  <Navbar />
  <div class="prestamos-container">
    <h2>Estado de préstamos</h2>
    <table v-if="prestamos.length">
      <thead>
        <tr>
          <th>ID Préstamo</th>
          <th>Ejemplar</th>
          <th>ID Usuario</th>
          <th>Usuario</th>
          <th>Fecha Préstamo</th>
          <th>Fecha Devolución</th>
          <th>Devuelto</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="p in prestamos" :key="p.id_prestamo">
          <td align="center">{{ p.id_prestamo }}</td>
          <td align="center">{{ p.libro_titulo }}</td>
          <td align="center">{{ p.id_usuario }}</td>
          <td align="center">{{ p.usuario_nombre }}</td>

          <!-- Campos editables -->
          <td align="center">
            <input v-if="editando === p.id_prestamo" v-model="p.fecha_prestamo" type="date" />
            <span v-else>{{ p.fecha_prestamo }}</span>
          </td>
          <td align="center">
            <input v-if="editando === p.id_prestamo" v-model="p.fecha_devolucion" type="date" />
            <span v-else>{{ p.fecha_devolucion || '-' }}</span>
          </td>
          <td align="center">
            <select v-if="editando === p.id_prestamo" v-model="p.devuelto">
              <option :value="true">Sí</option>
              <option :value="false">No</option>
            </select>
            <span v-else>{{ p.devuelto ? 'Sí' : 'No' }}</span>
          </td>

          <!-- Acciones -->
          <td align="center">
            <button v-if="editando === p.id_prestamo" @click="guardarCambios(p)">✅</button>
            <button v-else @click="editarFila(p.id_prestamo)">✏️</button>
          </td>
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
const editando = ref(null)

const editarFila = (id) => {
  editando.value = id
}

const guardarCambios = async (p) => {
  try {
    const token = localStorage.getItem('token')
    const res = await fetch(`http://localhost:8000/PROJECT/prestamos/${p.id_prestamo}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${token}`,
      },
      body: JSON.stringify({
        id_prestamo: p.id_prestamo,
        id_usuario: p.id_usuario,
        id_ejemplar: p.id_ejemplar,
        fecha_prestamo: p.fecha_prestamo,
        fecha_devolucion: p.fecha_devolucion,
        devuelto: p.devuelto,
      }),
    })

    if (!res.ok) {
      throw new Error('Error al guardar los cambios')
    }

    editando.value = null
  } catch (err) {
    console.error(err)
    alert('No se pudo guardar los cambios')
  }
}

onMounted(async () => {
  try {
    const token = localStorage.getItem('token')
    const res = await fetch('http://localhost:8000/PROJECT/prestamos', {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })
    if (res.status === 401) {
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
  max-width: 1000px;
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
input,
select {
  width: 100%;
  padding: 0.25rem;
}
button {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 1.2rem;
}
</style>
