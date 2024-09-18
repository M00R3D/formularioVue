<template>
  <header>

  </header>

  <main>
    <form @submit.prevent="submitForm">
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
  </main>
</template>
<script setup>
    import { ref } from 'vue';
    import usuarios from './data.json'
    const email = ref('');
    const password = ref('');

  const submitForm = () => {
  let usuarioEncontrado = false; 
  usuarios.forEach(user => {
    if (user.email === email.value && user.password === password.value) 
    {
      console.log(`Bienvenido, ${user.name}`);
      usuarioEncontrado = true; 
    }
  });

  if (usuarioEncontrado==false) {
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
