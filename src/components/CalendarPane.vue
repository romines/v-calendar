<template>
  <div class='c-pane' :style='paneStyle'>
    <slot name='header' :page='page_'>
      <div class='c-header' :style='headerStyle'>
        <div class='c-arrow-layout'>
          <slot name='header-left-button' :page='page_'>
            <span
              class='c-arrow vc-angle-left'
              :class='{ "c-disabled": !canMovePrevMonth }'
              :style='arrowStyle'
              @click='movePrevMonth'>
            </span>
          </slot>
        </div>
        <transition-group
          tag='div'
          class='c-title'
          :name='titleTransition_'
          mode='out-in'>
          <div
            class='c-title-1'
            v-for='p in pages'
            :key='p.key'
            v-if='p === page_'>
            <div class='c-title-2'>
              <slot name='header-title' :page='p'>
                <span
                  class='c-title-3'
                  :style='titleStyle'
                  @click='moveThisMonth'>
                  {{ p.headerLabel }}
                </span>
              </slot>
            </div>
          </div>
        </transition-group>
        <div class='c-arrow-layout'>
          <slot name='header-right-button' :page='page_'>
            <span
              class='c-arrow vc-angle-right'
              :class='{ "c-disabled": !canMoveNextMonth }'
              :style='arrowStyle'
              @click='moveNextMonth'>
            </span>
          </slot>
        </div>
      </div>
    </slot>
    <div class='c-weekdays'>
      <div
        v-for='weekday in weekdayLabels_'
        :key='weekday'
        class='c-weekday'
        :style='weekdayStyle'>
        {{ weekday }}
      </div>
    </div> 
    <transition-group
      tag='div'
      class='c-weeks'
      :name='weeksTransition_'
      mode='out-in'>
      <calendar-weeks
        v-for='p in pages'
        :key='p.key'
        :month='p.month'
        :year='p.year'
        :isLeapYear='p.isLeapYear'
        :daysInMonth='p.daysInMonth'
        :firstWeekdayInMonth='p.firstWeekdayInMonth'
        :prevMonthComps='p.prevMonthComps'
        :nextMonthComps='p.nextMonthComps'
        :first-day-of-week='firstDayOfWeek'
        :day-background-color='dayBackgroundColor'
        v-if='p === page_'
        v-bind='$attrs'
        v-on='$listeners'>
      </calendar-weeks> 
    </transition-group> 
  </div>
</template>

<script>
/* eslint-disable camelcase */
import Vue from 'vue';
import CalendarWeeks from './CalendarWeeks';
import {
  todayComps,
  monthLabels,
  weekdayLabels,
  getIsLeapYear,
  getMonthComps,
  getThisMonthComps,
  getPrevMonthComps,
  getNextMonthComps,
  pageIsBeforePage,
  pageIsAfterPage,
} from './utils';

export default {
  components: {
    CalendarWeeks,
  },
  props: {
    page: { type: Object, default: () => todayComps },
    minPage: Object,
    maxPage: Object,
    monthLabels: { type: Array, default: () => monthLabels },
    weekdayLabels: { type: Array, default: () => weekdayLabels },
    firstDayOfWeek: { type: Number, default: 1 },
    paneStyle: Object,
    headerStyle: Object,
    titleStyle: Object,
    titleTransition: { type: String, default: 'slide' },
    arrowStyle: Object,
    weekdayStyle: Object,
    weeksTransition: { type: String, default: 'slide' },
  },
  data() {
    return {
      pages: [],
      page_: null,
      slideTransition: '',
    };
  },
  computed: {
    weekdayLabels_() {
      const labels = [];
      for (let i = 1, d = this.firstDayOfWeek; i <= 7; i++, d += (d === 7) ? -6 : 1) {
        labels.push(this.weekdayLabels[d - 1]);
      }
      return labels;
    },
    titleTransition_() {
      if (this.titleTransition === 'slide') return `title-${this.slideTransition}`;
      return `title-${this.titleTransition}`;
    },
    weeksTransition_() {
      if (this.weeksTransition === 'slide') return `weeks-${this.slideTransition}`;
      return `weeks-${this.weeksTransition}`;
    },
    canMovePrevMonth() {
      return this.canMove(this.page_.prevMonthComps);
    },
    canMoveNextMonth() {
      return this.canMove(this.page_.nextMonthComps);
    },
    dayBackgroundColor() {
      if (this.paneStyle) return this.paneStyle.backgroundColor;
      return undefined;
    },
  },
  watch: {
    page(val) {
      this.move(val);
    },
    page_(val, oldVal) {
      this.slideTransition = this.getSlideTransition(oldVal, val);
    },
  },
  created() {
    if (this.page) {
      this.page_ = this.loadPage(this.page);
    } else {
      this.page_ = this.loadPage(todayComps);
      this.$emit('update:page');
    }
    this.preloadPages();
  },
  methods: {
    canMove(pageInfo) {
      if (this.minPage && pageIsBeforePage(pageInfo, this.minPage)) return false;
      if (this.maxPage && pageIsAfterPage(pageInfo, this.maxPage)) return false;
      return true;
    },
    movePrevYear() {
      this.move({ month: this.page_.month, year: this.page_.year - 1 });
    },
    movePrevMonth() {
      this.move(this.page_.prevMonthComps);
    },
    moveThisMonth() {
      this.move(todayComps);
    },
    moveNextMonth() {
      this.move(this.page_.nextMonthComps);
    },
    moveNextYear() {
      this.move({ month: this.page_.month, year: this.page_.year + 1 });
    },
    move(pageInfo) {
      if (this.canMove(pageInfo)) {
        this.forceMove(pageInfo);
      } else if (pageIsBeforePage(todayComps, this.minPage)) {
        this.forceMove(this.minPage);
      } else if (pageIsAfterPage(pageInfo, this.maxPage)) {
        this.forceMove(this.maxPage);
      }
    },
    forceMove(pageInfo) {
      // Exit if there is no page info or page info matches the current page
      if (!pageInfo || (pageInfo.month === this.page_.month && pageInfo.year === this.page_.year)) return;
      // Extract just the month and year info
      const monthYear = { month: pageInfo.month, year: pageInfo.year };
      // Set the active page
      this.page_ = this.loadPage(monthYear);
      // Flag that page was moved to/updated
      this.$emit('move', monthYear);
      this.$emit('update:page', monthYear);
      // Preload other pages
      this.preloadPages();
    },
    loadPage({ month, year }) {
      const key = `${year.toString()}.${month.toString()}`;
      let page = this.pages.find(p => (p.key === key));
      if (!page) {
        const monthLabel = this.monthLabels[month - 1];
        const yearLabel = year.toString();
        const yearLabel_2 = yearLabel.substring(2, 4);
        const headerLabel = `${monthLabel} ${yearLabel}`;
        const firstWeekdayInMonth = new Date(year, month - 1, 1).getDay() + 1;
        const currMonthComps = getMonthComps(month, year);
        const isLeapYear = getIsLeapYear(year);
        const daysInMonth = currMonthComps.days;
        const thisMonthComps = getThisMonthComps();
        const prevMonthComps = getPrevMonthComps(month, year);
        const nextMonthComps = getNextMonthComps(month, year);
        page = {
          key,
          month,
          year,
          monthLabel,
          yearLabel,
          yearLabel_2,
          headerLabel,
          isLeapYear,
          daysInMonth,
          firstWeekdayInMonth,
          thisMonthComps,
          prevMonthComps,
          nextMonthComps,
          canMove: pg => this.canMove(pg),
          move: pg => this.move(pg),
          moveThisMonth: () => this.moveThisMonth(),
          movePrevMonth: () => this.move(prevMonthComps),
          moveNextMonth: () => this.move(nextMonthComps),
        };
        this.pages.push(page);
      }
      page.loaded = true;
      return page;
    },
    preloadPages() {
      // Load the next and previous pages
      Vue.nextTick(() => {
        this.loadPage(this.page_.prevMonthComps);
        this.loadPage(this.page_.nextMonthComps);
        this.pages = this.pages.filter(p => p.loaded);
        this.pages.forEach((p) => { p.loaded = false; });
      });
    },
    getSlideTransition(fromPage, toPage) {
      if (!fromPage || !toPage) return '';
      if (fromPage.year !== toPage.year) return fromPage.year < toPage.year ? 'slide-left' : 'slide-right';
      if (fromPage.month !== toPage.month) return fromPage.month < toPage.month ? 'slide-left' : 'slide-right';
      return '';
    },
  },
};
</script>

<style lang='sass' scoped>

@import '../styles/vars.sass'

=box($justify: center, $align: center)
  display: flex
  justify-content: $justify
  align-items: $align
  margin: 0
  padding: 0

.c-pane
  display: flex
  flex-direction: column
  min-width: $paneMinWidth
  width: $paneWidth
  background-color: $paneBgColor
  border: $paneBorder
  padding: $panePadding
  overflow: hidden

.c-header
  display: flex
  align-items: stretch
  padding: $headerPadding
  user-select: none

  .c-arrow-layout
    +box()
    .c-arrow
      +box()
      font-size: $arrowFontSize
      width: $arrowWidth
      height: $arrowHeight
      transition: $arrowTransition
      cursor: pointer
      user-select: none
      &:hover
        opacity: 0.5
    
  .c-title
    flex-grow: 1
    display: flex
    justify-content: center
    align-items: center
    position: relative
    overflow: hidden
    .c-title-1
      position: absolute
      left: 0
      top: 0
      width: 100%
      height: 100%
      .c-title-2
        display: flex
        height: 100%
        justify-content: center
        align-items: center
        .c-title-3
          font-weight: $titleFontWeight
          font-size: $titleFontSize
          transition: $titleTransition
          cursor: pointer
          user-select: none
          &:hover
            opacity: 0.5
    
  .c-arrow.c-disabled
    cursor: not-allowed
    pointer-events: none
    opacity: 0.2

.c-weekdays
  display: flex
  padding: $weekdayPadding

.c-weekday
  +box()
  width: $dayWidth
  cursor: default
  color: $weekdayColor
  font-size: $weekdayFontSize
  font-weight: $weekdayFontWeight

.c-weeks
  flex-grow: 1
  position: relative

.title-slide-left-enter-active,
.title-slide-left-leave-active,
.title-slide-right-enter-active,
.title-slide-right-leave-active,
.title-fade-enter-active,
.title-fade-leave-active
  transition: $titleTransition

.title-none-enter-active,
.title-none-leave-active
  traisition: all 0s

.title-slide-left-enter,
.title-slide-right-leave-to
  opacity: 0
  transform: translateX($titleTranslateX)

.title-slide-left-leave-to,
.title-slide-right-enter
  opacity: 0
  transform: translateX(-$titleTranslateX)

.weeks-slide-left-enter-active,
.weeks-slide-left-leave-active,
.weeks-slide-right-enter-active,
.weeks-slide-right-leave-active,
.weeks-fade-enter-active,
.weeks-fade-leave-active
  transition: $weeksTransition

.weeks-none-enter-active,
.weeks-none-leave-active
  transition-duration: 0s

.weeks-slide-left-leave-active,
.weeks-slide-right-leave-active,
.weeks-fade-leave-active,
.weeks-none-leave-active
  position: absolute
  top: 0
  bottom: 0
  left: 0
  width: 100%
  height: 100%

.weeks-slide-left-enter,
.weeks-slide-right-leave-to
  opacity: 0
  transform: translateX($weeksTranslateX)

.weeks-slide-left-leave-to,
.weeks-slide-right-enter
  opacity: 0
  transform: translateX(-$weeksTranslateX)

.weeks-fade-enter,
.weeks-fade-leave-to,
.title-fade-enter,
.title-fade-leave-to
  opacity: 0

</style>
