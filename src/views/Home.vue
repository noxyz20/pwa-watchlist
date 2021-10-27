<template>
  <v-container text-center>
    <!-- Page d'accueil<br /> -->
    <!-- {{ this.myWatchlist }} -->
    <v-hover>
      <template v-slot:default="{ hover }">
        <div class="card-film">
          <v-card
            v-for="item in myWatchlist"
            v-bind:key="item.id"
            style="
              width: 20%;
              display: flex;
              flex-direction: column;
              justify-content: center;
            "
          >
            <v-img
              hover
              :src="item.url"
              width="50%"
              style="pading: 0 1rem"
            ></v-img>
            <p v-text="item.title"></p>
            <v-fade-transition>
              <v-overlay v-if="hover" absolute color="#036358">
                <v-btn @click="removeItem(item.id)">See more info</v-btn>
              </v-overlay>
            </v-fade-transition>
          </v-card>
        </div>
      </template>
    </v-hover></v-container
  >
</template>

<script>
export default {
  name: "home",
  data: () => ({
    myWatchlist: [],
  }),
  methods: {
    removeItem(id) {
      var currWatchlist = JSON.parse(localStorage.getItem("watchlist"));

      for (var i = 0; i < xlSentiers.length; i++) {
        var obj = xlSentiers[i];
        if (this.currSentier.indexOf(obj.id) !== -1) {
          xlSentiers.splice(i, 1);
        }
      }
    },
  },
  beforeMount() {
    // Si aucun sentier n'a jamais été téléchargé
    if (
      localStorage.getItem("watchlist") == "" ||
      localStorage.getItem("watchlist") == null ||
      localStorage.getItem("watchlist") == "[]"
    ) {
      console.log("watchlist vide, initialisation");
      localStorage.setItem("watchlist", JSON.stringify(this.myWatchlist));
    } else {
      console.log("Récupération de watchlist dans myWatchlist");
      this.myWatchlist = JSON.parse(localStorage.getItem("watchlist"));
    }
    console.log(this.myWatchlist);
  },
};
</script>

<style scoped>
.card-film {
  display: flex;
  width: 100%;
  flex-direction: row;
  margin: 0 !important;
  flex-wrap: wrap;
}
</style>
