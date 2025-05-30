<template>
  <div>
    <h1>Hola {{ nombre }}</h1>
    <input v-model="nombre" placeholder="Escribe tu nombre" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      nombre: 'David',
    }
  },
}
</script>

<style scoped>
h1 {
  color: #42b983;
}
</style>
