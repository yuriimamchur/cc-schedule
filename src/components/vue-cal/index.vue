<template>
    <div class="vuecal__flex vuecal vuecal--week-view vuecal--no-time" column="column">
        <div class="vuecal__header">
            <div class="vuecal__flex vuecal__weekdays-headings">
                <div class="vuecal__flex vuecal__heading " :class="heading.class" v-for="(heading, i) in viewHeadings" :key="i">
                    <span v-if="heading.date">{{ heading.date }}</span>
                    <span v-if="heading.day">{{ heading.day }}</span>
                </div>
            </div>
        </div>
        <div class="vuecal__flex vuecal__body" grow="grow" v-for="team in teamMembers" :key="team.id">
            <div style="min-width: 100%;">
                <div class="vuecal__flex vuecal__bg">
                    <div class="vuecal__time-column" >
                        <div class="vuecal__time-cell">TeamMember</div>
                    </div>
                    <div class="vuecal__flex vuecal__cells">
                        <vuecal-cell :class="cell.class"
                                     v-for="(cell, i) in viewCells(team)"
                                     :key="i" :date="cell.date"
                                     :formatted-date="cell.formattedDate"
                                     @click.native="selectCell(cell, team)">
                        </vuecal-cell>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { now, isDateToday, formatDate } from './date-utils'
import Cell from './cell'

export default {
  name: 'vue-cal',
  components: { 'vuecal-cell': Cell },
  props: {
    locale: {
      type: String,
      default: 'en'
    },
    hideViewSelector: {
      type: Boolean,
      default: false
    },
    hideWeekends: {
      type: Boolean,
      default: false
    },
    disableViews: {
      type: Array,
      default: () => []
    },
    defaultView: {
      type: String,
      default: 'week'
    },
    selectedDate: {
      type: String,
      default: ''
    },
    small: {
      type: Boolean,
      default: false
    },
    xsmall: {
      type: Boolean,
      default: false
    },
    dblClickToNavigate: {
      type: Boolean,
      default: true
    },
    time: {
      type: Boolean,
      default: true
    },
    timeFrom: {
      type: Number,
      default: 0 // In minutes.
    },
    timeTo: {
      type: Number,
      default: 24 * 60 // In minutes.
    },
    timeStep: {
      type: Number,
      default: 30 // In minutes.
    },
    timeCellHeight: {
      type: Number,
      default: 40 // In pixels.
    },
    '12Hour': {
      type: Boolean,
      default: false
    },
    minCellWidth: {
      type: Number,
      default: 0
    },
    splitDays: {
      type: Array,
      default: () => []
    },
    events: {
      type: Array,
      default: () => []
    },
    resizableEvents: {
      type: Boolean,
      default: true
    },
    editableEvents: {
      type: Boolean,
      default: true
    }
  },
  data: () => ({
    now,
    view: {
      id: 'week',
      title: '',
      startDate: now,
      selectedDate: null,
      selectedTeamMember: null
    },
    teamMembers: [{ id: 1 }, { id: 2 }, { id: 3 }],
    eventIdIncrement: 1,
    resizeEvent: {
      start: null,
      eventId: 0,
      newHeight: 0
    },
    focusedEventId: null, // Only one at the same time.
    draggingEventId: null, // Only one at the same time.
    removableEventId: null, // After a click and hold, event becomes deletable.
    mutableEvents: {}
  }),

  methods: {

    viewCells (team) {
      let todayFound = false
      let firstDayOfWeek = this.view.startDate

      return this.weekDays.map((cell, i) => {
        const date = firstDayOfWeek.addDays(i)
        const formattedDate = formatDate(date, 'yyyy-mm-dd', this.texts)

        return {
          date,
          formattedDate,
          class: {
            today: !todayFound && isDateToday(date) && !todayFound++,
            selected: this.view.selectedDate && firstDayOfWeek.addDays(i).getTime() === this.view.selectedDate.getTime() && team === this.view.selectedTeamMember
          }
        }
      })
    },

    selectCell (cell, team) {
      if (this.view.selectedDate.toString() !== cell.date.toString()) {
        this.view.selectedDate = cell.date
        this.view.selectedTeamMember = team
      }

      this.focusedEventId = null
    }
  },

  created () {
    this.$emit('before-created')
  },

  mounted () {
    this.$emit('created')
  },

  computed: {
    texts () {
      return require(`./i18n/${this.locale}.json`)
    },
    weekDays () {
      const days = this.texts.weekDays.map(day => ({ label: day }))
      return days.concat(days.concat(days))
    },
    viewHeadings () {
      return this.weekDays.map((cell, i) => ({
        day: cell.label.substr(0, 3),
        date: this.view.startDate.addDays(i).getDate()
      }))
    }
  }
}
</script>

<style lang="scss">
$time-column-width: 10em;
$time-column-width-12: 4em; // 12-hour clock shows am/pm.
$weekdays-headings-height: 2.8em;

.vuecal {
  height: 100%;
  overflow: hidden;

  // Disable user selection everywhere except in events.
  * {user-select: none;}

  // General.
  //==================================//
  .clickable {cursor: pointer;}

  &__flex {
    display: flex;
    flex-direction: row;

    &[column] {
      flex-direction: column;
      flex: 1;
    }

    &[grow] {
      flex-grow: 1;
    }
  }

  // Header.
  //==================================//

  &__weekdays-headings {
    border-bottom: 1px solid #ddd;
    margin-bottom: -1px;
    padding-left: $time-column-width;
  }

  &__heading {
    width: 100%;
    height: $weekdays-headings-height;
    font-weight: 400;
    justify-content: center;
    text-align: center;
    align-items: center;
    flex-direction: column;
  }

  // Calendar body.
  //==================================//
  &__body {
    overflow: auto;
    -webkit-overflow-scrolling: touch;
    flex-basis: 0;
    margin-left: -1px;
    min-height: 60px;
  }

  &__bg {
    position: relative;
      height: 100%;
  }

  &__time-column {
    width: $time-column-width;
    height: 100%;
    border-right: 1px solid #eee;
    margin-right: -1px;


    .vuecal__time-cell {
        display: flex;
      color: #999;
      text-align: right;
      padding-right: 2px;
      font-size: 0.9em;

      &:before {
        content: '';
        position: absolute;
        left: 0;
        right: 0;
        border-top: 1px solid #eee;
      }
    }
  }
  &__cells {
    flex-wrap: nowrap;
    overflow: hidden;
  }
}
</style>
