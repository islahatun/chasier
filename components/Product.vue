<template>
  <div>
    <v-row align="center">
      <v-col cols="10">
        <v-autocomplete
          label="produk"
          placeholder="Ketikan yang ingin dicari"
          :search-input.sync="search"
          :loading="isLoading"
          :items="itemsSearch"
          item-text="title"
          item-value="id"
          v-model="selectedSearch"
          return-object
          hide-no-data
        >
        </v-autocomplete>
      </v-col>
      <v-col cols="2">
        <v-menu>
          <template v-slot:activator="{ on: category }">
            <v-btn v-on="category" color="primary"> Categori </v-btn>
          </template>
          <v-list>
            <v-list-item-group>
              <v-list-item
                v-for="(kategori, i) in categories"
                :key="i"
                :value="kategori.id"
                :disabled="kategori.id == categoryId"
                @chage="updateCategoryId(kategori.id)"
              >
                <v-list-item-title>{{ kategori.title }}</v-list-item-title>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-menu>
      </v-col>
    </v-row>
    <v-row>
      <v-col v-for="(item, i) in filterProducts" :key="i" cols="2">
        <v-card :title="item.title" :ripple="true">
          <v-card-actions>
            <v-img
              :src="require(`@/assets/image/products/${item.thumbnail}`)"
            ></v-img>
          </v-card-actions>
          <v-card-text align="center" class="product-title">
            {{ item.title }}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import { mapState, mapMutations } from 'vuex'

export default {
  data() {
    return {
      search: null,
      isLoading: false,
      itemsSearch: [],
      selectedSearch: null,
    }
  },
  methods: {
    ...mapMutations('products', {
      updateCategoryId: 'updateCategoryId',
    }),
    resetSearchCategoryId() {
      this.categoryId = false
    },
  },
  computed: {
    filterProducts() {
      if (this.categoryId) {
        return this.products.filter((s) => s.categoryId == this.categoryId)
      } else if (this.selectedSearch) {
        return this.products.filter((s) => s.title == this.selectedSearch.title)
      }
      return this.products
    },
    ...mapState('products', {
      products: 'products',
      categories: 'categories',
      categoryId: 'categoryId',
    }),
  },
  watch: {
    search(val) {
      this.isLoading = true
      setTimeout(() => {
        this.itemsSearch = this.products.filter((e) => {
          this.isLoading = false
          this.resetSearchCategoryId()
          return e.title
        })
      }, 1000)
    },
  },
}
</script>


<style scoped>
.product-title {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
