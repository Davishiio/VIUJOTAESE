<template>
  <Navbar />
  <div class="prestar-container">
    <h2>Prestar libro</h2>

    <div v-if="ejemplares.length">
      <label for="ejemplar">Selecciona un ejemplar:</label>
      <select v-model="ejemplarSeleccionado">
        <option disabled value="">-- Selecciona un libro --</option>
        <option v-for="e in ejemplares" :key="e.id_ejemplar" :value="e.id_ejemplar">
          {{ e.titulo }} (Serie: {{ e.numero_serie }})
        </option>
      </select>

      <button @click="prestarLibro" :disabled="!ejemplarSeleccionado">Prestar libro</button>
    </div>
    <p v-else>No hay libros disponibles.</p>
  </div>
</template>

<script setup>
import Navbar from '@/components/Navbar.vue'
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const ejemplares = ref([])
const ejemplarSeleccionado = ref('')
const router = useRouter()

onMounted(async () => {
  try {
    const token = localStorage.getItem('token')
    const res = await fetch('http://localhost:8000/PROJECT/ejemplares', {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })

    const data = await res.json()
    // Solo ejemplares disponibles
    ejemplares.value = data.filter((e) => e.disponible === 1)
  } catch (err) {
    console.error('Error al cargar ejemplares:', err)
  }
})

const prestarLibro = async () => {
  const confirmar = confirm('¿Estás seguro de que deseas prestar este libro?')
  if (!confirmar) return

  const token = localStorage.getItem('token')
  const id_usuario = parseInt(localStorage.getItem('id_usuario')) // 👈 Asegura número

  const hoy = new Date()
  const fecha_prestamo = hoy.toISOString().split('T')[0]
  const fecha_devolucion = new Date(hoy.setDate(hoy.getDate() + 10)).toISOString().split('T')[0]

  const body = {
    id_usuario,
    id_ejemplar: ejemplarSeleccionado.value,
    fecha_prestamo,
    fecha_devolucion,
    fecha_entregado: null,
    devuelto: 0,
  }
  console.log('Cuerpo de la solicitud:', body)
  try {
    const res = await fetch('http://localhost:8000/PROJECT/prestamos', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${token}`,
      },
      body: JSON.stringify(body),
    })

    if (!res.ok) {
      const errData = await res.json()
      console.error('Error al prestar:', errData)
      throw new Error('No se pudo registrar el préstamo')
    }

    alert('¡Libro prestado con éxito!')
    router.push('/Menu')
  } catch (err) {
    console.error('Error al prestar libro:', err)
    alert('Error al prestar el libro.')
  }
}
</script>

<style scoped>
.prestar-container {
  max-width: 600px;
  margin: 2rem auto;
  text-align: center;
}
select {
  padding: 0.5rem;
  margin: 1rem 0;
  width: 100%;
}
button {
  padding: 0.5rem 1rem;
  cursor: pointer;
}
</style>
