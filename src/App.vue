<!-- eslint-disable no-undef -->
<!-- eslint-disable no-undef -->
<template>
  <main>
    <nav class="navbar">
      <img src="/src/assets/svg/logo.svg" class="navbar__logo" alt="" />
    </nav>

    <section class="section__block">
      <LoadingComponent v-if="isLoading" />
    </section>

    <footer class="navbar">
      <img src="/src/assets/svg/footer-logo.svg" class="navbar__footer--logo" />
    </footer>
  </main>
</template>
<script>
import { ref, reactive, computed } from "vue";
import speedTestService from "./speedTest";
// import { useGeolocation } from "./useGeolocation.js";
import LoadingComponent from "./components/LoadingComponent.vue";
import cfDataCenters from './assets/json/cfDataCenters.json';
export default {
  components: {
    LoadingComponent,
    MapsComponent,
    SpeedometerComponent,
  },

  setup() {
    const service = new speedTestService();
    const speedTest = reactive({
      download: "",
      upload: "",
      ping: "",
    });
    const isLoading = ref(true);
    const speedBlockLoading = ref(true);
    const locationData = reactive({
      ip: "",
      network: "",
      userLocation: "",
      serverLocation: "",
    });

    // const { coords } = useGeolocation();


    return {
      service,
      speedTest,
      isLoading,
      speedBlockLoading,
      locationData,
      userPos,
      serverPos,
      downloadText,
      uploadText,
      pingText,
    };
  },

  async mounted() {
    this.service.addressSpeed().then((data) => {
      const serverLocation = cfDataCenters[data.colo];
      this.locationData = {
        ip: data.remoteAddress,
        network: data.asOrganization,
        userLocation: `${data.city}, ${data.region}`,
        serverLocation: serverLocation ? `${serverLocation.city}, ${serverLocation.country}` : data.colo,
      }
      this.userPos = {
        lat: +data.latitude,
        lng: +data.longitude,
      }
      this.serverPos = serverLocation ? {
        lat: serverLocation.lat,
        lng: serverLocation.lon,
      } : undefined;
    });

    await new Promise(resolve => setTimeout(resolve, 3000));
    this.isLoading = false;
    this.startSpeedTest();
  

    // setTimeout(() => {
    //   this.locationData = {
    //     ip: "192.108.0.1",
    //     network: "Ligga Telecom",
    //     userLocation: "Passo do Vigário, RS",
    //     serverLocation: "Porto Alegre, RS",
    //   };
    // }, 5000);

    // setTimeout(async () => {
    //   this.speedBlockLoading = false;
    //   this.speedTest = {
    //     download: "24.2",
    //     upload: "14.3",
    //     ping: "20.4",
    //   };
    // }, 7000);    
  },

  updated() {
    // console.log(this.userPos, this.styles);
  },

  methods: {
    restartTest() {
      this.service.abortController.abort();
      this.service.abortController = new AbortController();
      this.speedBlockLoading = true;
      this.startSpeedTest();
    },
  },
};
</script>
<style>
.v-popper--theme-tooltip .v-popper__inner {
  background: #f6ae2d !important;
  color: black !important;
  /* padding: 24px; */
  border-radius: 6px;
  /* border: 1px solid #ddd; */
  box-shadow: 0 6px 30px rgba(0, 0, 0, 0.1);
  font-size: 11px;
  max-width: 400px;
  white-space: pre-line;
}
.v-popper--theme-tooltip .v-popper__arrow-outer {
  border-color: #f6ae2d !important;
}
</style>
