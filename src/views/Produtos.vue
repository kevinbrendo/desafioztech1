<template>
  <div>
  <div class="categorias">
    <Categorias />
    <Search />
  </div>
  <div class="produtos">
    <div v-for="(p, i) in Produtos" :key="i" class="card">
      <div v-for="(img, i) in p.images" :key="i" class="imagem">
        <img :src="img.url" @error="errorImage" alt="Produtos">
      </div>
      <div class="container">
      {{ p.title }}
      <div v-for="(e, i) in p.productVariants" :key="i" id="price"> R$ {{ e.price.toFixed(2).toString().replace('.',',') }}</div>
      </div>
      <button id="botaocomprar">Comprar</button>
    </div>
    <div>{{ this.$store.state.Produtos.title }}</div>
  </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Component from "vue-class-component";
import { createApolloFetch } from "apollo-fetch";
import Search from "@/components/Search.vue";
import Categorias from '@/components/Categorias.vue'

const fetch = createApolloFetch({
  uri: "https://api.code-challenge.ze.delivery/public/graphql"
});

@Component({
  components: {
    Search,
    Categorias
  }
})
export default class Produtos extends Vue {
  id = localStorage.getItem("id");

  get Produtos() {
    return this.$store.state.Produtos
  }

  // eslint-disable-next-line
  errorImage(event: any){
    event.target.src = "https://courier-images-web.imgix.net/static/img/small-logo.png?auto=compress,format&fit=max&w=undefined&h=undefined&dpr=2&fm=png";
    console.clear();
  }

  beforeMount() {
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
      variables: { id: this.id, search: "", categoryId: null }
    }).then(res => {
      this.$store.state.Produtos = res.data.poc.products
    });
  }

    
}
</script>

<style scoped>
img{
  width: 130px;
}

.produtos{
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.card {
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
  width: 200px;
  height: 260px;
  padding: 30px;
  margin: 15px;
}

.card:hover {
  box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2);
}

.container {
  padding: 2px 16px;
}

#botaocomprar {
  border: none;
  outline: 0;
  padding: 12px;
  color: white;
  background-color: rgb(3, 90, 36);
  text-align: center;
  cursor: pointer;
  width: 100%;
  font-size: 18px;
}
</style>
