<template>

  <div class="Container">

    <div v-if="desaparecer">
      <p>Puntaje: {{ puntaje }} </p>
      <p>Intento: {{ intento }}</p>
    </div>

    <div v-if="desaparecer">
      <img v-if=!mostrar src="../img/negro.png" alt="No se pudo cargar">
      <img v-if=mostrar :src="imagenUno" alt="No se pudo cargar">

      <img v-if=!mostrar src="../img/negro.png" alt="No se pudo cargar">
      <img v-if=mostrar :src="imagenDos" alt="No se pudo cargar">

      <img v-if=!mostrar src="../img/negro.png" alt="No se pudo cargar">
      <img v-if=mostrar :src="imagenTres" alt="No se pudo cargar">
    </div>

    <div v-if="mostrarGanar" class="mostrarGanar">
      <p>Puntaje: {{ puntaje }}</p>
      <img id="imagenGanadora" src="../img/congratulations.gif" alt="">
      <p>Felicitaciones has ganado un premio de $10.000,00</p>
    </div>

    <div v-if="mensajePerdida" class="mensajePerdida">
      <p>Haz utilizado tus 5 intentos</p>
      <p>El juego ha terminado, intentalo nuevamente</p>
    </div>

    <div v-if="desaparecer">
      <p>{{ textoUno }}</p>
      <p>{{ textoDos }}</p>
      <p>{{ textoTres }}</p>
    </div>

    <button v-if="desaparecer" v-on:click="clickJugar()">JUGAR</button>
    <button v-if="!desaparecer" v-on:click="reinciar()">Nuevo Juego</button>
  </div>

</template>

<script>
export default {
  name: "CasinoPokemonImg",
  data() {
    return {
      imagenUno: null,
      textoUno: "xxxxxxxxxx",

      imagenDos: null,
      textoDos: "xxxxxxxxxx",

      imagenTres: null,
      textoTres: "xxxxxxxxxx",

      mostrar: null,
      desaparecer: true,
      mensajePerdida: false,
      mostrarGanar: false,

      puntaje: 0,
      intento: 0
    }
  },
  props: {
    idPokemon: {
      type: Number,
      required: true
    }
  },
  methods: {
    clickJugar() {
      const vectorRes = this.generarVector()
      this.generarImagenes(vectorRes)
      this.mostrarResultado(vectorRes)
      this.intentoPuntaje(vectorRes)
    },
    intentoPuntaje(vectorRes) {
      if (this.intento !== 5) {
        this.intento += 1
        if (vectorRes[0] === vectorRes[1] && vectorRes[0] && vectorRes[2] && vectorRes[1] === vectorRes[2]) {
          this.puntaje += 5
          if (this.puntaje >= 10) {
            this.desaparecer = false
            this.mostrarGanar = true
          }
        } else if (vectorRes[0] === vectorRes[1] || vectorRes[0] === vectorRes[2] || vectorRes[1] === vectorRes[2]) {
          this.puntaje += 2
          if (this.puntaje >= 10) {
            this.desaparecer = false
            this.mostrarGanar = true
          }
        }
      } else {
        this.desaparecer = false
        this.mensajePerdida = true
      }
    },
    async mostrarResultado(vectorRes) {
      this.mostrar = true
      this.textoUno = await this.consumirAPI(vectorRes[0])
      this.textoDos = await this.consumirAPI(vectorRes[1])
      this.textoTres = await this.consumirAPI(vectorRes[2])
    },
    generarImagenes(vectorRes) {
      this.imagenUno = "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/" + vectorRes[0] + ".svg"
      this.imagenDos = "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/" + vectorRes[1] + ".svg"
      this.imagenTres = "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/" + vectorRes[2] + ".svg"
    },
    generarVector() {
      const vector = [];
      vector.length = 5;//le damos el tamaño a este vector
      for (let i = 0; i < vector.length; i++) {
        vector[i] = Math.floor(Math.random() * 600) + 1;
      }

      const vectorRes = [];
      vectorRes.length = 3;//le damos el tamaño a este vector
      for (let i = 0; i < vectorRes.length; i++) {
        vectorRes[i] = vector[Math.floor(Math.random() * 3) + 1];
      }
      return vectorRes;
    },
    async consumirAPI(id) {
      const {name} = await fetch("https://pokeapi.co/api/v2/pokemon/" + id).then(result => result.json())
      console.log("consumirAPI:" + name)
      return name
    },
    reinciar() {
      this.mostrar = false
      this.desaparecer = true
      this.mensajePerdida = false
      this.mostrarGanar = false
      this.intento = 0
      this.puntaje = 0
      this.textoUno = "xxxxxxxxxx"
      this.textoDos = "xxxxxxxxxx"
      this.textoTres = "xxxxxxxxxx"
    }
  }
}
</script>

<style scoped>

.mostrarGanar {
  color: blue;
}

.mensajePerdida p {
  display: block;
  margin-top: 50px;
}

.mensajePerdida {
  color: red;

}

.Container {
  margin-top: 50px;
}

#imagenGanadora {
  display: block;
  width: 500px;
  height: 250px;
  margin: 50px auto;
}

p {
  text-align: center;
  font-size: 25px;
  margin: 0 80px;
  display: inline;
}

img {
  position: relative;
  width: 250px;
  height: 300px;
  margin: 15px;
}

button {
  transition-duration: 0.4s;
  padding: 15px;
  margin: 25px auto;
  display: block;
  text-align: center;
  font-size: 16px;
  border-radius: 10px;
  width: 25%;
  background-color: royalblue;
  cursor: pointer;
}

button:hover {
  background-color: darkblue; /* Green */
  color: white;
}

</style>