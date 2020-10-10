<template>
  <li ref="item" class="item">
    <slot :item="itemdata"></slot>
  </li>
</template>
<script>
  export default {
    name: 'VirtualItem',
    props: ['itemdata', 'index'],
    mounted() {
      this.$emit('creatEl', this.$refs.item, this.index)
    }
  }
</script>
