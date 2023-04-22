<template>
  <TreeData />
</template>

<script lang="ts">

import { defineComponent } from 'vue';
import TreeData from './components/TreeData.vue';

export default defineComponent({
  name: 'App',
  components: {
    TreeData,
  }
});
</script>