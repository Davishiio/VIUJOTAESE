<template>
  <div class="login-container">
    <h2>Iniciar sesión</h2>
    <input v-model="email" type="email" placeholder="Correo electrónico" />
    <input v-model="password" type="password" placeholder="Contraseña" />
    <button @click="login">Ingresar</button>

    <p v-if="error" style="color: red">{{ error }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const email = ref('')
const password = ref('')
const error = ref('')
const router = useRouter()

const login = async () => {
  error.value = ''

  const headers = new Headers()
  headers.append('Content-Type', 'application/json')

  const raw = JSON.stringify({
    email: email.value,
    password: password.value,
  })

  const requestOptions = {
    method: 'POST',
    headers,
    body: raw,
    redirect: 'follow',
  }

  try {
    const response = await fetch('http://localhost:8000/PROJECT/usuarios/login', requestOptions)

    if (!response.ok) {
      const errorData = await response.json()
      error.value = errorData.mensaje || 'Credenciales incorrectas'
      return
    }

    const result = await response.json()

    // Guarda el token y el rol en localStorage
    localStorage.setItem('token', result.token)
    localStorage.setItem('id_rol', result.user.role)
    localStorage.setItem('id_usuario', result.user.id)

    // Redirige según el rol
    if (result.user.role === 1) {
      router.push('/MenuGerente') // o donde quieras
    } else {
      router.push('/Menu')
    }
  } catch (err) {
    console.error('Error de red:', err)
    error.value = 'No se pudo conectar con el servidor'
  }
}
</script>

<style scoped>
.login-container {
  max-width: 400px;
  margin: 50px auto;
  display: flex;
  flex-direction: column;
  gap: 10px;
}
</style>
