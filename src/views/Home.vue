<template>
  <div class="home">
    <sunburst/>
  </div>
</template>

<script>
// @ is an alias to /src
import Sunburst from '../components/Sunburst.vue';

export default {
  name: 'home',
  components: {
    Sunburst,
  },
};
</script>
