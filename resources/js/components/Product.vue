<template>
    <div class="card mb-3" style="width: 18rem;">
        <img class="card-img-top" alt="Camiseta" :src="img">
        <div class="card-body">
            <h6 class="card-text">{{ title }}</h6>
            <strong><p class="card-text">R$ {{ price.toFixed(2) }}</p></strong>
            <p class="card-text">4x R$ {{ (price/4).toFixed(2) }}</p>
        </div>
    </div>
</template>

<script>
    export default {
    props: {
        title: String,
        price: Number,
        img: String,
        type: String,
    },
    name: "Product",
    };
</script>
