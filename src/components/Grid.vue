<script setup lang="ts">
import type { PropType } from 'vue';
import { v4 as uuidv4 } from 'uuid';
import { computed, useSlots, ref } from 'vue';

export interface GridRow {
  isSelected: boolean;
  id: string;
}

export interface GridColumn {
  props?: {
    header: string | null;
    selectable: boolean | null;
    id: string | null;
  };
  children?: { body: any; header: any };
}

const props = defineProps({
  rows: {
    type: Array as PropType<Array<GridRow & any>>,
    required: true,
  },
  selectable: {
    type: Boolean,
    required: false,
    default: false,
  },
  multipleSelect: {
    type: Boolean,
    required: false,
    default: false,
  },
  toggleSelectedRow: {
    type: Boolean,
    required: false,
    default: true,
  },
});

const emit = defineEmits(['row-selected']);

const slots = useSlots();

const columns = computed(() => {
  const cols = slots.default ? slots.default() : [];

  const results = new Array<GridColumn>();

  cols.forEach((a) => {
    if (a.props?.header != null) results.push(a as any as GridColumn);

    if (a.children && a.children.length) {
      (a.children as any).forEach((c: any) => {
        results.push(c as any as GridColumn);
      });
    }
  });

  return results;
});

const geyColStyle = computed(() => {
  return {
    display: "grid",
    "grid-template-columns": `repeat(${columns.value.length}, auto)`,
    gap: 2,
    overflow: "auto"
  }
});

const getHeaderStyle = computed(() => {
  return {
    display: "flex",
    "align-items": "center",
    "border-bottom": "2px solid",
    "background-color": "white",
    "z-index": 1
  }
})

const getBodyStyle = computed(() => {
  return {
    display: "flex",
    "align-items": "center",
    "border-bottom": "1px solid",
    "min-height": "45px",
    cursor: props.selectable ? "pointer" : ""
  }
});

function onRowSelected(row: GridRow, col: GridColumn) {
  if (!props.selectable) return;
  if (!(col.props?.selectable ?? true)) return;

  if (!props.multipleSelect) {
    props.rows.forEach((a) => {
      if (a == row) return;
      a.isSelected = false;
    });
  }

  if (row.isSelected && !props.toggleSelectedRow) return;

  row.isSelected = !row.isSelected;

  if (row.isSelected) {
    emit('row-selected', row);
  }
}

function getDivKey(row: GridRow, col: GridColumn) {
  let id1 = uuidv4();
  let id2 = uuidv4();
  return `${row?.id ?? id1}-${col.props?.id ?? id2}`;
}
</script>

<template lang="pug">
div(:style="geyColStyle")
	div(v-for="(col, i) in columns" :style="getHeaderStyle" :key="i")
		div
			div(v-if="col.children?.header != null")
				component(:is="col.children.header")
			div(v-else): strong {{ col.props?.header }}

	template(v-for="(row, r) in rows")
		div(
			v-for="(col, c) in columns" 
			:style="getBodyStyle"
			@click="onRowSelected(row, col)" 
			:class="{'row-selected': row.isSelected && selectable }" 
			:key="r + '_' + c"
		)
			component(
        :key="getDivKey(row, col)"
				:is="col.children.body"
				:column="col"
				:data="row"
				v-if="col.children && col.children.body"
			)
</template>
