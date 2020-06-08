<template>
    <div class="categorias">
    <button @click="CategoryFilter(94)">Cervejas</button>
    <button @click="CategoryFilter(92)">Vinhos</button>
    <button @click="CategoryFilter(96)">Sem álcool</button>
    <button @click="CategoryFilter(97)">Petiscos</button>
    <button @click="CategoryFilter(98)">Outros</button>
    </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import { createApolloFetch } from "apollo-fetch";

const fetch = createApolloFetch({
  uri: "https://api.code-challenge.ze.delivery/public/graphql"
});

@Component
export default class Categorias extends Vue {
  id = localStorage.getItem("id");

    // eslint-disable-next-line
    CategoryFilter(Cid: any) {
        fetch({
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
        variables: { id: this.id, search: "", categoryId: Cid }
        }).then(res => {
        this.$store.state.Produtos = res.data.poc.products;
        });
    }
}

</script>

<style scoped>
.categorias {
  margin: 10px;
}

.categorias > button {
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  margin: 10px;
  width: 150px;
  border: none;
  padding: 8px;
  color: rgb(255, 255, 255);
  background-color: rgb(15, 62, 70);
  text-align: center;
  cursor: pointer;
  font-size: 18px;
}

.categorias > button:hover {
  box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2);
}
</style>