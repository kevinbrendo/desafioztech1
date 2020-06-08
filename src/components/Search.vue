<template>
    <div>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons"
      rel="stylesheet">
    <div class="searchBox">
        <input class="searchInput" type="text" placeholder="Pesquisar" v-on:keydown.enter="Pesquisar()" v-model="Search">
        <button class="searchButton" @click="Pesquisar()">
            <i class="material-icons">search</i>
        </button>
    </div>
    </div>
    
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import { createApolloFetch } from "apollo-fetch";


const fetch = createApolloFetch({
  uri: "https://api.code-challenge.ze.delivery/public/graphql"
});

@Component
export default class Search extends Vue {
  id = localStorage.getItem("id");
  Search = "";

    Pesquisar() {
      return fetch({
      query: `query poc($id: ID!, $categoryId: Int, $search: String) {
        poc(id: $id) {
          id
          products(categoryId: $categoryId, search: $search) {
            id
            title
            images {
              url
            }
            productVariants {
              price
              title
            }
          }
        }
      }`,
      variables: { id: this.id, search: this.Search, categoryId: null }
    }).then(res => {
      this.$store.state.Produtos = res.data.poc.products;
    });
    }

}

</script>

<style scoped>

.searchBox {
    position: absolute;
    top: -4%;
    left: 80%;
    transform:  translate(-50%,50%);
    background: #2f3640;
    height: 40px;
    border-radius: 40px;
    padding: 10px;

}

.searchBox:hover > .searchInput {
    width: 240px;
    padding: 0 6px;
}

.searchBox:hover > .searchButton {
  background: white;
  color : #2f3640;
}

.searchButton {
    color: rgb(34, 134, 109);
    float: right;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: #2f3640;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: 1.9s;
}

.searchInput {
    border:none;
    background: none;
    outline:none;
    float:left;
    padding: 0;
    color: white;
    font-size: 16px;
    transition: 1.9s;
    line-height: 40px;
    width: 0px;

}

@media screen and (max-width: 620px) {
.searchBox:hover > .searchInput {
    width: 150px;
    padding: 0 6px;
}
}
</style>