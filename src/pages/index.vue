<template>
  <div v-if="isAuthenticated">
    <mainpage />
  </div>
  <div v-else>
    <login />
  </div>
</template>

<script setup>
  import { onMounted, ref } from 'vue';
  import login from '../pages/login.vue';
  import mainpage from '../pages/mainpage.vue';

  const isAuthenticated = ref(true);
  onMounted(() => {
    // Leer cookie auth_token (asumiendo que está accesible)
    const authToken = document.cookie
      .split('; ')
      .find(row => row.startsWith('auth_token='))
      ?.split('=')[1];
    if (!authToken) {
      isAuthenticated.value = false;

    }

    // Verificar si el token en localStorage es diferente al de la cookie


  });
</script>
