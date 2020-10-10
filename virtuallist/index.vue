<template>
  <div ref="conlist" class="listcontent" @scroll="handleScroll">
    <ul
      :style="{
        paddingTop: `${startOffset}px`,
        paddingBottom: `${endOffset}px`
      }"
    >
      <ItemLI
        v-for="item in visibleData"
        :key="item.id"
        v-slot="slotProps"
        :index="item.id"
        :itemdata="item"
        @creatEl="cachePosition"
      >
        <slot :item="slotProps.item"></slot>
      </ItemLI>
    </ul>
  </div>
</template>
<script>
  import ItemLI from './item.vue'
  let startIndex = 0
  let endIndex = 0
  let visibleCount = 0
  const cache = []
  export default {
    name: 'Virtuallist',
    components: {
      ItemLI
    },
    props: {
      singleHeight: {
        type: Number,
        default: 60
      },
      list: {
        type: Array,
        default: () => []
      },
      bufferSize: {
        type: Number,
        default: 5
      }
    },
    data() {
      return {
        doc: null,
        anchorItem: {
          index: 0,
          top: 0,
          bottom: 0
        },
        startOffset: 0,
        endOffset: 0,
        visibleData: [],
        scrollTop: 0
      }
    },
    mounted() {
      this.doc = this.$refs.conlist
      let { height } = this.doc.getBoundingClientRect()
      visibleCount = Math.ceil(height / this.singleHeight) + this.bufferSize
      endIndex = startIndex + visibleCount
      this.updateVisibleData()
    },
    methods: {
      updateVisibleData() {
        this.visibleData = this.list.slice(startIndex, endIndex)
        this.startOffset = startIndex * this.singleHeight
        this.endOffset = (this.list.length - endIndex) * this.singleHeight
      },
      cachePosition(node, index) {
        const rect = node.getBoundingClientRect()
        const top = rect.top + this.scrollTop
        cache.push({
          index,
          top,
          bottom: top + this.singleHeight
        })
      },
      updateBoundaryIndex(scrollTop) {
        scrollTop = scrollTop || 0
        const anchorItem = cache.find(item => item.bottom >= scrollTop)
        if (!anchorItem) return
        this.anchorItem = {
          ...anchorItem
        }
        startIndex = this.anchorItem.index
        endIndex = startIndex + visibleCount
      },

      // 滚动事件处理函数
      handleScroll(e) {
        if (!this.doc) return
        const { scrollTop } = this.doc
        if (scrollTop > this.scrollTop) {
          if (scrollTop > this.anchorItem.bottom) {
            this.updateBoundaryIndex(scrollTop)
            this.updateVisibleData()
          }
        } else if (scrollTop < this.scrollTop) {
          if (scrollTop < this.anchorItem.bottom) {
            this.updateBoundaryIndex(scrollTop)
            this.updateVisibleData()
          }
          // 向上滚动(`scrollTop` 值减小的方向)
        }
        this.scrollTop = scrollTop
      }
    }
  }
</script>
<style lang="less" scoped>
  .listcontent {
    width: 100%;
    height: 100%;
    overflow: auto;
  }
</style>
