<script setup lang="ts">
import type { PropType } from 'vue';
import { v4 as uuidv4 } from 'uuid';

defineProps({
  id: {
    type: String,
    required: false,
    default: () => uuidv4(),
  },
  header: {
    type: String,
    required: false,
    default: () => '',
  },
  selectable: {
    type: Boolean,
    required: false,
    default: true,
  },
  data: {
    type: Object as PropType<any>,
    required: false,
  },
});
</script>

<template lang="pug">
slot(name="header")
slot(name="body" :data="data")
</template>
