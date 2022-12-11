<script setup lang="ts">
import type { PropType } from 'vue';
import { v4 as uuidv4 } from 'uuid';
import { computed, useSlots, ref } from 'vue';

export interface ThDataGridRow {
  isSelected: boolean;
  id: string;
}

export interface IThDataGridColumn {
  props?: {
    header: string | null;
    selectable: boolean | null;
    id: string | null;
  };
  children?: { body: any; header: any };
}

const props = defineProps({
  rows: {
    type: Array as PropType<Array<ThDataGridRow & any>>,
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

  const results = new Array<IThDataGridColumn>();

  cols.forEach((a) => {
    if (a.props?.header != null) results.push(a as any as IThDataGridColumn);

    if (a.children && a.children.length) {
      (a.children as any).forEach((c: any) => {
        results.push(c as any as IThDataGridColumn);
      });
    }
  });

  return results;
});

const getGridCol = computed(() => {
  return `display: grid; grid-template-columns: repeat(${columns.value.length}, auto); gap: 2`;
});

function getHeaderStyle(column: IThDataGridColumn) {
  return `display: flex; align-items: center; border-bottom: 2px;background-color: white; z-index: 1;`;
}

const getBodyStyle = computed(() => {
  const selectableStyle = props.selectable ? 'cursor: pointer; ' : '';
  return `display: flex; align-items: center; border-bottom: 1px;${selectableStyle}min-height: 45px;`;
});

function onRowSelected(row: ThDataGridRow, col: IThDataGridColumn) {
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

function getDivKey(row: ThDataGridRow, col: IThDataGridColumn) {
  let id1 = uuidv4();
  let id2 = uuidv4();
  return `${row?.id ?? id1}-${col.props?.id ?? id2}`;
}
</script>

<template lang="pug">
div(:style="getGridCol" style="overflow: auto;")
	div(v-for="(col, i) in columns" :style="getHeaderStyle(col)" :key="i")
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
