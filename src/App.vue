<template>
  <header></header>

  <main>
    <form @submit.prevent="submitForm" v-if="!usuarioActual">
      <div>
        <label for="email">Email:</label>
        <input type="email" id="email" v-model="email" required />
      </div>

      <div>
        <label for="password">Contraseña:</label>
        <input type="password" id="password" v-model="password" required />
      </div>
      
      <button type="submit">Iniciar Sesión</button>
    </form>

    <div v-else>
      <h2>Bienvenido, {{ usuarioActual.name }}!</h2>
      <ul>
        <li v-for="(user, index) in usuarios" :key="user.email">
          {{ user.name }} - {{ user.email }} 
          <button type="submit" @click="deleteUser(index)">Eliminar</button>
        </li>
      </ul>
      <button @click="cerrarSesion">Cerrar sesión</button>
    </div>
  </main>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import usuariosData from './data.json'; 

const email = ref('');
const password = ref('');
const usuarioActual = ref(null);
const usuarios = ref([...usuariosData]); 

const deleteUser = (index) => {
  usuarios.value.splice(index, 1);
  if (usuarios.value.length === 0) {
    usuarios.value = [...usuariosData]; 
  }
};

onMounted(() => {
  const storedUser = sessionStorage.getItem('usuarioActual');
  if (storedUser) {
    usuarioActual.value = JSON.parse(storedUser);
  }
});

const cerrarSesion = () => {
  sessionStorage.removeItem('usuarioActual');
  usuarioActual.value = null;
};

const submitForm = () => {
  let usuarioEncontrado = null;

  usuarios.value.forEach(user => {
    if (user.email === email.value && user.password === password.value) {
      usuarioEncontrado = user;
      sessionStorage.setItem('usuarioActual', JSON.stringify(user));
      usuarioActual.value = user;
    }
  });

  if (!usuarioEncontrado) {
    console.log('Email o contraseña incorrectos');
  }
};
</script>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
