<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-card-title>Bienvenido a la página principal</v-card-title>
          <v-card-text v-if="user">
            <p>Nombre: {{ user.name }}</p>
            <p>Email: {{ user.email }}</p>
            <!-- Mostrar más datos del usuario según sea necesario -->
          </v-card-text>
          <v-card-text v-else>
            <p>Cargando información del usuario...</p>
          </v-card-text>
          <v-card-actions>
            <v-btn color="error" @click="logout">Cerrar Sesión</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
  import { onMounted, ref } from 'vue';
  import { useRouter } from 'vue-router';

  const router = useRouter();
  const user = ref(null);

  const checkAuthAndFetchProfile = async () => {
    try {
      // Leer cookie auth_token (asumiendo que está accesible)
      const authToken = document.cookie
        .split('; ')
        .find(row => row.startsWith('auth_token='))
        ?.split('=')[1];

      if (!authToken) {
        throw new Error('No hay token de autenticación');
      }

      // Verificar si el token en localStorage es diferente al de la cookie
      const storedToken = localStorage.getItem('auth_token');
      if (!storedToken || storedToken !== authToken) {
        localStorage.setItem('auth_token', authToken);
      }

      // Verificar si ya tenemos datos del usuario en localStorage
      const storedUser = localStorage.getItem('user');
      if (storedUser) {
        user.value = JSON.parse(storedUser);
      }

      // Obtener datos del perfil del usuario
      const response = await fetch('https://localhost:3000/usuario/profile', {
        method: 'GET',
        headers: {
          'Authorization': `Bearer ${authToken}`,
          'Content-Type': 'application/json',
        },
      });

      if (!response.ok) throw new Error('Error al obtener el perfil');

      const userData = await response.json();
      user.value = userData;
      localStorage.setItem('user', JSON.stringify(userData));
    } catch (error) {
      console.error('Error de autenticación:', error);
      router.push('/login');
    }
  };

  const logout = () => {
    localStorage.removeItem('auth_token');
    localStorage.removeItem('user');
    document.cookie = 'auth_token=; Max-Age=0; path=/;';
    router.push('/login');
  };

  onMounted(() => {
    checkAuthAndFetchProfile();
  });
</script>
