<template>
  <div class="card shadow card-product">
    <img class="card-img-top" :src="'/images/'+product.gambar" v-bind:alt="product" />
    <div class="card-body">
      <h5 class="card-title">{{ product.nama }}</h5>
      <p
        class="card-text"
      >Harga : Rp. {{ product.harga }}</p>
      <router-link :to="'/foods/'+product.id" class="btn btn-success"><b-icon-cart></b-icon-cart> Pesan</router-link>
    </div>
  </div>
</template>
<script>
export default {
  name: "CardProduct",
  props: ["product"],
};
</script>