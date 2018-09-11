<template>
  <nav class="pagation-nav">
    <span class="pagation-action pagation-prev"
      @click="prevPage"
      v-if="currentPage > 1">← </span>
    <template>
     <span v-if="totalIndex < total.length && totalIndex >= 1" @click="totalIndex += -1"  class="pagation-num"> ... </span>
      <span v-for="(num, index) in total[totalIndex]"
        :key="index"
        >
         <span
          class="pagation-num"
          @click="changeCurrentPage(num)"
          :class="hightlightCurrentPage(num)">
          {{ num }}
        </span>
      </span>
     <span v-if="totalIndex +1 != total.length" @click="totalIndex += 1" class="pagation-num"> ... </span>

    </template>
    <span class="pagation-action pagation-next"
      @click="nextPage"
      v-if="currentPage < pageSum"> →</span>
  </nav>
</template>

<script>
import navLayoutMixin from '../lib/navLayout.mixin'
export default {
  mixins: [navLayoutMixin],
  props: {
    pageItems: {
      default: () => []
    }
  },
  data() {
    return {
      currentPage: 1,
      pageArrIndex: 10,
      totalIndex:0,
    }
  },
  methods: {
     slice(array, start, end) {
      let length = array == null ? 0 : array.length
      if (!length) {
        return []
      }
      start = start == null ? 0 : start
      end = end === undefined ? length : end

      if (start < 0) {
        start = -start > length ? 0 : (length + start)
      }
      end = end > length ? length : end
      if (end < 0) {
        end += length
      }
      length = start > end ? 0 : ((end - start) >>> 0)
      start >>>= 0

      let index = -1
      const result = new Array(length)
      while (++index < length) {
        result[index] = array[index + start]
      }
      return result
    },
    chunk(array, size) {
      size = Math.max(size, 0)
      const length = array == null ? 0 : array.length
      if (!length || size < 1) {
        return []
      }
      let index = 0
      let resIndex = 0
      const result = new Array(Math.ceil(length / size))

      while (index < length) {
        result[resIndex++] = this.slice(array, index, (index += size))
      }
      return result
    },
    changeCurrentPage(pageNum) {
      window.scrollTo(0, 0);
      this.currentPage = pageNum
    },
    prevPage() {
      this.currentPage = this.currentPage - 1
    },
    nextPage() {
      this.currentPage = this.currentPage + 1
    },
    hightlightCurrentPage(pageNum) {
      return {
        'pagation-current': pageNum === this.currentPage
      }
    },
  },
  computed: {
    pageSum() {
      return Math.ceil(this.pageItems.length / this.perPage)
    },
    total() {
      let pageArr = [...new Array(this.pageSum).keys()]
      pageArr = pageArr.map(i=>i+=1)
      return this.chunk(pageArr,this.pageArrIndex)
    },
  },
  watch: {
    currentPage(pageNum) {
      this.totalIndex = parseInt((this.currentPage-1) / this.pageArrIndex)

      this.$emit('change', pageNum)
    }
  }
}
</script>

<style lang="stylus">
.pagation-nav
  padding 1rem
  text-align center
  clear both
  line-height 2
  overflow hidden
  span:hover
    color #000

.pagation-action
  display block
  text-align center
  cursor pointer
  width 2rem
  height 2rem
  border-radius 50%
  background-color #fff
  box-shadow 0 1px 3px rgba(26,26,26,0.1)

.pagation-num
  cursor pointer
  padding 0.2rem 0.4rem
  line-height 1
  height 2ex

.pagation-prev
  float left

.pagation-next
  float right

.pagation-current
  font-weight 800
  color #333
  font-size 20px
</style>
