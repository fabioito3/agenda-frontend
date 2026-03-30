<template>
  <q-page class="row justify-center items-center">
    <div style="width: 350px">
      <q-card class="my-card">
        <div class="text-h6">
          Cadastro
        </div>

        <q-card-section>
          <q-input v-model="name" label="Nome" />

          <q-input v-model="email" type="email" label="Email" class="q-mb-md"/>

          <q-input v-model="password" type="password" label="Senha" class="q-mb-md"/>

          <q-btn color="primary" label="Cadastrar" @click="register" 
          class="full-width"/>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<script setup>
import {ref} from 'vue'
import {useRouter} from 'vue-router'
import api from 'src/services/api'

const name = ref('')
const email = ref('')
const password = ref('')
const router = useRouter()

const register = async () => {
  try{
    await api.post('/register', {
      name: name.value,
      email: email.value,
      password: password.value
    })

    router.push('/login')
  } catch (error){
    console.log(error)
  }
}
</script>