<template>

  <div id="container">
    <!--  Pantalla inicial-->
    <div v-if="PantallaJuego">
      <div>
        <p>Puntaje: {{ puntaje }} </p>
        <p>Intento: {{ intento }}</p>
      </div>

      <!--      Imagenes-->
      <div>
        <div v-if="!mostrar">
          <img src="../img/negro.png" alt="No se pudo cargar">
          <img src="../img/negro.png" alt="No se pudo cargar">
          <img src="../img/negro.png" alt="No se pudo cargar">
        </div>
        <div v-if="mostrar">
          <img :src="imagenUno" alt="No se pudo cargar">
          <img :src="imagenDos" alt="No se pudo cargar">
          <img :src="imagenTres" alt="No se pudo cargar">
        </div>
      </div>

      <!--      Texto Imagenes-->
      <div>
        <p>{{ textoUno }}</p>
        <p>{{ textoDos }}</p>
        <p>{{ textoTres }}</p>
      </div>

      <button v-on:click="clickJugar()">JUGAR</button>
    </div>

    <!--  Pantalla ganar-->
    <div v-if="pantallaGanar" class="mostrarGanar">
      <p>Puntaje: {{ puntaje }}</p>
      <img id="imagenGanadora" src="../img/congratulations.gif" alt="">
      <p>Felicitaciones has ganado un premio de $10.000,00</p>
      <button v-on:click="reinciar()">Nuevo Juego</button>
    </div>

    <!--  Pantalla perder-->
    <div v-if="pantallaPerdida" class="mensajePerdida">
      <p>Haz utilizado tus 5 intentos</p>
      <p>El juego ha terminado, intentalo nuevamente</p>
      <button v-on:click="reinciar()">Nuevo Juego</button>
    </div>
  </div>

</template>

<script>
export default {
  name: "CasinoPokemon",
  data() {
    return {
      puntaje: 0,
      intento: 0,

      imagenUno: null,
      imagenDos: null,
      imagenTres: null,

      textoUno: "xxxxxxxxxx",
      textoDos: "xxxxxxxxxx",
      textoTres: "xxxxxxxxxx",

      mostrar: false,
      PantallaJuego: true,
      pantallaPerdida: false,
      pantallaGanar: false
    }
  },
  methods: {
    async clickJugar() {
      const vectorRes = this.vectorAleatorio()
      const vectorPlantilla = await this.contruirPokemon(vectorRes)

      this.imagenUno = vectorPlantilla[0].ruta
      this.imagenDos = vectorPlantilla[1].ruta
      this.imagenTres = vectorPlantilla[2].ruta

      this.textoUno = vectorPlantilla[0].nombre
      this.textoDos = vectorPlantilla[1].nombre
      this.textoTres = vectorPlantilla[2].nombre

      this.mostrar = true

      this.numeroIntentos(vectorRes)
    },
    vectorAleatorio() {
      const vector = [];
      vector.length = 5;//le damos el tamaño a este vector
      for (let i = 0; i < vector.length; i++) {
        vector[i] = Math.floor(Math.random() * 600) + 1;// del 1 al 600
      }

      const vectorRes = [];
      vectorRes.length = 3;//le damos el tamaño a este vector
      for (let i = 0; i < vectorRes.length; i++) {
        vectorRes[i] = vector[Math.floor(Math.random() * 3) + 1];
      }
      return vectorRes;
    },
    async consumiAPI(id) {
      return await fetch("https://pokeapi.co/api/v2/pokemon/" + id).then(r => r.json())
    },
    async contruirPokemon(vectorRes) {
      const vectorPokemons = []
      for (const valorActual of vectorRes) {
        const objp = await this.construirObjetoPokemon(valorActual)
        vectorPokemons.unshift(objp)
      }
      return vectorPokemons
    },
    async construirObjetoPokemon(id) {
      const {name} = await this.consumiAPI(id)
      const objetoPokemon = {
        ruta: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/" + id + ".svg",
        nombre: name
      }
      return objetoPokemon
    },
    numeroIntentos(vectorRes) {
      if (this.intento < 5) {
        this.intento += 1
        if (vectorRes[0] === vectorRes[1] && vectorRes[0] === vectorRes[2]) {
          this.puntaje += 5
        } else if (vectorRes[0] === vectorRes[1] || vectorRes[0] === vectorRes[2] || vectorRes[1] === vectorRes[2]) {
          this.puntaje += 2
        }
      } else {
        this.totalPuntaje()
      }
    },
    totalPuntaje() {
      if (this.puntaje >= 10) {
        this.PantallaJuego = false
        this.pantallaGanar = true
      }
      if (this.puntaje < 10) {
        this.pantallaPerdida = true
        this.PantallaJuego = false
      }
    },
    reinciar() {
      this.mostrar = false
      this.PantallaJuego = true
      this.pantallaPerdida = false
      this.pantallaGanar = false
      this.intento = 0
      this.puntaje = 0
      this.textoUno = "xxxxxxxxxx"
      this.textoDos = "xxxxxxxxxx"
      this.textoTres = "xxxxxxxxxx"
    }
  },
}
</script>

<style scoped>
.mostrarGanar {
  color: blue;
}

.mensajePerdida p {
  display: block;
  color: red;
  margin: 40px auto;
}

#container {
  margin-top: 50px;
}

#imagenGanadora {
  display: block;
  width: 500px;
  height: 250px;
  margin: 50px auto;
}

section input, label, p {
  text-align: center;
  display: inline;
  width: 40%;
  margin: 0 90px;
  font-size: 25px;
  padding: 7px;
  border-radius: 10px;
}

img {
  /*Siempre dar un tamaño, porque al llamar el api devuelven distintos tamaños*/
  width: 300px;
  height: 350px;
  margin: 30px 20px 0 20px;
  /*position: relative;*/
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