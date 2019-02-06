<template>
  <div class="treemap">
    <treemap/>
  </div>
</template>
<script>
// @ is an alias to /src
import Treemap from '../components/Treemap.vue';

export default {
  name: 'about',
  components: {
    Treemap,
  },
};
</script>

<style lang="scss">
.treemap {
  margin: auto;
  width: 960px;
}
</style>
