<template>
  <div id="app">
    <div class="container">
      <h1 class="title">Calculadora</h1>
		<div class="form-group">
			<label for="num1">Primer número:</label>
			<input v-model="num1" v-on:input="getBase()" type="text" class="form-control" id="num1" placeholder="Ingrese el primer número">
		</div>
		<div class="form-group">
			<label for="num2">Segundo número:</label>
			<input v-model="num2" v-on:input="getBase()" type="text" class="form-control" id="num2" placeholder="Ingrese el segundo número">
		</div>
      <br>
      <div class="form-group">
			<label for="base">Base:</label>
			<input v-model="base" type="text" class="form-control" id="base">
		</div>

      <div class="buttons">
      <button type="button" @click="addition()" class="btn btn-primary">Sumar</button>
		<button type="button" @click="subtraction()" class="btn btn-primary">Restar</button>
		<button type="button" @click="multiplication()"  class="btn btn-primary">Multiplicar</button>
		<button type="button" @click="division()" class="btn btn-primary">Dividir</button>
      </div>
    <br>
		<div class="form-group">
			<label for="resultado">Resultado:</label>
			<input v-model="result" type="text" class="form-control" id="resultado" readonly>
		</div>
        <h3 class="title"> ULTIMOS RESULTADOS: </h3>
        <button class="btn btn-danger" @click="borrarHistorial"> Borrar historial de resultados</button>
    <div>
      <table class="table">
  <thead>
    <tr>
      <th scope="col">Operacion</th>
      <th scope="col">Base</th>
      <th scope="col">Numero 1</th>
      <th scope="col">Numero 2</th>
      <th scope="col">Resultado</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="item in lastResults">
      <th scope="row">{{item.operation}}</th>
      <td>{{ item.base }}</td>
      <td>{{ item.num1 }}</td>
      <td>{{ item.num2 }}</td>
      <td>{{ item.result }}</td>
    </tr>
  </tbody>
</table>
    </div>
	</div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: 'App',
  data: function () {
    return {
      base: 0,
      num1: '0',
      num2: '0',
      result: '0',
      lastResults: localStorage.getItem('lastResults') ? JSON.parse(localStorage.getItem('lastResults')) : []
    }
  },
  methods: {
    borrarHistorial(){
      localStorage.removeItem('lastResults')
      this.lastResults = []
    },
    async getBase() {
      if (this.num1 === '' || this.num2 === '') {
        this.base = '0'
        this.result = ''
        return
      }
      if ((this.num1.split('-').length - 1) % 2 !== 0 || (this.num2.split('-').length - 1) % 2 !== 0) {
        this.base = 'Número mal escrito'
        this.result = 'Número mal escrito'
        return
      }
      await axios.post('https://calculator-api-ahcr.onrender.com/base/', {
            "num": this.num1,
            "num2": this.num2
          },
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }).then((response) => {
        this.base = response.data.base
      })
    },
    async addition() {
      this.lastResults = localStorage.getItem('lastResults') ? JSON.parse(localStorage.getItem('lastResults')) : []
      await axios.post('https://calculator-api-ahcr.onrender.com/addition/', {
            num1: this.num1,
            num2: this.num2,
            base: this.base
          },
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
      ).then((response) => {
        this.result = response.data.result
        this.lastResults.push({
          num1: this.num1,
          num2: this.num2,
          base: this.base,
          result: this.result,
          operation: 'Suma'
        })
        localStorage.setItem('lastResults', JSON.stringify(this.lastResults))

      })
    },
    async subtraction() {
      this.lastResults = localStorage.getItem('lastResults') ? JSON.parse(localStorage.getItem('lastResults')) : []
      await axios.post('https://calculator-api-ahcr.onrender.com/subtraction/', {
            num1: this.num1,
            num2: this.num2,
            base: this.base
          },
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
      ).then((response) => {
        this.result = response.data.result
      })
      this.lastResults.push({
          num1: this.num1,
          num2: this.num2,
          base: this.base,
          result: this.result,
          operation: 'Resta',
        })
      localStorage.setItem('lastResults', JSON.stringify(this.lastResults))
      console.log(this.lastResults)
    },
    async multiplication() {
      this.lastResults = localStorage.getItem('lastResults') ? JSON.parse(localStorage.getItem('lastResults')) : []
      await axios.post('https://calculator-api-ahcr.onrender.com/multiplication/', {
            num1: this.num1,
            num2: this.num2,
            base: this.base
          },
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
      ).then((response) => {
        this.result = response.data.result
        this.lastResults.push({
          num1: this.num1,
          num2: this.num2,
          base: this.base,
          result: this.result,
          operation: 'Multiplicacion'
        })
      })
      localStorage.setItem('lastResults', JSON.stringify(this.lastResults))
      console.log(this.lastResults)
    },
    async division() {
      this.lastResults = localStorage.getItem('lastResults') ? JSON.parse(localStorage.getItem('lastResults')) : []
      await axios.post('https://calculator-api-ahcr.onrender.com/division/', {
            num1: this.num1,
            num2: this.num2,
            base: this.base
          },
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
      ).then((response) => {
        this.result = response.data.result
        this.lastResults.push({
          num1: this.num1,
          num2: this.num2,
          base: this.base,
          result: this.result,
          operation: 'División'
        })
      })
      localStorage.setItem('lastResults', JSON.stringify(this.lastResults))
    },
  }
}
</script>

<style scoped>
.buttons {
  display: flex;
  justify-content: space-between;
  padding: 25px;
}

.title{
  margin: 30px;
  text-align: center;
}
</style>
