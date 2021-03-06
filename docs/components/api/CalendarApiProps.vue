<template>
<div>
  <b-table
    :data='props'
    :row-class='(row) => row.class'>
    <template scope='props'>
      <b-table-column :style='{minWidth:"280px"}' label='Name'>
        <span v-html='props.row.name'></span>
      </b-table-column>
      <b-table-column label='Description'>
        <span v-html='props.row.description'></span>
      </b-table-column>
      <b-table-column label='Default'>
        <span v-html='props.row.default'></span>
      </b-table-column>
    </template>
  </b-table>
</div>
</template>

<script>
export default {
  data() {
    const props = [
      {
        name: '<code>month-labels: Array[String]</code>',
        description: 'Month labels displayed in header.',
        default: '<code>["January",...,"December"]</code>',
      },
      {
        name: '<code>weekday-labels: Array[String]</code>',
        description: 'Weekday labels displayed in header. Start with Sunday, even if it isn\'t set as the first day of the week',
        default: '<code>["S", "M", "T", "W", "T", "F", "S", "S"]</code>',
      },
      {
        name: '<code>first-day-of-week: Number</code>',
        description: 'Weekday number (1-7, Sun-Sat) to use as the first day of the week.',
        default: '<code>1</code>',
      },
      {
        name: '<code>from-page: Object</code>',
        description: 'Active page for single paned calendar or the left pane for double paned calendar. Use the <code>.sync</code> modifier for two-way binding.',
        default: '<code>{ month: <i>thisMonth</i>, year: <i>thisMonthYear</i> }</code>',
      },
      {
        name: '<code>to-page: Object</code>',
        description: 'Active page for the right pane for double paned calendar. Use the <code>.sync</code> modifier for two-way binding.',
        default: '<code>{ month: <i>nextMonth</i>, year: <i>nextMonthYear</i> }</code>',
      },
      {
        name: '<code>min-page: Object</code>',
        description: 'Earliest page (<i>month</i>, <i>year</i>) that the user can navigate to.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>max-page: Object</code>',
        description: 'Latest page (<i>month</i>, <i>year</i>) that the user can navigate to.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>is-double-paned: Boolean</code>',
        description: 'Puts two calendars side by side.',
        default: '<code>false</code>',
      },
      {
        name: '<code>wrap: Boolean</code>',
        description: 'If calendar is double-paned, wrap the panes when constrained.',
        default: '<code>false</code>',
      },
      {
        name: '<code>attributes: Array[Object]</code>',
        description: 'List of attributes to display in the calendar.',
        default: '<code>[]</code>',
      },
      {
        name: '<code>indicators-offset: String',
        description: 'Offset margin of indicators from bottom of day cell.',
        default: '<code>"0"</code>',
      },
      {
        name: '<code>class: Object</code>, <code>style: Object</code>',
        description: 'Typical class and style props. Used for calendar wrapper.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>pane-style: Object</code>',
        description: 'Style for calendar pane.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>header-style: Object</code>',
        description: 'Style for calendar header.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>arrow-style: Object</code>',
        description: 'Style for calendar header arrows.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>title-style: Object</code>',
        description: 'Style for calendar header title.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>title-transition: String</code>',
        description: 'Transition type for title when navigating to a new page: <code>"slide"</code>, <code>"fade"</code>, <code>"none"</code>',
        default: '<code>"slide"</code>',
      },
      {
        name: '<code>weekday-style: Object</code>',
        description: 'Style for weekday labels.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>weeks-transition: String</code>',
        description: 'Transition type for weeks when navigating to a new page: <code>"slide"</code>, <code>"fade"</code>, <code>"none"</code>',
        default: '<code>"slide"</code>',
      },
      {
        name: '<code>day-content-style: Object</code>',
        description: 'Style for day cell content.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>day-content-hover-style: Object</code>',
        description: 'This style is merged with the <code>day-content-style</code> prop when the content is hovered.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>day-height: String</code>',
        description: 'Height of the day cell container. Should be large enough to contain content and highlights.',
        default: '<code>"2.2rem"</code>',
      },
      {
        name: '<code>value: Date, Array[Date], Object</code>',
        description: '<span class="tag is-warning">Date Picker</span>Selected date, dates or date range.',
        default: '<code>null</code>',
      },
      {
        name: '<code>select-mode: String</code>',
        description: '<span class="tag is-warning">Date Picker</span>Selection mode: <code>"single"</code>, <code>"multiple"</code>, <code>"range"</code>',
        default: '<code>"single"</code>',
      },
      {
        name: '<code>disabled-dates: Array</code>',
        description: '<span class="tag is-warning">Date Picker</span>List of disabled dates or date range objects. Date ranges must specify <code>start</code> and <code>end</code> dates.',
        default: '<code>undefined</code>',
      },
      {
        name: '<code>select-attribute: Object</code>',
        description: '<span class="tag is-warning">Date Picker</span>Attribute to use for the date selection in all modes.',
        default: `<code>
        {
          highlight: {
            backgroundColor: '#74a4a4',
            borderWidth: '1px',
            borderColor: '#65999a'
          },
          contentStyle: {
            color: '#fafafa'
          },
          contentHoverStyle: {
            backgroundColor: 'transparent'
          }
        }
        </code>`,
      },
      {
        name: '<code>drag-attribute: Object</code>',
        description: '<span class="tag is-warning">Date Picker</span>Attribute to use for the dragged selection in <code>"range"</code> mode.',
        default: `<code>
        {
          highlight: {
            backgroundColor: '#c1d6d7',
            height: '25px'
          },
          contentStyle: {
            color: '#103456'
          },
          contentHoverStyle: {
            backgroundColor: 'transparent'
          }
        }
        </code>`,
      },
      {
        name: '<code>disabled-attribute: Object</code>',
        description: '<span class="tag is-warning">Date Picker</span>Attribute to use for disabled dates. If specified, you may include dates in this prop and ignore the <code>disabled-dates</code> prop.',
        default: `<code>
        {
          order: 100,
          contentStyle: {
            color: 'red',
            textDecoration: 'line-through'
          },
          contentHoverStyle: {
            cursor: 'not-allowed',
            backgroundColor: 'transparent'
          }
        }
        </code>`,
      },
    ];
    return {
      props,
    };
  },
};
</script>

<style lang='sass'>
.tag.is-warning
  margin-right: 10px
</style>
