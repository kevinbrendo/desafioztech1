<template>
    <div class="home">
        <div v-if="Loading == false">
        <input 
        v-model="address"
        v-on:keydown.enter="Geocode()"
        type="text"
        placeholder="Inserir endereço para pedir"
        class="form-element"
        >
        <button class="form-element" v-on:click="Geocode()"> > </button>
        </div>
        <div v-else class="loading">
            <img src="https://www.blogson.com.br/wp-content/uploads/2017/10/loading4.gif" alt="loading">
        </div>
    </div>
</template>

<script lang="ts">
import Axios from 'axios'
import {Vue, Component} from 'vue-property-decorator'
import { createApolloFetch } from 'apollo-fetch'

    const fetch = createApolloFetch({
        uri: 'https://api.code-challenge.ze.delivery/public/graphql'
    })

    @Component
    export default class Home2 extends Vue {
        address = ''
        now = new Date()
        lat = ''
        long = ''
        Loading = false

        Geocode() {
            return Axios.get(`
            https://maps.googleapis.com/maps/api/geocode/json?address=${this.address}&key=AIzaSyCziIJeSD5f4XT2G39lT2LFEBeA3R19q9c`)
            .then(x => {
                this.Loading = true
                this.lat = x.data.results[0].geometry.location.lat
                this.long = x.data.results[0].geometry.location.lng
            }).then(() => {
                fetch({
                    query: `query pocSearchMethod($now: DateTime!, $algorithm: String!, $lat: String!, $long: String!) {
                        pocSearch(now: $now, algorithm: $algorithm, lat: $lat, long: $long) {
                        __typename
                        id
                        status
                        tradingName
                        officialName
                        deliveryTypes {
                            __typename
                            pocDeliveryTypeId
                            deliveryTypeId
                            price
                            title
                            subtitle
                            active
                        }
                        }
                    }`,
                    variables: { now: this.now, algorithm: "NEAREST", lat: (this.lat).toString(), long: (this.long).toString() },
                    }).then(res => {
                    localStorage.setItem('id', res.data.pocSearch[0].id);
                    }).then(() => {
                    window.location.pathname = 'produtos';
                    });
            })
            
        }

    } 
</script>

<style scoped>

.home{
    padding-top: 10%;
}

.form-element {
    outline: none;
    font-size: 2rem;
    border: 1px solid rgb(122, 112, 112);
    padding: 5px 10px 8px;
    color: rgb(122, 112, 112)
}

input.form-element {
    width: 700px;
    background: #FFF2;
    border-top-left-radius: 8px;
    border-bottom-left-radius: 8px;
}

button.form-element {
    border-left: none;
    background-color: darkslategray;
    border-top-right-radius: 8px;
    border-bottom-right-radius: 8px;
    cursor: pointer;
}

.loading > img{
    padding-top: 30px;
    width: 70px;
}

</style>