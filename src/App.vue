<template>
  <nav class="menu__box">
    <button @click="poprzednie">Poprzednia strona</button>
    <button @click="następne;">Następna strona</button>
    <button @click="reset">Reset</button>
    <input @keyup="szukaj" v-model="szukanaFraza" placeholder="szukaj" />
  </nav>

  <tabela-api
    :tabToTable="tabDoTabeli"
    @wyslij-id="wyslij"
    @sortowanie-tablicy="sortowanie"
  />
</template>

<script>
import TabelaApi from "./components/TabelaApi.vue";

export default {
  components: {
    TabelaApi,
  },
  data() {
    return {
      tab: [],
      tabDoTabeli: [],
      limit: 10,
      szukanaFraza: null,
      jakieSorotwanie: "id",
      api_key: "",
    };
  },
  methods: {
    sortowanie(kolumna) {
      if (kolumna != this.jakieSorotwanie) {
        this.jakieSorotwanie = kolumna;
        this.tabDoTabeli.sort((a, b) => (a[kolumna] > b[kolumna] ? 1 : -1));
      } else {
        this.jakieSorotwanie = "";
        this.tabDoTabeli.sort((a, b) => (a[kolumna] < b[kolumna] ? 1 : -1));
      }
    },
    async wyslij(id) {
      return fetch(
        `https://app.linkhouse.co/rekrutacja/ping?api_key=${this.api_key}&user_id=` +
          id,
        {
          method: "POST",
          "content-type": "application/json",
        }
      )
        .then((dana) => {
          if (dana.ok) {
            return dana.json();
          } else {
            throw new Error("Something went wrong");
          }
        })
        .then((daneR) => {
          if (daneR.msg == "ok") {
            console.log(daneR);
          } else {
            throw new Error(daneR.errors);
          }
        });
    },

    async reset() {
      this.szukanaFraza = null;
      this.tabDoTabeli = [];
      return fetch(
        `https://app.linkhouse.co/rekrutacja/api?api_key=${this.api_key}`,
        {
          method: "POST",
          "content-type": "application/json",
        }
      )
        .then((dana) => {
          if (dana.ok) {
            return dana.json();
          } else {
            throw new Error("Something went wrong");
          }
        })
        .then((daneR) => {
          if (daneR.msg == "ok") {
            this.tab = daneR.response;
            let i = 0;
            for (const item of this.tab) {
              if (i < 10) {
                i++;
                this.tabDoTabeli.push(item);
              }
            }
          } else {
            throw new Error(daneR.errors);
          }
        })
        .catch((err) => {
          console.log(err);
          throw new Error("Something went wrong");
        });
    },

    szukaj() {
      if (this.szukanaFraza != "") {
        const TablicaZnalezionych = [];
        for (const item of this.tabDoTabeli) {
          if (
            item.name.includes(this.szukanaFraza) ||
            item.login.includes(this.szukanaFraza)
          ) {
            TablicaZnalezionych.push(item);
          }
          this.tabDoTabeli = TablicaZnalezionych;
        }
      }
    },
    następne() {
      if (this.limit != 100) {
        this.tabDoTabeli = [];
        let i = 0;
        this.limit += 10;
        for (const item of this.tab) {
          if (i < this.limit && i > this.limit - 11) {
            this.tabDoTabeli.push(item);
          }
          i++;
        }
      }
    },
    poprzednie() {
      if (this.limit != 10) {
        this.tabDoTabeli = [];
        let i = 0;
        this.limit -= 10;
        for (const item of this.tab) {
          if (i < this.limit && i > this.limit - 11) {
            this.tabDoTabeli.push(item);
          }
          i++;
        }
      }
    },
  },
  async created() {
    if (localStorage.getItem("api_key") === null) {
      localStorage.setItem("api_key", prompt("Prosze podać klucz api"));
      this.api_key = localStorage.getItem("api_key");
    } else {
      this.api_key = localStorage.getItem("api_key");
    }
    this.reset();
  },
};
</script>

<style>
button {
  background: inherit;
  border: none;
  transition: 0.5s;
  font-weight: bold;
  padding: 10px;
  font-size: 1.2rem;
}
button:hover {
  color: rgb(241, 163, 16);
  transform: scale(1.2);
}
.menu__box {
  display: flex;
  justify-content: space-evenly;
}
@media screen and (max-width: 600px) {
  .menu__box {
    flex-wrap: wrap;
  }
  button {
    font-size: 0.8rem;
  }
}
</style>
