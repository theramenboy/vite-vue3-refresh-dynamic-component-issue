<script setup lang="ts">
import type { PropType } from 'vue';
import type { ThDataGridRow } from './th-data-grid-model';
import { v4 as uuidv4 } from 'uuid';
import { computed } from 'vue';

const props = defineProps({
  modelValue: String,
});

const emit = defineEmit(['update:modelValue']);

const privateValue = computed({
  get: () => props.modelValue,
  set: (val: string) => {
    emit('update:modelValue', val);
  },
});
</script>

<template lang="pug">
input(type="text" v-model="privateValue")
</template>
