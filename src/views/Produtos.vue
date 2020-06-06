<template>
  <div>
  <div class="categorias">
    <button @click="CategoryFilter(94)">Cervejas</button>
    <button @click="CategoryFilter(92)">Vinhos</button>
    <button @click="CategoryFilter(96)">Sem álcool</button>
    <button @click="CategoryFilter(97)">Petiscos</button>
    <button @click="CategoryFilter(98)">Outros</button>
  </div>
  <div class="produtos">
    <div v-for="(p, i) in prod" :key="i" class="card">
      <div v-for="(img, i) in p.images" :key="i" class="imagem">
        <img :src="img.url" @error="errorImage" alt="Produtos">
      </div>
      <div class="container">
      {{ p.title }}
      <div v-for="(e, i) in p.productVariants" :key="i" id="price"> R$ {{ e.price.toFixed(2).toString().replace('.',',') }}</div>
      </div>
      <button id="botaocomprar">Comprar</button>
    </div>
  </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Component from "vue-class-component";
import { createApolloFetch } from "apollo-fetch";

const fetch = createApolloFetch({
  uri: "https://api.code-challenge.ze.delivery/public/graphql"
});

@Component
export default class Produtos extends Vue {
  prod = [];
  id = localStorage.getItem("id");

  // eslint-disable-next-line
  errorImage(event: any){
    event.target.src = "https://courier-images-web.imgix.net/static/img/small-logo.png?auto=compress,format&fit=max&w=undefined&h=undefined&dpr=2&fm=png";
    console.clear();
  }

  beforeMount() {
    fetch({
      query: `query poc($id: ID!, $categoryId: Int, $search: String) {
        poc(id: $id) {
          id
          products(categoryId: $categoryId, search: $search) {
            id
            title
            rgb
            images {
              url
            }
            productVariants {
              price
              inventoryItemId
              shortDescription
              title
              published
              volume
              volumeUnit
              description
              subtitle
              components {
                id
                productVariantId
                productVariant {
                  id
                  title
                  description
                  shortDescription
                }
              }
            }
          }
        }
      }`,
      variables: { id: this.id, search: "", categoryId: null }
    }).then(res => {
      this.prod = res.data.poc.products;
    });
  }

  // eslint-disable-next-line
  CategoryFilter(Cid: any) {
    fetch({
      query: `query poc($id: ID!, $categoryId: Int, $search: String) {
        poc(id: $id) {
          id
          products(categoryId: $categoryId, search: $search) {
            id
            title
            rgb
            images {
              url
            }
            productVariants {
              price
              inventoryItemId
              shortDescription
              title
              published
              volume
              volumeUnit
              description
              subtitle
              components {
                id
                productVariantId
                productVariant {
                  id
                  title
                  description
                  shortDescription
                }
              }
            }
          }
        }
      }`,
      variables: { id: this.id, search: "", categoryId: Cid }
    }).then(res => {
      this.prod = res.data.poc.products;
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
