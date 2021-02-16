<template>
  <div id="app">
    <div class="hero is-white is-gradient is-bold">
      <div class="hero-body">
        <h1 class="title">
          <span class="has-text-success">R&M</span>
          <span class="subtitle">Personajes</span>
        </h1>

        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input
            placeholder="Buscar por Nombre"
              v-model="search"
              type="text"
              class="input is-rounded"
              @keyup.enter="searchData"
            />
          </div>
          <div class="control">
            <button class="button is-success is-rounded" @click="searchData">
              Buscar
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div
        class="columns is-desktop is-mobile is-tablet is-multiline is-centered"
      >
        <character
          @showModal="showModal"
          v-for="character in characters"
          :key="character.id"
          :character="character"
        />
      </div>

      <nav
        class="pagination is-rounded"
        role="navigation"
        aria-label="pagination"
      >
        <a class="pagination-previous" @click="changePage(page - 1)"
          >Anterior</a
        >
        <ul class="pagination-list">
          <li>
            <a class="pagination-link is-current">{{ page }}</a>
          </li>
        </ul>
        <a class="pagination-next" @click="changePage(page + 1)">Siguiente</a>
      </nav>
    </div>

    <div class="modal" :class="{ 'is-active': modal }" v-if="modal">
      <div class="modal-background" @click="modal = false"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-titile">
            Acerda de: {{ currentCharacter.name }}
          </p>
          
        </header>
        <div class="modal-card-body">
          <img :src="currentCharacter.image" :alt="currentCharacter.name" />
          <p>Genero: {{ currentCharacter.gender }}</p>
          <p>Estado: {{ currentCharacter.status }}</p>
          <p>Raza: {{ currentCharacter.species }}</p>
          <!-- <p>Tipo: {{currentCharacter}}</p> -->
          <p>Ubicacion: {{ currentCharacter.origin.name }}</p>
        </div>

        <footer class="modal-card-foot">
          <button class="buton" @click="modal = false">Cerrar</button>
        </footer>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import Character from "./components/Character";
export default {
  data() {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: "",
      modal: false,
      currentCharacter: {}
    };
  },
  created() {
    this.consumirApi();
  },
  name: "app",
  components: {
    Character //character
  },
  methods: {
    consumirApi() {
      const params = {
        page: this.page,
        name: this.search
      };
      let result = axios
        .get(`https://rickandmortyapi.com/api/character/`, { params })
        .then(res => {
          this.characters = res.data.results;
          // console.log(res.data.info)
          this.pages = res.data.info.pages;
          // console.log(res.data);
        })
        .catch(error => {
          console.log(error);
        });
      return result;
    },
    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      this.consumirApi();
    },
    searchData() {
      this.page = 1;
      this.consumirApi();
    },
    showModal(id) {
      //consultar los datos de los personajes por id
      this.fetchCharacter(id);
    },
    async fetchCharacter(id) {
      let result = await axios.get(
        `https://rickandmortyapi.com/api/character/${id}/`
      );
      console.log(result.data);
      this.currentCharacter = result.data;
      console.log(this.currentCharacter, "personaje");
      this.modal = true;
    }
  }
};
</script>
<style></style>
