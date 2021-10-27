<template>
<v-container text-center>
  <v-card
    color="red lighten-2"
    dark
  >
    <v-card-title class="text-h5 red lighten-3">
      Search for Public APIs
    </v-card-title>
    <v-card-text>
      Explore hundreds of free API's ready for consumption! For more information visit
      <a
        class="grey--text text--lighten-3"
        href="https://github.com/toddmotto/public-apis"
        target="_blank"
      >the GitHub repository</a>.
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
      <v-btn
        :disabled="!model"
        color="grey darken-3"
        @click="model = null"
      >
        Clear
        <v-icon right>
          mdi-close-circle
        </v-icon>
      </v-btn>
    </v-card-actions>
  </v-card>
  <v-hover>
    <template v-slot:default="{ hover }">
  <div class="card-film">
    <v-card v-for="item in items" v-bind:key="item.id" style="width: 20%; display:flex; flex-direction:  column; justify-content: center;">
        <v-img hover :src="'https://image.tmdb.org/t/p/w342/'+ item.poster_path" width="50%" style="pading: 0 1rem"></v-img>
        <p v-text="item.name || item.title"></p>
         <v-fade-transition>
          <v-overlay
            v-if="hover"
            absolute
            color="#036358"
          >
            <v-btn :href="'/'+item.id">See more info</v-btn>
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
      model: null,
      search: null,
    }),

    computed: {
      fields () {
        if (!this.model) return []

        return Object.keys(this.model).map(key => {
          return {
            key,
            value: this.model[key] || 'n/a',
          }
        })
      },
      items () {
        return this.results.map(entry => {
          // const title = entry.title.length > this.descriptionLimit
          //   ? entry.title.slice(0, this.descriptionLimit) + '...'
          //   : entry.title
          
          const title = entry.title

          return Object.assign({}, entry, { title })
        })
      },
    },

    watch: {
      search (val) {
        // Items have already been loaded
        // if (this.items.length > 0) return

        // Items have already been requested
        if (this.isLoading) return

        this.isLoading = true

        // Lazily load input items
        fetch('https://api.themoviedb.org/3/search/multi?api_key=650af1e6bf1ba3da24d035e79fef613a&language=fr&query=' + val + '&page=1&include_adult=false')
        // fetch('https://api.publicapis.org/results')
          .then(res => res.json())
          .then(res => {
            const { count, results } = res
            this.count = count
            this.results = results
            console.log(this.count)
          })
          .catch(err => {
            console.log(err)
          })
          .finally(() => (this.isLoading = false))
      },
    },
  }
</script>

<style scoped>
.card-film {
  display: flex;
  width: 100%;
  flex-direction: row;
  margin:0 !important;
  flex-wrap: wrap;
}
</style>
