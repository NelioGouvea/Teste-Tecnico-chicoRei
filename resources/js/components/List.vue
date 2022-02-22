<template>
    <div class="main">
        <div class="filter mb-2">
            <div style="margin-right: 15px;">
                <label>Tipos:</label>
                <select class="bg-dark text-white"v-model="name" v-on:change="typesFilter">
                    <option value="">Todos</option>
                    <option v-for="type in types" :value="type.name">{{ type.name }}</option>
                </select>
            </div>
            <div>
              <label>Genêro:</label>
                <select class="bg-dark text-white" v-model="name" v-on:change="gendersFilter">
                    <option value="">Todos</option>
                    <option v-for="gender in genders" :value="gender.name">{{ gender.name }}</option>
                </select>
            </div>
        </div>
        <div class="container products">
            <template>
                <p v-if="loading">
                    Estamos procurando nossos produtos para você...
                </p>
                <p v-else-if="listProducts.length === 0">
                    Nenhum produtos encontrado
                </p>
                <div v-for="product in this.listProducts">
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
            <template v-if="listProducts.length !== 0 && pageOfItems >= 2">
              <button class="page-link bg-light text-dark m-1" v-on:click="previousPage()">< Anterior</button>
            </template>
            <template
                class="page-item"
                v-for="(page, index) in this.totalPages"
                v-if="listProducts.length !== 0"
            >           
                <template v-if="pageOfItems === page">
                    <button class="page-link bg-light text-dark m-1" v-on:click="onChangePage(page)">{{ page }}</button>
                </template>
                <template v-else>
                    <button class="page-link bg-light text-dark m-1" v-on:click="onChangePage(page)">{{ page }}</button>
                </template>
            </template>
            <template v-if="pageOfItems < 3 && listProducts.length !== 0">
              <button class="page-link bg-light text-dark m-1" v-on:click="nextPage()">Próxima ></button>
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
      'x-rapidapi-host': process.env.MIX_API_HOST,
      'x-rapidapi-key': process.env.MIX_API_KEY,
    },
    data: {
      cookies: 'cookie_name_1=cookie_value_1;cookie_name_2=cookie_value_2',
      return_text: false,
      url: 'https://chicorei.com/roupas/',
    }
  };

  export default {
    name: "List",
    data() {
      return {
        name: "",
        listProducts: [],
        pageOfItems: 1,
        group: 12,
        products: [],
        types: [],
        loading: true,
        tam: 0,
        genders: [],
        gender: "",
      };
    },
    components: {
      Product,
    },
    methods: {
      pagination() {
        let pageOfItems = this.pageOfItems;
        let group = this.group;
        this.listProducts = this.listProducts.slice((pageOfItems - 1) * group, pageOfItems * group);
        if (pageOfItems > this.totalPages) this.onChangePage(1);
      },
      typesFilter() {
        name = this.name;

        if(name !== ""){
          this.listProducts = this.products.filter(value => value.type.name === name);
        }else{
          this.listProducts = this.products;
        }
        this.tam = this.listProducts.length;
        this.pagination();
      },
      previousPage(){
        this.pageOfItems = this.pageOfItems - 1;
        this.typesFilter();
        this.gendersFilter();
        window.scrollTo(0, 0);
      },
      nextPage(){
        this.pageOfItems = this.pageOfItems + 1;
        this.typesFilter();
        this.gendersFilter();
        window.scrollTo(0, 0);
      },
      onChangePage(pageOfItems) {
        this.pageOfItems = pageOfItems;
        this.typesFilter();
        this.gendersFilter();
        window.scrollTo(0, 0);
      },
      gendersFilter() {
        let search = "";
        name = this.name;
        switch(name){ 
          case "Infantil":
            search = "I";
            break;
          case "Masculino":
            search = "M";
            break;
          case "Feminino":
            search = "F";
            break;
          default:
            search = "";
            break;
        }
        if(search === ""){
          this.listProducts = this.products;
        }else{
          this.listProducts = this.products.filter(value => value.img_cover_gender === search);
        }
        this.tam = this.listProducts.length;
        this.pagination();
      },
    },
    computed: {
      totalPages: function () {
        if (this.tam !== 0){
          return Math.ceil(this.tam / this.group);
        }
        return Math.ceil(this.products.length / this.group);
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
      this.products = request.products.hits;
      this.types = request.filters.types;
      this.genders = request.filters.genders;
      this.listProducts = this.products;
      this.pagination();
      this.loading = false;
    },
  };
</script>
