<template>
  <v-app>
    <v-app-bar app color="blue" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <h3>Countrify</h3>
      </div>

      <v-spacer></v-spacer>
      <v-btn
        depressed
        text
        elevation="6"
        :disabled="error || tableOpen"
        v-on:click="tableOpen = true"
      >
        Try it
      </v-btn>
    </v-app-bar>

    <v-main class="mt-2 mb-2">
      <v-alert v-if="error" prominent type="error">
        <v-row align="center">
          <v-col class="grow">
            Some kind of error happened while fetching data.
          </v-col>
          <v-col class="shrink">
            <v-btn v-on:click="getData">Try again</v-btn>
          </v-col>
        </v-row>
      </v-alert>
      <main-component v-if="tableOpen" :countries="countries" />
    </v-main>
  </v-app>
</template>

<script>
import MainComponent from "./components/main.component.vue";

export default {
  name: "App",

  components: {
    MainComponent,
  },

  data: function () {
    return {
      countries: [],
      error: false,
      tableOpen: false,
    };
  },
  methods: {
    getData() {
      this.error = false;
      fetch("https://countriesnow.space/api/v0.1/countries/flag/images")
        .then((res) => {
          return res.json();
        })
        .then((data) => {
          this.countries = data.data;
        })
        .catch((e) => {
          this.error = true;
          console.log(e);
        });
    },
  },
  created() {
    this.getData();
  },
};
</script>

<style lang="scss">

html {
  height: 100%;
}

body {
  min-height: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  color: rgba(0, 0, 0, 0.6);
  padding: 1.25rem;
}


#app {
  height: 100%;
}

.viewport {
  background: #fefefe;
  overflow-y: auto;
}

.spacer > div {
  padding: 0.5rem 0rem;
  border: 1px solid #f5f5f5;
}
.root {
  overflow-y: auto;
  overflow-x: hidden;
}
</style>
