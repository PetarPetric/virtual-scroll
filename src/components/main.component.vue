<template>
  <v-container class="mt-3">
    <div class="root" ref="root" :style="rootStyle">
      <div class="spacer" ref="spacer" :style="spacerStyle">
        <v-row
          class="d-flex justify-space-between pr-4 pl-4"
          v-for="country in visibleCountries"
          :key="country.iso3"
        >
          {{ country.name }}
          <v-img
            :alt="`${country.name} flag`"
            class="shrink"
            :src="country.flag"
            transition="scale-transition"
            width="40"
          />
        </v-row>
      </div>
    </div>
  </v-container>
</template>

<script>
const passiveSupportMixin = {
  methods: {
    doesBrowserSupportPassiveScroll() {
      let passiveSupported = false;

      try {
        const options = {
          get passive() {
            passiveSupported = true;
            return false;
          },
        };

        window.addEventListener("test", null, options);
        window.removeEventListener("test", null, options);
      } catch (err) {
        passiveSupported = false;
      }
      return passiveSupported;
    },
  },
};

export default {
  name: "MainComponent",
  mixins: [passiveSupportMixin],
  props: {
    countries: {
      type: Array,
      required: true,
    },
  },
  data: () => ({
    scrollTop: 0,
  }),
  computed: {
    rowHeight() {
      return this.rootHeight / 20;
    },
    rootHeight() {
      return window.innerHeight - 76;
    },
    viewportHeight() {
      return this.numberOfCountries * this.rowHeight;
    },
    startIndex() {
      let startNode = Math.floor(this.scrollTop / this.rowHeight);
      startNode = Math.max(0, startNode);
      return startNode;
    },
    visibleNodeCount() {
      let count = Math.ceil(this.rootHeight / this.rowHeight);
      count = Math.min(this.numberOfCountries - this.startIndex, count);
      return count;
    },
    visibleCountries() {
      return this.countries.slice(
        this.startIndex,
        this.startIndex + this.visibleNodeCount
      );
    },
    numberOfCountries() {
      return this.countries.length;
    },
    offsetY() {
      return this.startIndex * this.rowHeight;
    },
    spacerStyle() {
      return {
        transform: "translateY(" + this.offsetY + "px)",
      };
    },
    viewportStyle() {
      return {
        overflow: "hidden",
        height: this.viewportHeight + "px",
        position: "relative",
      };
    },
    rootStyle() {
      return {
        height: this.rootHeight + 20 + "px",
      };
    },
  },
  methods: {
    handleScroll(event) {
      console.log(event);
      this.scrollTop = this.$refs.root.scrollTop;
    },
  },
  mounted() {
    this.$refs.root.addEventListener(
      "scroll",
      this.handleScroll,
      this.doesBrowserSupportPassiveScroll() ? { passive: true } : false
    );
  },
  destroyed() {
    this.$refs.root.removeEventListener("scroll", this.handleScroll);
  },
};
</script>
