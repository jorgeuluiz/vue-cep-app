<template>
  <v-container>
    <v-text-field v-model="cep" label="Digite o CEP" filled ></v-text-field>
    <v-btn @click="buscarCep" color="green" >Buscar</v-btn>

    <div v-if="cepData">
      <v-card class="mt-3">
        <v-card-subtitle>Logradouro: {{ cepData.logradouro }}</v-card-subtitle>
        <v-card-subtitle>Bairro: {{ cepData.bairro }}</v-card-subtitle>
        <v-card-subtitle>Cidade: {{ cepData.localidade }} </v-card-subtitle>
        <v-card-subtitle>Estado: {{ cepData.uf }}</v-card-subtitle>
      </v-card>
    </div>
    <div v-if="error">
      <v-alert type="error" dismissible>{{ error }}</v-alert>
    </div>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      cep: '',
      cepData: null,
      error: null,
      cache: {}
    }
  },
  methods: {
    async buscarCep () {
      this.error = null

      if (!this.cep || this.cep.length !== 8) {
        this.error = 'Por favor, insira um CEP válido com 8 dígitos.'
        return
      }

      if (this.cache[this.cep]) {
        this.cepData = this.cache[this.cep]
        return
      }

      try {
        const response = await axios.get(`https://localhost:7227/api/Cep/${this.cep}`)
        console.log(response)
        if (response.data.erro) {
          this.error = 'CEP não encontrado.'
        } else {
          this.cepData = response.data
          this.cache[this.cep] = this.cepData
        }
      } catch (error) {
        this.error = 'Erro ao consultar o CEP.'
      }
    }
  }
}
</script>

<style scoped>
.cep-search {
  text-align: center;
  margin-top: 50px;
}

input {
  padding: 10px;
  margin: 10px;
}

.v-card {
  margin-top: 15px;
}

.v-btn.green {
  background-color: green;
  color: green;
}

.error {
  color: red;
  font-size: 14px;
}

h3 {
  margin-top: 20px;
}

.v-input__control .v-input__label {
  position: relative;
  top: -10px;
}

.v-text-field {
  border-bottom: none;
}
</style>
