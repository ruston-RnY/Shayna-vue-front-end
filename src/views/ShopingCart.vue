<template>
  <div class="shoping_cart">
    <HeaderShayna />
    <Breadcrumb />

    <!-- Shopping Cart Section Begin -->
    <section class="shopping-cart spad text-left">
      <div class="container">
        <div class="row">
          <div class="col-lg-8">
            <div class="row">
              <div class="col-lg-12">
                <div class="cart-table">
                  <table>
                    <thead>
                      <tr>
                        <th>Image</th>
                        <th class="p-name text-center">Product Name</th>
                        <th>Price</th>
                        <th>Action</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr
                        v-for="keranjang in keranjangUser"
                        :key="keranjang.id"
                      >
                        <td class="cart-pic first-row">
                          <img :src="keranjang.photo" class="img-cart" />
                        </td>
                        <td class="cart-title first-row text-center">
                          <h5>{{ keranjang.name }}</h5>
                        </td>
                        <td class="p-price first-row">
                          Rp {{ keranjang.price }}
                        </td>
                        <td class="delete-item">
                          <a href="#" @click="removeItem(keranjang.id)"
                            ><i class="material-icons"> close </i></a
                          >
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
              <div class="col-lg-8">
                <h4 class="mb-4">Informasi Pembeli:</h4>
                <div class="user-checkout">
                  <form>
                    <div class="form-group">
                      <label for="namaLengkap">Nama lengkap</label>
                      <input
                        v-model="customerInfo.name"
                        type="text"
                        class="form-control"
                        id="namaLengkap"
                        aria-describedby="namaHelp"
                        placeholder="Masukan Nama"
                      />
                    </div>
                    <div class="form-group">
                      <label for="namaLengkap">Email Address</label>
                      <input
                        v-model="customerInfo.email"
                        type="email"
                        class="form-control"
                        id="emailAddress"
                        aria-describedby="emailHelp"
                        placeholder="Masukan Email"
                      />
                    </div>
                    <div class="form-group">
                      <label for="namaLengkap">No. HP</label>
                      <input
                        v-model="customerInfo.telpon"
                        type="text"
                        class="form-control"
                        id="noHP"
                        aria-describedby="noHPHelp"
                        placeholder="Masukan No. HP"
                      />
                    </div>
                    <div class="form-group">
                      <label for="alamatLengkap">Alamat Lengkap</label>
                      <textarea
                        v-model="customerInfo.address"
                        class="form-control"
                        id="alamatLengkap"
                        rows="3"
                      ></textarea>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
          <div class="col-lg-4">
            <div class="row">
              <div class="col-lg-12">
                <div class="proceed-checkout">
                  <ul>
                    <li class="subtotal text-center" style="font-size: 20px">
                      Transaction Details
                    </li>
                    <li class="subtotal mt-3">
                      Subtotal <span>Rp {{ subTotal }}</span>
                    </li>
                    <li class="subtotal mt-3">
                      Pajak 10%<span>Rp {{ pajak }}</span>
                    </li>
                    <li class="subtotal mt-3">
                      Total Biaya <span>Rp {{ totalBiaya }}</span>
                    </li>
                    <li class="subtotal mt-3">
                      Bank Transfer <span>Mandiri</span>
                    </li>
                    <li class="subtotal mt-3">
                      No. Rekening <span>2208 1996 1403</span>
                    </li>
                    <li class="subtotal mt-3">
                      Nama Penerima <span>Shayna</span>
                    </li>
                  </ul>
                  <a @click="checkout()" href="#" class="proceed-btn"
                    >I ALREADY PAID</a
                  >
                  <!-- <router-link to="/success" class="proceed-btn"
                    >I ALREADY PAID</router-link
                  > -->
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- Shopping Cart Section End -->
  </div>
</template>

<script>
import HeaderShayna from "@/components/HeaderShayna.vue";
import Breadcrumb from "@/components/Breadcrumb.vue";
import axios from "axios";

export default {
  name: "ShopingCart",
  components: {
    HeaderShayna,
    Breadcrumb,
  },
  data() {
    return {
      keranjangUser: [],
      customerInfo: {
        name: "",
        email: "",
        telpon: "",
        addres: "",
      },
    };
  },
  methods: {
    removeItem(idx) {
      // mencari dan mengambil id dari item yang akan dihapus
      let keranjangUserStorage = JSON.parse(
        localStorage.getItem("keranjangUser")
      );
      let itemKeranjangUserStorage = keranjangUserStorage.map(
        (itemKeranjangUserStorage) => itemKeranjangUserStorage.id
      );

      // mengecek id yang didapat dengan id yang ada di storage kemudian menghapusnya
      let index = itemKeranjangUserStorage.findIndex((id) => id == idx);
      this.keranjangUser.splice(index, 1);

      // merefresh keranjang
      const parsed = JSON.stringify(this.keranjangUser);
      localStorage.setItem("keranjangUser", parsed);
      window.location.reload();
    },
    checkout() {
      let productId = this.keranjangUser.map(function (product) {
        return product.id;
      });

      let checkoutData = {
        name: this.customerInfo.name,
        email: this.customerInfo.email,
        telpon: this.customerInfo.telpon,
        address: this.customerInfo.address,
        transaction_total: this.totalBiaya,
        transaction_status: "PENDING",
        transaction_details: productId,
      };

      axios
        .post("http://localhost:8000/api/checkout", checkoutData)
        .then(() => this.$router.push("SUCCESS"))
        // eslint-disable-next-line no-console
        .catch((err) => console.log(err));

      // menghapus semua item di keranjang setelah checkout success
      localStorage.clear();
    },
  },
  mounted() {
    if (localStorage.getItem("keranjangUser")) {
      try {
        this.keranjangUser = JSON.parse(localStorage.getItem("keranjangUser"));
      } catch (e) {
        localStorage.removeItem("keranjangUser");
      }
    }
  },
  computed: {
    subTotal() {
      return this.keranjangUser.reduce(function (items, data) {
        return items + data.price;
      }, 0);
    },
    pajak() {
      return (this.subTotal * 10) / 100;
    },
    totalBiaya() {
      return this.subTotal + this.pajak;
    },
  },
};
</script>

<style scoped>
.img-cart {
  width: 100px;
  height: 100px;
}
</style>