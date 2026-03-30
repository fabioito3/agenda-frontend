<template>
  <q-page class="row justify-center items-center">
    <div style="width: 350px">
      <qcard class="q-pa-md">
        <q-card-section>
          <div class="text-h6">
            Login
          </div>
        </q-card-section>

        <q-card-section>
          <q-input v-model="email" type="email" label="Email" class="q-mb-md"/>

          <q-input v-model="password" type="password" label="Senha" class="q-mb-md"/>

          <q-btn color="primary" label="Fazer Login" @click="login" class="full-width"/>

          <div class="q-mt-md text-center">
            Não tem cadastro?
            <q-btn color="primary" label="Fazer cadastro" @click="$router.push('/register')" />
          </div>
        </q-card-section>
      </qcard>
    </div>
  </q-page>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import api from 'src/services/api'

const email = ref('')
const password = ref('')
const router = useRouter()

const login = async () =>{
  try{
    const res = await api.post('/login', {
      email: email.value,
      password: password.value
    })

    localStorage.setItem('token', res.data.token)
    localStorage.setItem('user', JSON.stringify(res.data.user))

    router.push('/')

  } catch (error){
    console.log(error)
  }
}
</script>