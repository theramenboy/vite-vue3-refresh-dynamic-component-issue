<script setup lang="ts">
import { computed } from 'vue';

const props = defineProps({
  modelValue: {
    type: String,
    required: true,
  },
});

const emit = defineEmits(['update:modelValue']);

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
