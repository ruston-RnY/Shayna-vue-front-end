<template>
  <div class="home">
    <HeaderShayna />
    <HeroShayna />
    <WomenSection />
    <InstaSection />
    <LogoPartner />
    <FooterShayna />
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";
import HeaderShayna from "@/components/HeaderShayna.vue";
import HeroShayna from "@/components/HeroShayna.vue";
import WomenSection from "@/components/WomenSection.vue";
import InstaSection from "@/components/InstaSection.vue";
import LogoPartner from "@/components/LogoPartner.vue";
import FooterShayna from "@/components/FooterShayna.vue";

export default {
  name: "home",
  components: {
    HeaderShayna,
    HeroShayna,
    WomenSection,
    InstaSection,
    LogoPartner,
    FooterShayna,
  },
};
</script>
