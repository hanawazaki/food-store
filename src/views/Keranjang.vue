<template>
  <div class="keranjang">
    <Navbar :updatedKeranjang="keranjang" />
    <div class="container">
      <!-- start breadcrumb -->
      <div class="row mt-4">
        <div class="col">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <router-link to="/" class="text-dark">Home</router-link>
              </li>
              <li class="breadcrumb-item">
                <router-link to="/foods" class="text-dark">Foods</router-link>
              </li>
              <li class="breadcrumb-item active" aria-current="page">Keranjang</li>
            </ol>
          </nav>
        </div>
      </div>
      <!-- end breadcrumb -->
      <div class="row">
        <div class="col">
          <h2>
            Keranjang
            <strong>Saya</strong>
          </h2>
          <div class="table-responsive mt-3">
            <table class="table">
              <thead>
                <tr>
                  <th scope="col">#</th>
                  <th scope="col">Foto</th>
                  <th scope="col">Makanan</th>
                  <th scope="col">Keterangan</th>
                  <th scope="col">Jumlah</th>
                  <th scope="col">Harga</th>
                  <th scope="col">Total Harga</th>
                  <th scope="col">Hapus</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(keranjangs,index) in keranjang" :key="keranjangs.id">
                  <th scope="row">{{index+1}}</th>
                  <td>
                    <img
                      :src="'/images/'+keranjangs.products.gambar"
                      alt="product"
                      class="img-fluid shadow"
                      width="250"
                    />
                  </td>
                  <td>{{keranjangs.products.nama}}</td>
                  <td>{{keranjangs.keterangan ? keranjangs.keterangan : " - "}}</td>
                  <td>{{keranjangs.itempesan}} porsi</td>
                  <td align="right">Rp. {{keranjangs.products.harga}}</td>
                  <td align="right">Rp. {{keranjangs.products.harga * keranjangs.itempesan}}</td>
                  <td align="center" class="text-danger">
                    <b-icon-trash @click="hapusItemKeranjang(keranjangs.id)"></b-icon-trash>
                  </td>
                </tr>
                <tr>
                  <td colspan="6" align="right">
                    <strong>Total Harga :</strong>
                  </td>
                  <td align="right">Rp. {{totalHarga}}</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
      <!-- form checkout -->
      <div class="row justify-content-end">
        <div class="col-md-4">
          <form class="mt-4" v-on:submit.prevent>
            <div class="form-group">
              <label for="nama">Nama</label>
              <input type="text" class="form-control" v-model="pesan.nama" />
            </div>
            <div class="form-group">
              <label for="nomeja">Nomor Meja</label>
              <input type="text" class="form-control" v-model="pesan.nomeja" />
            </div>
            <button type="submit" class="btn btn-success float-right" @click="checkout">
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
  name: "Keranjang",
  components: {
    Navbar,
  },
  data() {
    return {
      keranjang: [],
      pesan: {},
    };
  },
  methods: {
    setKeranjang(data) {
      this.keranjang = data;
    },
    hapusItemKeranjang(id) {
      axios
        .delete("https://kulineran-server.herokuapp.com/keranjangs/" + id)
        .then(() => {
          this.$toast.success("Sukses Hapus Item", {
            type: "error",
            position: "top-right",
            duration: 3000,
            dismissible: true,
          });

          axios
            .get("https://kulineran-server.herokuapp.com/keranjangs")
            .then((res) => this.setKeranjang(res.data))
            .catch((err) => console.log(err));
        })
        .catch((err) => console.log(err));
    },
    checkout() {
      if (this.pesan.nama && this.pesan.nomeja) {
        this.pesan.kerangjangs = this.keranjang;
        axios
        .post("https://kulineran-server.herokuapp.com/pesanans", this.pesan)
        .then(() => {
            // reset keranjang
            this.keranjang.map(function(item){
                return axios
                .delete("https://kulineran-server.herokuapp.com/keranjangs/" + item.id)
                .catch((err) => console.log(err));
            });
          this.$router.push({ path: "/pesanan-sukses" });
          this.$toast.success("Sukses Dipesan", {
            type: "success",
            position: "top-right",
            duration: 3000,
            dismissible: true,
          });
        });
      } else {
        this.$toast.success("Nama dan Nomor Meja harus diisi", {
          type: "error",
          position: "top-right",
          duration: 3000,
          dismissible: true,
        });
      }
    },
  },
  mounted() {
    axios
      .get("https://kulineran-server.herokuapp.com/keranjangs")
      .then((res) => this.setKeranjang(res.data))
      .catch((err) => console.log(err));
  },
  computed: {
    totalHarga() {
      return this.keranjang.reduce(function (items, data) {
        return items + data.products.harga * data.itempesan;
      }, 0);
    },
  },
};
</script>