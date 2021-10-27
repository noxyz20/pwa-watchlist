<template>
  <div
    style="
      padding: 2rem;
      display: flex;
      flex-direction: column;
      width: 100vw;
      align-items: center;
    "
    text-center
  >
    <!-- Page d'accueil<br /> -->
    <!-- {{ this.myWatchlist }} -->
    <!-- <v-hover>
      <template v-slot:default="{ hover }"> -->
    <div class="card-film">
      <v-card
        v-for="(item, index) in myWatchlist"
        v-bind:key="item.id"
        style="
          width: 200px;
          display: flex;
          flex-direction: column;
          justify-content: center;
          margin: 1rem;
        "
      >
        <v-img
          hover
          :src="
            item.url != null
              ? 'https://image.tmdb.org/t/p/w342/' + item.url
              : 'https://via.placeholder.com/250x350'
          "
          style="padding: 0 1rem"
        ></v-img>
        <p v-text="item.title"></p>
        <!-- <v-fade-transition>
              <v-overlay v-if="hover" absolute color="#036358">
                <v-btn @click="removeItem(item.id, index)">Supprimer</v-btn>
              </v-overlay>
            </v-fade-transition> -->
        <v-btn class="error" @click="removeItem(item.id, index)"
          >Supprimer</v-btn
        >
      </v-card>
    </div>
    <!-- </template>
    </v-hover> -->
  </div>
</template>

<script>
export default {
  name: "home",
  data: () => ({
    myWatchlist: [],
  }),
  methods: {
    removeItem(id, index) {
      var currWatchlist = JSON.parse(localStorage.getItem("watchlist"));

      console.log("Film à supprimer : " + id + "\nindex : " + index);
      currWatchlist.splice(index, 1);
      this.myWatchlist = currWatchlist;

      localStorage.setItem("watchlist", JSON.stringify(currWatchlist));
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
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  width: 100vw;
  -webkit-box-orient: horizontal;
  -webkit-box-direction: normal;
  -ms-flex-direction: row;
  flex-direction: row;
  margin: 0 !important;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
  -webkit-box-pack: start;
  -ms-flex-pack: start;
  justify-content: flex-start;
  padding: 1rem;
}
.v-application p {
  display: flex;
  margin: 0.5rem;
  justify-content: center;
  align-items: center;
  text-align: center;
}
</style>
