<template>
<b-table
  :data='props'>
  <template scope='props'>
    <b-table-column label='Name'>
      <span v-html='props.row.name'></span>
    </b-table-column>
    <b-table-column label='Description'>
      <span v-html='props.row.description'></span>
    </b-table-column>
    <b-table-column label='Parameters'>
      <span v-html='props.row.parameters'></span>
    </b-table-column>
  </template>
</b-table>
</template>

<script>
export default {
  data() {
    const props = [
      {
        name: '<code>dayClick</code>',
        description: 'Calendar day cell was clicked.',
        parameters: '<code>dayInfo: Object</code>',
      },
      {
        name: '<code>dayEnter</code>',
        description: 'Cursor entered a calendar day cell.',
        parameters: '<code>dayInfo: Object</code>',
      },
      {
        name: '<code>dayLeave</code>',
        description: 'Cursor left a calendar day cell.',
        parameters: '<code>dayInfo: Object</code>',
      },
      {
        name: '<code>updated:fromPage</code>',
        description: 'Calendar left/single pane moved to a different page.',
        parameters: '<code>page: Object</code>',
      },
      {
        name: '<code>updated:toPage</code>',
        description: 'Calendar right pane moved to a different page.',
        parameters: '<code>page: Object</code>',
      },
      {
        name: '<code>input</code>',
        description: '<span class="tag is-warning">Date Picker</span>New date was selected.',
        parameters: '<code>value: Date, Array[Date], Object</code>',
      },
      {
        name: '<code>drag</code>',
        description: '<span class="tag is-warning">Date Picker</span>Dragged selection was updated. Only valid for "range" selection mode.',
        parameters: '<code>range: Object</code>',
      },
    ];
    return {
      props,
    };
  },
};
</script>
