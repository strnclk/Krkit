<template>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&amp;display=swap" rel="stylesheet">
  <div class="login-container">
    <div class="login-card">
      <img src="https://r.resimlink.com/FBGpL.png" alt="Logo" class="logo" />
      <h2 class="login-title">Giriş Yap</h2>
      <form @submit.prevent="login">
        <q-input dense outlined v-model="username" label="Kullanıcı Adı" class="input-field" />
        <q-input dense outlined type="password" v-model="password" label="Şifre" class="input-field" />
        <q-btn unelevated color="primary" type="submit" class="login-button">Giriş Yap</q-btn>
      </form>
      <p class="register-text">Hesabınız yok ise yöneticinize başvurun</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { useQuasar } from 'quasar';
import axios from 'axios';
import { ClientRequest } from 'node:http';

const username = ref('');
const password = ref('');
const router = useRouter();
const $q = useQuasar();

const login = async () => {
  if (!username.value || !password.value) {
    $q.notify({
      message: 'Lütfen kullanıcı adı ve şifrenizi girin!',
      color: 'orange',
      position: 'top',
      timeout: 2500,
    });
    return;
  }

  try {
    const response = await axios.post('https://testapi.sitrancelik.com/api/auth/login', {
      username: username.value,
      password: password.value,
    });

    if (response.data.token) {
      localStorage.setItem('token', response.data.token);

      $q.notify({
        message: 'Giriş başarılı, anasayfaya yönlendiriliyorsunuz...',
        color: 'green',
        position: 'top',
        timeout: 2000,
      });

      setTimeout(() => {
        router.push('/toDoList');
      }, 2000);
    } else {
      throw new Error('Beklenmedik bir hata oluştu.');
    }
  } catch (error) {
    console.error('Giriş başarısız', error);

    let errorMessage = 'Giriş başarısız, lütfen tekrar deneyiniz.';

    if (error.response && error.response.data && error.response.data.message) {
      errorMessage = error.response.data.message;
    }

    $q.notify({
      message: errorMessage,
      color: 'red',
      position: 'top',
      timeout: 3000,
    });
  }
};
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: linear-gradient(135deg, #adefff, #ffd5ea);
}

.login-card {
  background: white;
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  text-align: center;
  width: 350px;
}

.logo {
  width: 280px;

}

.login-title {
  font-family: 'Poppins', sans-serif;
  font-size: 1.8rem;
  font-weight: 600;
  color: #333;
}

.input-field {
  margin-bottom: 1rem;
}

.login-button {
  width: 100%;
  padding: 10px;
  border-radius: 6px;
  
}

.register-text {
  margin-top: 1rem;
  font-size: 0.7rem;
}
</style>
