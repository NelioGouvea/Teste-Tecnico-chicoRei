<style>
.filter{
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    padding: 5px;
}

select{
  cursor: pointer;
  border-radius: 3px;
  padding: 3px;
  background-color: white;
}

select:hover{
  background-color: #orange;
}

.products{
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}
</style>

<template>
  <div class="main">
    <div class="filter">
        <div class="filter-center"> 
            <label>Tipos:</label>
            <select v-model="name" v-on:change="evenProducts">
                <option value="">Todos</option>
                <option v-for="type in types" :value="type.name">{{ type.name }}</option>
            </select>  
        </div>
    </div>
    <div class="container products">
        <template>
            <p v-if="firtsLoad === 0">Estamos procurando nossos produtos para você...</p>
            <p v-if="evenProductsList.length === 0 && firtsLoad !== 0">Nenhum resultado encontrado</p>
            <div v-for="product in this.evenProductsList">
            <Product
                class=""
                :title="product.name"
                :type="product.type.name"
                :img="product.img_cover"
                :price="product.price"
            />
            </div>
        </template>
    </div>
    <div class="pagination justify-content-center pb-3">
      <template class="page-item"
        v-for="(page, index) in this.totalPages"
        v-if="evenProductsList.length !== 0"
      >
        <template v-if="pageOfItems === page">
          <button class="page-link" v-on:click="onChangePage(page)">
            {{ page }}
          </button>
        </template>
        <template v-else>
          <button class="page-link" v-on:click="onChangePage(page)">
            {{ page }}
          </button>
        </template>
      </template>
    </div>
  </div>
</template>

<script>
import Product from "./Product";

var axios = require("axios").default;

var options = {
  method: 'POST',
  url: 'https://scrapingant.p.rapidapi.com/post',
  headers: {
    'content-type': 'application/json',
    'x-rapidapi-host': 'scrapingant.p.rapidapi.com',
    'x-rapidapi-key': 'd55ae156e8msh502c96128bcc8b2p159ab2jsnc645559c46b0'
  },
  data: {
    cookies: 'cookie_name_1=cookie_value_1;cookie_name_2=cookie_value_2',
    return_text: false,
    url: 'https://chicorei.com/roupas/'
  }
};

export default {
  name: "ProductList",
  data() {
    return {
      name: "",
      evenProductsList: [],
      pageOfItems: 1,
      group: 8,
      tam: 0,
      products: [],
      types: [],
      firtsLoad: 0,
    };
  },
  components: {
    Product,
  },
  methods: {
    pagination() {
      let pageOfItems = this.pageOfItems;
      let group = this.group;
      let i = 0;
      this.evenProductsList = this.evenProductsList.filter(function (value) {
        i++;
        return i > (pageOfItems - 1) * group && i <= pageOfItems * group;
      });
      if (pageOfItems > this.totalPages) this.onChangePage(1);
    },
    evenProducts() {
      name = this.name;
      let i = 0;
      this.evenProductsList = this.products.filter(function (value) {
        if (value.type.name === name || name === "") {
          i++;
          return true;
        } else return false;
      });
      this.tam = i;
      this.pagination();
    },
    onChangePage(pageOfItems) {
      this.pageOfItems = pageOfItems;
      this.evenProducts();
      window.scrollTo(0, 0);
    },
  },
  computed: {
    totalPages: function () {
      if (this.tam !== 0) return Math.ceil(this.tam / this.group);
      else return Math.ceil(this.products.length / this.group);
    },
  },
  async mounted() {
    const response = await axios
      .request(options)
      .then(function (response) {
        return response.data;
      })
      .catch(function (error) {
        console.error(error);
      });
    let result = response.substring(
      response.lastIndexOf('{"state":"vueProducts"'),
      response.indexOf(";var popListener=function(event)")
    );
    let request = JSON.parse(result);
    console.log(request.products.types);
    this.products = request.products.hits;
    this.types = request.filters.types;
    this.evenProductsList = this.products;
    this.pagination();
    this.firtsLoad = 1;
  },
};
</script>