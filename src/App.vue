<template>
  <nav class="menu__box">
    <button @click="prev">Poprzednia strona</button>
    <button @click="next">Następna strona</button>
    <button @click="reset">Reset</button>
    <input @keyup="szukaj" v-model="szukanaFraza" placeholder="szukaj" />
  </nav>

  <tabela-api
    :tabToTable="tabToTable"
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
      tabToTable: [],
      limit: 10,
      szukanaFraza: null,
      jakieSorotwanie: "id",
    };
  },
  methods: {
    sortowanie(kolumna) {
      if (kolumna != this.jakieSorotwanie) {
        this.jakieSorotwanie = kolumna;
        this.tabToTable.sort((a, b) => (a[kolumna] > b[kolumna] ? 1 : -1));
      } else {
        this.jakieSorotwanie = "";
        this.tabToTable.sort((a, b) => (a[kolumna] < b[kolumna] ? 1 : -1));
      }
    },
    async wyslij(id) {
      this.szukanaFraza = null;
      return fetch(
        "https://app.linkhouse.co/rekrutacja/api?api_key=APIREKRUTACJA&user_id=" +
          id,
        {
          method: "POST",
          "content-type": "application/json",
        }
      ).then((dana) => console.log(dana));
    },

    async reset() {
      this.szukanaFraza = null;
      this.tabToTable = [];
      return fetch(
        "https://app.linkhouse.co/rekrutacja/api?api_key=APIREKRUTACJA",
        {
          method: "POST",
          "content-type": "application/json",
        }
      )
        .then((data) => {
          if (data.ok) {
            // console.log(data);
            return data.json();
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
                this.tabToTable.push(item);
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
        for (const item of this.tabToTable) {
          if (
            item.name.includes(this.szukanaFraza) ||
            item.login.includes(this.szukanaFraza)
          ) {
            TablicaZnalezionych.push(item);
          }
          this.tabToTable = TablicaZnalezionych;
        }
      }
    },
    next() {
      if (this.limit != 100) {
        this.tabToTable = [];
        let i = 0;
        this.limit += 10;
        for (const item of this.tab) {
          if (i < this.limit && i > this.limit - 11) {
            this.tabToTable.push(item);
          }
          i++;
        }
      }
    },
    prev() {
      if (this.limit != 10) {
        this.tabToTable = [];
        let i = 0;
        this.limit -= 10;
        for (const item of this.tab) {
          if (i < this.limit && i > this.limit - 11) {
            this.tabToTable.push(item);
          }
          i++;
        }
      }
    },
  },
  async created() {
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
