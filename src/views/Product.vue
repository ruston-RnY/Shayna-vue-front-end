<template>
  <div class="home">
    <HeaderShayna />
    <Breadcrumb />

    <!-- Product Shop Section Begin -->
    <section class="product-shop spad page-details text-left">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="row">
              <div class="col-lg-6">
                <div class="product-pic-zoom">
                  <img
                    class="product-big-img"
                    :src="pic_default"
                    alt=""
                    style="height: 470px"
                  />
                </div>
                <div
                  class="product-thumbs"
                  v-if="productDetails.galleries.length > 0"
                >
                  <carousel class="product-thumbs-track ps-slider" :nav="false">
                    <div
                      v-for="ss in productDetails.galleries"
                      :key="ss.id"
                      class="pt"
                      @click="changeImage(ss.photo)"
                      :class="ss.photo == pic_default ? 'active' : ''"
                    >
                      <img :src="ss.photo" alt="" />
                    </div>
                  </carousel>
                </div>
              </div>
              <div class="col-lg-6">
                <div class="product-details">
                  <div class="pd-title">
                    <span>{{ productDetails.type }}</span>
                    <h3>{{ productDetails.name }}</h3>
                  </div>
                  <div class="pd-desc mt-4">
                    <p v-html="productDetails.description"></p>
                    <h4>Rp. {{ productDetails.price }}</h4>
                  </div>
                  <div class="quantity">
                    <router-link to="/shoping_cart">
                      <a
                        href="#"
                        class="primary-btn pd-cart"
                        @click="
                          saveKeranjang(
                            productDetails.id,
                            productDetails.name,
                            productDetails.price,
                            productDetails.galleries[0].photo
                          )
                        "
                        >Add To Cart</a
                      >
                    </router-link>
                    <!-- <router-link to="/shoping_cart" class="primary-btn pd-cart"
                      >Add To Cart</router-link
                    > -->
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- Product Shop Section End -->

    <Related />
    <FooterShayna />
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";
import HeaderShayna from "@/components/HeaderShayna.vue";
import FooterShayna from "@/components/FooterShayna.vue";
import Breadcrumb from "@/components/Breadcrumb.vue";
import Related from "@/components/Related.vue";
import carousel from "vue-owl-carousel";
import axios from "axios";

export default {
  name: "product",
  components: {
    HeaderShayna,
    FooterShayna,
    Breadcrumb,
    Related,
    carousel,
  },
  data() {
    return {
      pic_default: "",
      productDetails: [],
      keranjangUser: [],
    };
  },
  methods: {
    changeImage(urlImage) {
      this.pic_default = urlImage;
    },
    setDataPicture(data) {
      // untuk mengisi fungsi setDataPicture dengan data dari API
      this.productDetails = data;
      // untuk mengisi var pic_default dengan data dari API
      this.pic_default = data.galleries[0].photo;
    },
    saveKeranjang(idProduct, nameProduct, priceProduct, photoProduct) {
      var productStored = {
        id: idProduct,
        name: nameProduct,
        price: priceProduct,
        photo: photoProduct,
      };

      this.keranjangUser.push(productStored);
      const parsed = JSON.stringify(this.keranjangUser);
      localStorage.setItem("keranjangUser", parsed);
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
    axios
      .get("http://localhost:8000/api/products", {
        params: {
          id: this.$route.params.id,
        },
      })
      .then((res) => this.setDataPicture(res.data.data))
      // eslint-disable-next-line no-console
      .catch((err) => console.log(err));
  },
};
</script>

<style scoped>
.pt {
  margin: 5px;
}
</style>
