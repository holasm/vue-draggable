<template>
<swapable :adjustable='true'>
  <h1 class="header">Self adjusting</h1>
</swapable>
<swapable :adjustable='false'>
  <h1 class="header">Non adjsuting</h1>
</swapable>
</template>

<script>
import Swapable from './Swapable'

export default {
  components: {
    Swapable
  }
}
</script>

<style lang='scss'>
.header{
  padding: 10px 32px;
}
</style>