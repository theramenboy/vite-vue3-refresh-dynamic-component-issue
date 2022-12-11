<script setup lang="ts">
import Grid from './components/Grid.vue';
import Column from './components/Column.vue';
import Input from './components/Input.vue';
import { ref, onMounted } from 'vue';

const array = ref<Array<ArrayType>>([]);

onMounted(() => {
  array.value = [
    {
      a: '',
      b: '',
      c: '',
      isSelected: false,
    },
    {
      a: '',
      b: '',
      c: '',
      isSelected: false,
    },
    {
      a: '',
      b: '',
      c: '',
      isSelected: false,
    },
  ];
});

function selectField(row: any) {
  // DO SOMETHING
}

interface ArrayType {
  a: string;
  b: string;
  c: string;
  isSelected: boolean;
}
</script>

<template lang="pug">
div(v-if="array != null && array.length > 0")
  Grid(:rows="array" :selectable="true" @row-selected="selectField($event)")
    Column(header="Field A")
      template(v-slot:body="{ data }")
        Input(V-model="data.a")
    Column(header="Field B")
      template(v-slot:body="{ data }")
        Input(V-model="data.b")
    Column(header="Field C")
      template(v-slot:body="{ data }")
        Input(V-model="data.c")
</template>

<style scoped></style>
