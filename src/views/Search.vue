<template>
  <v-container text-center>
    <v-card color="red lighten-2" dark>
      <v-card-title class="text-h5 red lighten-3">
        Search for Public APIs
      </v-card-title>
      <v-card-text>
        Explore hundreds of free API's ready for consumption! For more
        information visit
        <a
          class="grey--text text--lighten-3"
          href="https://github.com/toddmotto/public-apis"
          target="_blank"
          >the GitHub repository</a
        >.
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
          item-text="title"
          item-value="id"
          label="Public APIs"
          placeholder="Start typing to Search"
          prepend-icon="mdi-database-search"
          return-object
        ></v-autocomplete>
      </v-card-text>
      <v-divider></v-divider>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="grey darken-3" @click="clearButton()">
          Clear
          <v-icon right> mdi-close-circle </v-icon>
        </v-btn>
      </v-card-actions>
    </v-card>
    <v-hover>
      <template v-slot:default="{ hover }">
        <div class="card-film">
          <v-card
            v-for="item in items"
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
              :src="'https://image.tmdb.org/t/p/w342/' + item.poster_path"
              width="50%"
              style="pading: 0 1rem"
            ></v-img>
            <p v-text="item.name || item.title"></p>
            <v-fade-transition>
              <v-overlay v-if="hover" absolute color="#036358">
                <v-btn
                  @click="
                    addItem(item.id, item.poster_path, item.name || item.title)
                  "
                  >See more info</v-btn
                >
              </v-overlay>
            </v-fade-transition>
          </v-card>
        </div>
      </template>
    </v-hover>
  </v-container>
</template>
<script>
export default {
  name: "search",
  data: () => ({
    descriptionLimit: 60,
    results: [],
    overlay: false,
    isLoading: false,
    // model: null,
    search: null,
    myVarItems: null,
    myWatchlist: [],
  }),

  methods: {
    clearButton() {
      // model = null;
      this.results = [];
    },
    addItem(id, url, name, title) {
      console.log("id : " + id);
      var myTitle;
      if (name != null) {
        myTitle = name;
        console.log("name : " + myTitle);
      } else {
        myTitle = title;
        console.log("title : " + myTitle);
      }
      url = "https://image.tmdb.org/t/p/w342" + url;
      console.log("url :" + url);
      if (this.myWatchlist.includes(id)) {
        console.log("Film déjà ajouté !");
      } else {
        this.myWatchlist.push({ id: id, title: myTitle, url: url });
        localStorage.setItem("watchlist", JSON.stringify(this.myWatchlist));
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
            console.log(this.count);
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
  display: flex;
  width: 100%;
  flex-direction: row;
  margin: 0 !important;
  flex-wrap: wrap;
}
</style>
