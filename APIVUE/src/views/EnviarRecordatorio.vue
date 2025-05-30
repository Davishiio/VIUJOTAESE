<template>
  <Navbar />
  <div class="recordatorio-container">
    <h2>Enviar recordatorio</h2>

    <table>
      <thead>
        <tr>
          <th>Usuario</th>
          <th>Email</th>
          <th>Libro</th>
          <th>Fecha préstamo</th>
          <th>Fecha devolución</th>
          <th>Enviar Recordatorio</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="prestamo in prestamos" :key="prestamo.id_prestamo">
          <td>{{ prestamo.usuario_nombre }}</td>
          <td>{{ prestamo.usuario_email }}</td>
          <td>{{ prestamo.libro_titulo }}</td>
          <td>{{ prestamo.fecha_prestamo }}</td>
          <td>{{ prestamo.fecha_devolucion }}</td>
          <td style="text-align: center">
            <button @click="abrirModal(prestamo)">✈️</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Modal -->
    <div v-if="modalAbierto" class="modal-overlay">
      <div class="modal">
        <h3>Enviar recordatorio</h3>
        <p><strong>Para:</strong> {{ correo.to }}</p>
        <p><strong>Asunto:</strong> {{ correo.subject }}</p>
        <p><strong>Mensaje:</strong></p>
        <div v-html="correo.body" class="modal-body"></div>
        <div class="modal-buttons">
          <button @click="enviarCorreo">Enviar</button>
          <button @click="modalAbierto = false">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import Navbar from '@/components/Navbar.vue'
import { ref, onMounted } from 'vue'

const prestamos = ref([])
const modalAbierto = ref(false)
const correo = ref({
  to: '',
  name: 'BIBLIOTECA BIU YI',
  subject: '',
  body: '',
})
const token = localStorage.getItem('token')

onMounted(async () => {
  try {
    const res = await fetch('http://localhost:8000/PROJECT/prestamos', {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })
    prestamos.value = await res.json()
  } catch (err) {
    console.error('Error al obtener préstamos:', err)
  }
})

const abrirModal = (prestamo) => {
  correo.value.to = prestamo.usuario_email
  correo.value.subject = `Hola ${prestamo.usuario_nombre}`
  correo.value.body = `
    <b>Le recordamos que se acerca la fecha de entrega de <i>${prestamo.libro_titulo}</i></b>,<br>
    el cual adquirió el día <b>${prestamo.fecha_prestamo}</b> y tiene una fecha programada de devolución el día <b>${prestamo.fecha_devolucion}</b>.<br>
    <br>
    Anticiparse para evitar recargos.<br><br>
    Saludos,<br>
    BIBLIOTECA BIU YI
  `
  modalAbierto.value = true
}

const enviarCorreo = async () => {
  try {
    const res = await fetch('http://localhost:8000/PROJECT/emails/send', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${token}`,
      },
      body: JSON.stringify(correo.value),
    })

    if (!res.ok) throw new Error('Error al enviar el correo')

    alert('Correo enviado exitosamente')
    modalAbierto.value = false
  } catch (err) {
    console.error('Error al enviar el correo:', err)
    alert('No se pudo enviar el recordatorio')
  }
}
</script>

<style scoped>
.recordatorio-container {
  max-width: 1000px;
  margin: auto;
  padding: 2rem;
}
table {
  width: 100%;
  border-collapse: collapse;
}
th,
td {
  padding: 0.75rem;
  border: 1px solid #ccc;
  text-align: left;
}
button {
  padding: 0.4rem 0.8rem;
  cursor: pointer;
}
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  width: 400px;
  max-width: 90%;
}
.modal-buttons {
  margin-top: 1rem;
  display: flex;
  justify-content: space-between;
}
.modal-body {
  background: #f8f8f8;
  padding: 0.5rem;
  margin-top: 0.5rem;
  border-radius: 4px;
}
</style>
