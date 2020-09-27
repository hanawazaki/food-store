<template>
  <div class="food-detail">
    <navbar />
    <div class="container">
      <!-- start breadcrumb -->
      <div class="row mt-5">
        <div class="col">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <router-link to="/" class="text-dark">Home</router-link>
              </li>
              <li class="breadcrumb-item">
                <router-link to="/foods" class="text-dark">Foods</router-link>
              </li>
              <li class="breadcrumb-item active" aria-current="page">Food Detail</li>
            </ol>
          </nav>
        </div>
      </div>
      <!-- end breadcrumb -->
      <div class="row mt-3">
        <div class="col-md-6">
          <img :src="'/images/'+product.gambar" alt="product" class="img-fluid shadow" />
        </div>
        <div class="col-md-6">
          <h2>{{product.nama}}</h2>
          <hr />
          <h4>
            Harga :
            <strong>Rp. {{product.harga}}</strong>
          </h4>
          <form class="mt-4" v-on:submit.prevent>
            <div class="form-group">
              <label for="jumlah">Jumlah</label>
              <input type="number" class="form-control" v-model="pesan.itempesan" />
            </div>
            <div class="form-group">
              <label for="keterangan">Keterangan</label>
              <textarea
                v-model="pesan.keterangan"
                placeholder="keterangan spt : pedas dll"
                class="form-control"
              ></textarea>
            </div>
            <button type="submit" class="btn btn-success" @click="pemesanan">
              <b-icon-cart></b-icon-cart>Pesan
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Navbar from "@/components/Navbar.vue";
import axios from "axios";

export default {
  name: "FoodDetail",
  data() {
    return {
      product: {},
      pesan: {},
    };
  },
  components: {
    Navbar,
  },
  methods: {
    setProduct(data) {
      this.product = data;
    },
    pemesanan() {
      this.pesan.products = this.product
      axios
      .post('http://localhost:3000/keranjangs', this.pesan)
      .then(()=>{
          console.log('berhasil')
      })
      .catch((error)=>console.log(error))
    },
  },
  mounted() {
    axios
      .get("http://localhost:3000/products/" + this.$route.params.id)
      .then((response) => this.setProduct(response.data))
      .catch((error) => console.log(error));
  },
};
</script>