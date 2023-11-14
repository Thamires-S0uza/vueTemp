<template>
  <div class="listar">
    <div class="cadastrar">
      <label for="dataHora">Data E Hora Limite: </label>
      <input id="dataHora" type="datetime-local" v-model="dataHora" /><br />
      <label for="medida">Nova Temperatura: </label>
      <input id="medida" type="float" v-model="medida" /><br />
      <button @click="cadastrar">Cadastrar</button>
    </div>
    <table>
      <thead>
        <td>Id</td>
        <td>Data/Hora</td>
        <td>Medida</td>
        <td>Sensação</td>
      </thead>
      <tbody>
        <tr v-for="temperatura in temperaturas" :key="temperatura.id">
          <td>{{ temperatura.id }}</td>
          <td>{{ temperatura.dataHora }}</td>
          <td>{{ temperatura.medida }}</td>
          <td v-if="26 < temperatura.medida">Quente!!</td>
          <td v-else>Ok</td>
        </tr>
      </tbody>
    </table>

    <p>{{ msg }}</p>
    <br />

    <label for="dataHora2">Data/Hora: </label>
    <input id="dataHora2" type="datetime-local" v-model="dataHora2" /><br />
    <label for="medida2">Medida: </label>
    <input id="medida2" type="float" v-model="medida2" /><br />
    <button @click="buscar">Buscar</button><br />
  </div>
</template>

<style>
p {
  color: white;
}
label {
  color: white;
}
table,
th,
td {
  border: 1px solid white;
  margin-top: 25px;
}
</style>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import axios from 'axios'
const dataHora = ref()
const medida = ref()
const dataHora2 = ref()
const medida2 = ref()
const msg = ref()

const temperaturas = ref()

async function atualizar() {
  temperaturas.value = (
    await axios.get('https://8080-mineda-springboot3app-fbjzox4p8h8.ws-us106.gitpod.io/temperatura')
  ).data
}

async function cadastrar() {
  await axios.post(
    'https://8080-mineda-springboot3app-fbjzox4p8h8.ws-us106.gitpod.io/temperatura',
    {
      dataHora: dataHora.value,
      medida: medida.value
    }
  )
  dataHora.value = null
  medida.value = null
  atualizar()
}

async function buscar() {
  axios
    .get(
      `https://8080-mineda-springboot3app-fbjzox4p8h8.ws-us106.gitpod.io/temperatura/${dataHora2.value}/${medida2.value}`
    )
    .then(function (response) {
      if (response.data < 2) {
        msg.value = 'Nenhum registro foi encontrado para os critérios fornecidos'
        temperaturas.value = []
      } else {
        msg.value = ''
        temperaturas.value = response.data
      }
    })
    .catch(function (error) {
      console.log(error)
    })
}

onMounted(() => {
  atualizar()
})
</script>
