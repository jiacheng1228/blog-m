<template>
  <div class="search-suggestion">
    <van-cell
      icon="search"
      v-for="(suggestion, index) in searchSuggestions"
      :key="index"
      @click="$emit('suggestionSearch',suggestion)"
    >
        <span slot="title" v-html="highlight(suggestion)"></span>
    </van-cell>
  </div>
</template>

<script>
import { getSearchSuggestions } from '@/api/search'
import { debounce } from 'lodash'
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
    //   handler (value) {
    //     this.loadSearchSuggestions(value)
    //   },
      // debounce 函数
      // 参数1：一个函数
      // 参数2：延迟时间，单位是毫秒
      // 返回值：防抖之后的函数
      handler: debounce(function (value) {
        this.loadSearchSuggestions(value)
      }, 200),
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
    },
    highlight (suggestion) {
      const highlightStr = `<span class="active">${this.searchText}</span>`
      // 正则表达式 // 中间的内容都会当作匹配字符来使用，而不是数据变量
      // 如果需要根据数据变量动态的创建正则表达式，则手动 new RegExp
      // RegExp 正则表达式构造函数
      //    参数1：匹配模式字符串，它会根据这个字符串创建正则对象
      //    参数2：匹配模式，要写到字符串中
      const reg = new RegExp(this.searchText, 'gi')
      if (suggestion) {
        return suggestion.replace(reg, highlightStr)
      }
    }
  }
}
</script>

<style scoped lang="less">
.search-suggestion {
  /deep/ span.active {
    color: #3296fa;
  }
}
</style>
