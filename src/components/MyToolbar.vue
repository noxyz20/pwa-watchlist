<template>
  <div>
    <!-- <v-navigation-drawer v-model="drawer" app>
      <myContentDrawer />
    </v-navigation-drawer> -->

    <v-app-bar class="indigo" clipped-left app>
      <!-- <v-app-bar-nav-icon dark @click.stop="drawer = !drawer"></v-app-bar-nav-icon> -->
      <div class="myFlex">
        <div class="myMyFlex">
          <div @click="goHome" class="white--text" style="cursor: pointer">
            Watchlist
          </div>
          <div @click="goSearch" class="white--text" style="cursor: pointer">
            Search
          </div>
        </div>
      </div>
    </v-app-bar>
  </div>
</template>

<script>
import myContentDrawer from "@/components/Drawer";

export default {
  name: "myToolbar",
  components: { myContentDrawer },
  data: function () {
    return {
      drawer: false,
    };
  },
  mounted: function () {
    // Listen for event openDrawer (triggered by other component, like the button in the home)
    document.addEventListener("toggleDrawer", this.toggleDrawer);
  },
  beforeDestroy: function () {
    // If the component is unmount, unlisten the event.
    document.removeEventListener("toggleDrawer", this.toggleDrawer);
  },
  methods: {
    toggleDrawer() {
      this.drawer = !this.drawer;
    },
    goHome() {
      window.location.hash = "/";
    },
    goSearch() {
      window.location.hash = "/search";
    },
  },
};
</script>

<style scoped>
.myFlex {
  width: 100vw;
  display: flex;
  justify-content: center;
}
.myMyFlex {
  display: flex;
  width: 200px;
  justify-content: space-between;
}
</style>
