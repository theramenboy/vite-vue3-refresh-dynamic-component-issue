<script setup lang="ts">
import Grid from './components/Grid.vue';
import Column from './components/Column.vue';
import Input from './components/Input.vue';
import { ref, onMounted } from 'vue';
import { v4 as uuidv4} from "uuid"

const array = ref<Array<ArrayType>>([]);
const array2 = ref<Array<ArrayType>>([]);
const example = ref<ArrayType>({
  id: uuidv4(),
  a: "",
  b: "",
  c: "",
  isSelected: false,
})

onMounted(() => {
  array.value = [new ArrayType(), new ArrayType(), new ArrayType()];

  array2.value = [new ArrayType(), new ArrayType(), new ArrayType()];
});

function selectRow(row: ArrayType) {
  array2.value.forEach(a => {
    if(row.id == a.id) {
      if(a.isSelected) return;
      else a.isSelected = true;
    }
    else {
      a.isSelected = false;
    }

  })
}

class ArrayType {
  id: string;
  a: string;
  b: string;
  c: string;
  isSelected: boolean;

  constructor() {
    this.id = uuidv4();
    this.a = "";
    this.b = "";
    this.c = "";
    this.isSelected = false;
  }
}
</script>

<template lang="pug">
div(v-if="array != null && array.length > 0")
  div: strong CUSTOM COMPONENT WITH SLOTS 
  div: em The issue here is that if I click on the input, first the focus is correct, but than the CSS of the parent div in "Grid.vue" is apply and the dynamic component REFRESH losing the focus
  div: em What can I do to resolve this to be like the example below ? 
  br
  Grid(:rows="array" :selectable="true" :toggle-selected-row="false")
    Column(header="")
      template(v-slot:header)
        div Custom Field A Header
      template(v-slot:body="{ data }")
        Input(v-model="data.a")
    Column(header="Field B")
      template(v-slot:body="{ data }")
        Input(v-model="data.b")
    Column(header="Field C")
      template(v-slot:body="{ data }")
        Input(v-model="data.c")
  br
  br
  div: strong WHAT I WANT
  div: em I'd like that if I click on the input, the div click set the object to selected, the row is marked with the "row-selected" class and the input stay focused
  div: em This is made with a simple DIV and a custom Input component. I'd like this behavior within my Grid component and the usage of the slots with Column component. 
  br
  div(v-for="arr in array2" :class="{ 'row-selected': arr.isSelected}" style="display: flex; gap: 4px; padding: 5px; cursor: pointer; border-bottom: 1px solid" @click="selectRow(arr)")
    div: Input(:id="arr.id" v-model="arr.a")
    div: Input(v-model="arr.b")
    div: Input(v-model="arr.c")
</template>

<style>
.row-selected {
  background-color: lightgray;
}
</style>
