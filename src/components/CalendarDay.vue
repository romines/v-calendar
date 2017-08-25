<template>
<div class='c-day'>
  <!-- Background layers -->
  <div
    v-for='(background, i) in backgrounds' :key='i'
    :class='getWrapperClass(background)'>
    <div :class='background.class' :style='background.style'></div>
  </div>
  <!-- Single date selection background layer -->
   <!-- <transition name='size'>
     <div class='c-day-layer c-day-wrap-center' v-if='showSingleSelectBackground'>
      <div :class='singleSelectBackgroundClass' :style='singleSelectBackgroundStyle'></div>
    </div> 
  </transition>  -->
  <!-- Background layer for dragged and selection spans -->
  <!-- <div class='c-day-layer' :class='daySpanBgWrapperClass()' v-if='showDaySpanBg()'>
    <div :class='daySpanBgClass()'></div>
  </div> -->
  <!-- Background layer for hovered dates -->
  <transition name='fade'>
    <div class='c-day-layer c-day-wrap-center' v-if='showDayHover()'>
      <div :class='dayHoverBgClass()'></div>
    </div>
  </transition>
  <!-- Content layer -->
  <div class="c-day-layer c-day-wrap-center">
    <div
      :class='contentClass'
      :style='contentStyle'
      @click='click()'
      @mouseenter='enter()'
      @mouseleave='leave()'>
      {{ label }}
    </div>
  </div>
</div>
</template>

<script>

export default {
  data() {
    return {
      isHovered: false,
    };
  },
  props: {
    backgrounds: Array,
    contentClass: String,
    contentStyle: Object,
    indicators: Array,
    label: String,
    day: Number,
    weekday: Number,
    week: Number,
    month: Number,
    year: Number,
    inMonth: Boolean,
    beforeMonth: Boolean,
    afterMonth: Boolean,
  },
  computed: {
    // computedTheme() {
    //   const cd = this.computedData;
    //   const t = this.theme;
    //   let color = t.dayColor;
    //   if (t.isToday) color = t.dayColor;
    //   if (t.isSelected) color = t.selectColor;
    //   const theme = {
    //     backgroundStyle: cd.isToday ? {
    //       ...this.getCircleStyle(t.dayHoverHeight),
    //       backgroundColor: t.todayBgColor,
    //     } : null,
    //     singleSelectBackgroundStyle: {
    //       ...this.getCircleStyle(t.selectHeight),
    //       color: t.selectColor,
    //       backgroundColor: t.selectBgColor,
    //     },
    //     contentStyle: {
    //       ...this.getBoxStyle(),
    //       ...this.getCircleStyle(t.dayHoverHeight),
    //       color,
    //       fontSize: t.dayFontSize,
    //       fontWeight: t.dayFontWeight,
    //       cursor: 'pointer',
    //       userSelect: 'none',
    //       transition: 'all 0.18s ease-in-out',
    //     },
    //   };
    //   if (this.configureTheme) this.configureTheme(theme);
    //   return theme;
    // },
  },
  methods: {
    getWrapperClass({ horizontalAlign, verticalAlign }) {
      if (!horizontalAlign) horizontalAlign = 'center';
      if (!verticalAlign) verticalAlign = 'center';
      return `c-day-layer c-day-box-${horizontalAlign}-${verticalAlign}`;
    },
    getBoxStyle(justify = 'center') {
      return {
        display: 'flex',
        justifyContent: justify,
        alignItems: 'center',
        margin: 0,
        padding: 0,
      };
    },
    getCircleStyle(diameter) {
      return {
        width: diameter,
        height: diameter,
        borderRadius: '50%',
      };
    },
    click() {
      this.$emit('dayClick', this.data);
    },
    enter() {
      this.isHovered = true;
      this.$emit('dayEnter', this.data);
    },
    leave() {
      this.isHovered = false;
      this.$emit('dayLeave', this.data);
    },
    // showDaySpanBg() {
    //   if (this.selectMode !== 'range') return false;
    //   return this.isDragged || (this.isSelected && !this.dragActive);
    // },
    // daySpanBgWrapperClass() {
    //   if ((this.startsDrag && this.endsDrag) || (this.startsSelection && this.endsSelection)) return 'c-day-wrap-center';
    //   if (this.startsDrag || this.startsSelection) return 'c-day-wrap-right';
    //   if (this.endsDrag || this.endsSelection) return 'c-day-wrap-left';
    //   return 'c-day-wrap-center';
    // },
    // daySpanBgClass() {
    //   if (this.startsDrag && this.endsDrag) return 'c-day-drag-start-end';
    //   if (this.startsDrag) return 'c-day-drag-start';
    //   if (this.endsDrag) return 'c-day-drag-end';
    //   if (this.isDragged) return 'c-day-drag-center';
    //   if (this.startsSelection && this.endsSelection) return 'c-day-select-start-end';
    //   if (this.startsSelection) return 'c-day-select-start';
    //   if (this.endsSelection) return 'c-day-select-end';
    //   return 'c-day-select-center';
    // },
    showDayHover() {
      return this.isHovered && !this.dragActive;
    },
    dayHoverBgClass() {
      if (this.isSelected) return ['c-day-select-circle', 'c-day-hover-bg'];
      if (this.isToday) return ['c-day-today-circle', 'c-day-hover-bg'];
      return ['c-day-circle', 'c-day-hover-bg'];
    },
    // dayContentClass() {
    //   if (this.isDragged) {
    //     if (this.inMonth) return ['c-day-drag-circle', 'c-day-drag-content'];
    //     return ['c-day-drag-circle', 'c-day-drag-not-in-month-content'];
    //   }
    //   if (this.isSelected && !this.dragActive) {
    //     if (this.inMonth) return ['c-day-select-circle', 'c-day-select-content'];
    //     return ['c-day-select-circle', 'c-day-select-not-in-month-content'];
    //   }
    //   if (!this.inMonth) return ['c-day-circle', 'c-day-not-in-month-content'];
    //   if (this.isToday) return ['c-day-today-circle', 'c-day-today-content'];
    //   return ['c-day-circle', 'c-day-content'];
    // },
  },
};

</script>

<style lang='sass' scoped>

$wrapperPadding: 0.8em 0.4em
$wrapperBgColor: #3c6186

$headerPadding: 0 0.4em 0.3em 0.4em

$arrowColor: #fafafa
$arrowFontSize: 2rem
$arrowFontWeight: 300
$arrowHoverColor: #8f9aab
$arrowSize: 0.9em
$arrowMarginTop: -.2em
$arrowMarginHorizontal: 0

$titleColor: #fafafa
$titleFontSize: 1.1rem
$titleFontWeight: 300
$titleHoverColor: #8f9aab

$weekdayColor: #8f9aab
$weekdayFontSize: 0.9rem
$weekdayFontWeight: 500
$weekdayPadding: 0.6em 0

$dayColor: #fafafa
$dayFontSize: 0.8rem
$dayFontWeight: 600
$dayWidth: 14.2857%
$dayHeight: 2.2em

$dragBgColor: #91abc3
$dragColor: #103456
$dragNotInMonthColor: #8f9aab
$dragFontSize: 0.85rem
$dragFontWeight: 600
$dragSpanHeight: 1.8em
$dragSpanEndsWidth: 95%

$selectBgColor: #fafafa
$selectColor: #103456
$selectNotInMonthColor: #8f9aab
$selectFontSize: 0.85rem
$selectFontWeight: 600
$selectSpanHeight: 1.9em
$selectSpanEndsWidth: 95%

$hoverBgColor: rgba(16, 52, 86, 0.25)
$hoverHeight: 1.9em

$notInMonthColor: #8f9aab
$notInMonthFontSize: 0.8rem
$notInMonthFontWeight: 500

$transitionTime: 0.18s ease-in-out
$scaleTransition: all 0.06s ease-in-out

=circle($diameter)
  width: $diameter
  height: $diameter
  border-radius: 50%
  transition: height $transitionTime, width $transitionTime, opacity $transitionTime

=content($color, $fontSize, $fontWeight, $cursor: pointer)
  color: $color
  font-size: $fontSize
  font-weight: $fontWeight
  cursor: $cursor
  user-select: none
  transition: all $transitionTime

=box($justify: center, $align: center)
  display: flex
  justify-content: $justify
  align-items: center
  margin: 0
  padding: 0

=roundedRect($width, $height, $borderRadius, $bgColor)
  width: $width
  height: $height
  border-radius: $borderRadius
  background-color: $bgColor
  transition: height $transitionTime

.c-day-box-center-center
  +box()

.c-header
  display: flex
  align-items: center
  padding: $headerPadding
  cursor: default
  
  .c-arrow, .c-title
    cursor: pointer
    user-select: none
    transition: all $transitionTime
    &:hover
      color: #8f9aab
    
  .c-arrow
    +box()
    +content($arrowColor, $arrowFontSize, $arrowFontWeight)
    width: $arrowSize
    height: $arrowSize
    border-radius: 50%
    &:hover
      color: $arrowHoverColor
      background-color: $hoverBgColor
    .c-left
      margin-top: $arrowMarginTop
      margin-left: -$arrowMarginHorizontal
    .c-right
      margin-top: $arrowMarginTop
      margin-left: $arrowMarginHorizontal
    
  .c-title-wrapper
    text-align: center
    flex-grow: 1
    .c-title
      +content($titleColor, $titleFontSize, $titleFontWeight)
      &:hover
        color: $titleHoverColor
        
.c-weekday
  +box()
  +content($weekdayColor, $weekdayFontSize, $weekdayFontWeight, default)
  padding: $weekdayPadding
  
.c-day
  position: relative
  width: $dayWidth
  height: $dayHeight

.c-day-layer
  position: absolute
  width: 100%
  height: 100%
  left: 0
  top: 0
  background-color: transparent

.c-day-wrap-left
  +box(flex-start)

.c-day-wrap-center
  +box()

.c-day-wrap-right
  +box(flex-end)

.c-day-drag-start-end
  +roundedRect($dragSpanEndsWidth, $dragSpanHeight, $dragSpanHeight, $dragBgColor)

.c-day-drag-start
  +roundedRect($dragSpanEndsWidth, $dragSpanHeight, $dragSpanHeight 0 0 $dragSpanHeight, $dragBgColor)

.c-day-drag-center
  +roundedRect(100%, $dragSpanHeight, 0, $dragBgColor)

.c-day-drag-end
  +roundedRect($dragSpanEndsWidth, $dragSpanHeight, 0 $dragSpanHeight $dragSpanHeight 0, $dragBgColor)

.c-day-drag-circle
  +circle($dragSpanHeight)

.c-day-select-start-end
  +roundedRect($selectSpanEndsWidth, $selectSpanHeight, $selectSpanHeight, $selectBgColor)

.c-day-select-start
  +roundedRect($selectSpanEndsWidth, $selectSpanHeight, $selectSpanHeight 0 0 $selectSpanHeight, $selectBgColor)

.c-day-select-center
  +roundedRect(100%, $selectSpanHeight, 0, $selectBgColor)

.c-day-select-end
  +roundedRect($selectSpanEndsWidth, $selectSpanHeight, 0 $selectSpanHeight $selectSpanHeight 0, $selectBgColor)
  
.c-day-select-circle
  +circle($selectSpanHeight)
 
.c-day-circle
  +circle($hoverHeight)
  
.c-day-hover-bg
  background-color: $hoverBgColor
  
.c-day-content
  +box()
  +content($dayColor, $dayFontSize, $dayFontWeight)
  
.c-day-not-in-month-content
  +box()
  +content($notInMonthColor, $notInMonthFontSize, $notInMonthFontWeight)
  
.c-day-drag-content
  +box()
  +content($dragColor, $dragFontSize, $dragFontWeight)

.c-day-drag-not-in-month-content
  +box()
  +content($dragNotInMonthColor, $dragFontSize, $dragFontWeight)
  
.c-day-select-content
  +box()
  +content($selectColor, $selectFontSize, $selectFontWeight)

.c-day-select-not-in-month-content
  +box()
  +content($selectNotInMonthColor, $selectFontSize, $selectFontWeight)

.fade-enter-active, .fade-leave-active
  transition: opacity $transitionTime
  
.fade-enter, .fade-leave-to
  opacity: 0

.size-enter-active
  animation: bounceGrow 0.14s

.size-leave-active
  animation: bounceShrink 0.2s

@keyframes bounceGrow
  0%
    transform: scaleX(0.7) scaleY(0.7)
    opacity: 0
  90%
    transform: scaleX(1.05) scaleY(1.05)
  95%
    transform: scaleX(0.95) scaleY(0.95)
  100%
    transform: scaleX(1) scaleY(1)
    opacity: 1

@keyframes bounceShrink
  80%
    transform: scaleX(1.1) scaleY(1.1)
  100%
    transform: scaleX(0.5) scaleY(0.5)
    opacity: 0

</style>