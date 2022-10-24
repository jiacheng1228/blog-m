<template>
  <div class="search-suggestion">
    <van-cell
      icon="search"
      v-for="(suggestion, index) in searchSuggestions"
      :key="index"
      :title="suggestion"
    />
  </div>
</template>

<script>
import { getSearchSuggestions } from '@/api/search'
export default {
  name: 'SearchSuggestion',
  data () {
    return {
      searchSuggestions: []
    }
  },
  props: {
    searchText: {
      type: String,
      required: true
    }
  },
  watch: {
    searchText: {
      // 当 searchText 发生改变的时候就会调用 handler 函数
      handler (value) {
        this.loadSearchSuggestions(value)
      },
      immediate: true
    }
  },
  methods: {
    async loadSearchSuggestions (q) {
      try {
        const { data } = await getSearchSuggestions(q)
        console.log(data)
        this.searchSuggestions = data.data.options
      } catch (error) {
        this.$toast('搜索建议获取失败')
      }
    }
  }
}
</script>

<style>
</style>
