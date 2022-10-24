<template>
  <div class="search-container">
    <form action="/">
    <van-search
        v-model="value"
        show-action
        placeholder="请输入搜索关键词"
        background="#3296fa"
        @search="onSearch"
        @cancel="onCancel"
    />
    </form>

    <!-- 联想建议 -->
    <search-suggestion
    />
    <!-- /联想建议 -->

    <!-- 搜索历史记录 -->
    <search-history  />
    <!-- /搜索历史记录 -->

    <!-- 搜索结果 -->
    <search-result

    />
    <!-- /搜索结果 -->
  </div>
</template>

<script>
import SearchHistory from './components/search-history.vue'
import SearchSuggestion from './components/search-suggestion.vue'
import SearchResult from './components/search-result.vue'
export default {

  name: 'SearchIndex',
  data () {
    return {
      value: ''
    }
  },
  components: {
    SearchHistory,
    SearchSuggestion,
    SearchResult
  },
  methods: {
    onSearch (val) {
      this.$toast(val)
    },
    onCancel () {
      this.$router.back()
    }
  }

}
</script>

<style>

</style>
