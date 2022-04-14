<template>
  <table>
    <thead>
      <tr>
        <th
          v-for="(column, index) in objList"
          :key="index"
          @click="sortowanie(column)"
        >
          {{ column }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr class="wiersz" v-for="dana in tabToTable" :key="dana">
        <td data-label="id">{{ dana.id }}</td>
        <td data-label="name">{{ dana.name }}</td>
        <td data-label="login">{{ dana.login }}</td>
        <td data-label="created_at">{{ dana.created_at }}</td>
        <td><button @click="wyslij(dana.id)">wyślij</button></td>
      </tr>
    </tbody>
  </table>
</template>
<script>
export default {
  props: ["tabToTable"],
  methods: {
    cos() {},
    wyslij(id) {
      console.log(id);
      this.$emit("wyslijId", id);
    },
    sortowanie(columna) {
      this.$emit("sortowanieTablicy", columna);
    },
  },
  computed: {
    objList() {
      const cos = this.tabToTable.map((item) => Object.keys(item));
      return cos[0];
    },
  },
};
</script>

<style scoped>
table {
  width: 100%;
  text-align: left;
  border-collapse: collapse;
}
th {
  font-size: 1.5rem;
  padding: 10px;
  cursor: pointer;
}
td {
  font-size: 1.2rem;
  padding: 10px;
}
tr {
  border-bottom: 1px solid #ddd;
}
@media screen and (max-width: 600px) {
  thead {
    display: flex;
    justify-content: space-evenly;
  }

  thead tr th {
    font-size: 1em;
    text-align: center;
    margin: 0 auto;
  }

  table tr {
    border-bottom: 3px solid #ddd;
    display: block;
    margin-bottom: 0.625em;
  }
  table td {
    border-bottom: 1px solid #ddd;
    display: block;
    font-size: 0.8em;
    text-align: right;
  }

  table td::before {
    content: attr(data-label);
    float: left;
    font-weight: bold;
    text-transform: uppercase;
  }
  thead tr {
    border: none;
  }
}
</style>
