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
      <button @click="showCreateUserForm" style="display: block; margin-top: 20px;">Agregar</button>

      <ul>
        <li v-for="(user, index) in usuarios" :key="user.email">
          {{ user.name }} - {{ user.email }}
          <button @click="deleteUser(index)">Eliminar</button>
          <button @click="startEditing(user, index)">Editar</button>
        </li>
      </ul>

      <button @click="cerrarSesion">Cerrar sesión</button>
    </div>

    <form v-if="isEditing" @submit.prevent="updateUser">
      <div>
        <label for="editName">Nombre:</label>
        <input type="text" id="editName" v-model="editUserForm.name" required />
      </div>
      <div>
        <label for="editEmail">Email:</label>
        <input type="email" id="editEmail" v-model="editUserForm.email" required />
      </div>
      <div>
        <label for="editPassword">Contraseña:</label>
        <input type="password" id="editPassword" v-model="editUserForm.password" required />
      </div>

      <button type="submit">Guardar Cambios</button>
      <button type="button" @click="cancelEditing">Cancelar</button>
    </form>

    <form v-if="isCreating" @submit.prevent="createUser">
      <div>
        <label for="newName">Nombre:</label>
        <input type="text" id="newName" v-model="newUserForm.name" required />
      </div>
      <div>
        <label for="newEmail">Email:</label>
        <input type="email" id="newEmail" v-model="newUserForm.email" required />
      </div>
      <div>
        <label for="newPassword">Contraseña:</label>
        <input type="password" id="newPassword" v-model="newUserForm.password" required />
      </div>

      <button type="submit">Confirmar</button>
      <button type="button" @click="cancelCreating">Cancelar</button>
    </form>
  </main>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import usuariosData from './data.json'; 

const email = ref('');
const password = ref('');
const usuarioActual = ref(null);
const usuarios = ref([...usuariosData]); 
const isEditing = ref(false); 
const editUserForm = ref({ name: '', email: '', password: '' }); 
const isCreating = ref(false);
const newUserForm = ref({ name: '', email: '', password: '' }); 
let editIndex = ref(null); 

const startEditing = (user, index) => {
  isEditing.value = true;
  editIndex.value = index;
  editUserForm.value = { ...user };
};

const cancelEditing = () => {
  isEditing.value = false;
  editUserForm.value = { name: '', email: '', password: '' };
  editIndex.value = null;
};

const updateUser = () => {
  if (editIndex.value !== null) {
    usuarios.value[editIndex.value] = { ...editUserForm.value };
    cancelEditing();
  }
};

const deleteUser = (index) => {
  usuarios.value.splice(index, 1);
  if (usuarios.value.length === 0) {
    usuarios.value = [...usuariosData]; 
  }
};

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
  } else {
    console.log('Usuario actual:', usuarioActual.value); // Verifica si se asigna correctamente
  }
};

const showCreateUserForm = () => {
  isCreating.value = true;
  newUserForm.value = { name: '', email: '', password: '' };
};

const createUser = () => {
  const newUser = { ...newUserForm.value };
  usuarios.value.push(newUser);
  cancelCreating();
};

const cancelCreating = () => {
  isCreating.value = false;
  newUserForm.value = { name: '', email: '', password: '' };
};
onMounted(() => {
  const storedUser = sessionStorage.getItem('usuarioActual');
  if (storedUser) {
    usuarioActual.value = JSON.parse(storedUser);
  }
});
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
