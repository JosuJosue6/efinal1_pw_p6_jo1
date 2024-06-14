<template>
  <div class="score">
    <label for="">Puntaje: {{ puntaje }}</label>
    <label for="">Intento: {{ intentos }}</label>
  </div>
  <div  class="pokemon">
    <div>
      <img :src="imagen || fondo" alt="" />
      <p>{{ nombre }}</p>
    </div>

    <div>
      <img :src="imagen2 || fondo" alt="" />
      <p>{{ nombre2 }}</p>
    </div>

    <div>
      <img :src="imagen3 || fondo" alt="" />
      <p>{{ nombre3 }}</p>
    </div>
  </div>

  <button @click="jugar">JUGAR</button>

  <div v-show="intentos === 5">
    <label class="gano">{{ mensaje }}</label>
    <p><button @click="nuevoJuego">Nuevo Juego</button></p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      puntaje: 0,
      intentos: 0,
      imagen: null,
      nombre: "xxxxxxx",
      imagen2: null,
      nombre2: "xxxxxxx",
      imagen3: null,
      nombre3: "xxxxxxx",
      fondo: require("@/assets/fondo.png"),
      mensaje: "",
    };
  },

  methods: {
    async jugar() {
      this.intentos++;

      await Promise.all([
        this.buscarPokemon("nombre", "imagen"),
        this.buscarPokemon("nombre2", "imagen2"),
        this.buscarPokemon("nombre3", "imagen3")]
      );

      this.puntajeTotal();
      if (this.intentos === 5) {
        this.mensajeFinal();
      }
    },

    async buscarPokemon(nombreP, imagenP) {
      const idPokemon = Math.floor(Math.random() * 5 + 1);
      const pokemon = await fetch(
        `https://pokeapi.co/api/v2/pokemon/${idPokemon}`
      ).then((p) => p.json());
      //console.log(idPokemon);
      //console.log(pokemon);
      const { name } = pokemon;
      console.log("Aqui "+name);
      this[nombreP] = name;
      this[
        imagenP
      ] = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${idPokemon}.svg`;
    },

    puntajeTotal() {
      if (this.imagen === this.imagen2 && this.imagen === this.imagen3) {
        this.puntaje += 5;
      } else if (
        this.imagen === this.imagen2 ||
        this.imagen === this.imagen3 ||
        this.imagen2 === this.imagen3
      ) {
        this.puntaje += 2;
      } else {
        this.puntaje += 0;
      }
    },

    mensajeFinal() {
      if (this.puntaje >= 10) {
        this.mensaje = "Felicitaciones has ganado un premio de $10.000,00";
      } else {
        this.mensaje =
          "Has utilizado tus 5 intentos. El juego ha termindo, intentalo nuevamente";
      }
    },

    nuevoJuego() {
      this.puntaje = 0;
      this.intentos = 0;
      this.imagen = null;
      this.nombre = "xxxxxxx";
      this.imagen2 = null;
      this.nombre2 = "xxxxxxx";
      this.imagen3 = null;
      this.nombre3 = "xxxxxxx";
      this.mensaje = "";
    },
  },
};
</script>

<style>
label {
  margin: 40px;
}

.score {
  margin: 15px;
}

button {
  padding: 10px 80px;
  cursor: pointer;
  margin-bottom: 15px;
  border: 2px solid black;
  font-size: 20px;
}

img {
  width: 250px;
  height: 250px;
}

.pierde {
  color: red;
}

.gana {
  color: blue;
}
</style>