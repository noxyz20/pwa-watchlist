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
    <v-card style="width: 600px" color="indigo lighten" dark>
      <v-card-title class="text-h5 indigo lighten-1">
        Rechercher une oeuvre
      </v-card-title>
      <v-card-text>
        <!-- Explore hundreds of free API's ready for consumption! For more
        information visit
        <a
          class="grey--text text--lighten-3"
          href="https://github.com/toddmotto/public-apis"
          target="_blank"
          >the GitHub repository</a
        >. -->
      </v-card-text>
      <v-card-text>
        <v-autocomplete
          v-model="model"
          :items="items"
          :loading="isLoading"
          :search-input.sync="search"
          color="white"
          clearable
          hide-no-data
          hide-selected
          label="Liste des oeuvres"
          placeholder="Rechercher une oeuvre.."
          prepend-icon="mdi-database-search"
          return-object
        ></v-autocomplete>
      </v-card-text>
      <v-divider></v-divider>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="grey darken-3" @click="clearButton()">
          Reinitialiser
          <v-icon right> mdi-close-circle </v-icon>
        </v-btn>
      </v-card-actions>
    </v-card>
    <!-- <v-hover> -->
    <!-- <template v-slot:default="{ hover }"> -->
    <div class="card-film">
      <v-card
        class="myCard"
        v-for="(item, index) in items"
        v-bind:key="index"
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
            item.poster_path != null
              ? 'https://image.tmdb.org/t/p/w342/' + item.poster_path
              : 'https://via.placeholder.com/250x350'
          "
          style="padding: 0 1rem"
        ></v-img>
        <p v-text="item.name || item.title"></p>
        <!-- <v-fade-transition>
              <v-overlay v-if="hover" absolute color="#036358">
                <v-btn
                  @click="
                    addItem(
                      item,
                      item.id,
                      item.poster_path,
                      item.name || item.title
                    )
                  "
                  >Ajouter</v-btn
                >
              </v-overlay>
            </v-fade-transition> -->
        <v-btn
          class="success"
          @click="
            addItem(item, item.id, item.poster_path, item.name || item.title)
          "
          >Ajouter</v-btn
        >
      </v-card>
    </div>
    <!-- </template> -->
    <!-- </v-hover> -->
  </div>
</template>
<script>
export default {
  name: "search",
  data: () => ({
    descriptionLimit: 60,
    results: [],
    overlay: false,
    isLoading: false,
    model: null,
    search: null,
    myVarItems: null,
    myWatchlist: [],
  }),

  methods: {
    clearButton() {
      // model = null;
      this.results = [];
    },
    addItem(item, id, url, name, title) {
      // console.log(item);
      // console.log("id : " + id);
      var myTitle;
      if (name != null) {
        myTitle = name;
        // console.log("name : " + myTitle);
      } else {
        myTitle = title;
        // console.log("title : " + myTitle);
      }
      url = url;
      // console.log("url :" + url);
      // console.log(this.myWatchlist);
      var myCurrentWatchlist = JSON.stringify(this.myWatchlist);
      // console.log(myCurrentWatchlist.includes(id))
      if (myCurrentWatchlist.includes(id)) {
        console.log("Film déjà ajouté !");
      } else {
        this.myWatchlist.push({ item: item, id: id, title: myTitle, url: url });
        localStorage.setItem("watchlist", JSON.stringify(this.myWatchlist));
        console.log("Film ajouté : " + item.id);
      }
    },
  },

  computed: {
    fields() {
      if (!this.model) return [];

      return Object.keys(this.model).map((key) => {
        return {
          key,
          value: this.model[key] || "n/a",
        };
      });
    },
    items(val) {
      return this.results.map((entry) => {
        // const title = entry.title.length > this.descriptionLimit
        //   ? entry.title.slice(0, this.descriptionLimit) + '...'
        //   : entry.title

        const title = entry.title;
        // if (val != "") {
        return Object.assign({}, entry, { title });
        // }
      });
    },
  },
  beforeMount() {
    // Si aucun sentier n'a jamais été téléchargé
    if (
      localStorage.getItem("watchlist") == "" ||
      localStorage.getItem("watchlist") == null ||
      localStorage.getItem("watchlist") == "[]"
    ) {
      localStorage.setItem("watchlist", JSON.stringify(this.myWatchlist));
    } else {
      this.myWatchlist = JSON.parse(localStorage.getItem("watchlist"));
    }
  },
  watch: {
    // items(val) {
    //   console.log(val);
    //   // Si on click en dehors
    //   if (val == null) {
    //     this.items = this.myVarItems;
    //   } else {
    //     this.myVarItems = val;
    //   }
    // },
    search(val) {
      // Items have already been loaded
      // if (this.items.length > 0) return

      // Items have already been requested
      if (this.isLoading) return;

      if (val != null) {
        this.isLoading = true;

        // Lazily load input items
        fetch(
          "https://api.themoviedb.org/3/search/multi?api_key=650af1e6bf1ba3da24d035e79fef613a&language=fr&query=" +
            val +
            "&page=1&include_adult=false"
        )
          // fetch('https://api.publicapis.org/results')
          .then((res) => res.json())
          .then((res) => {
            const { count, results } = res;

            this.count = count;
            this.results = results;
            // console.log(this.count);
          })
          .catch((err) => {
            console.log(err);
          })
          .finally(() => (this.isLoading = false));
      }
    },
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
