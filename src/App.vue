<template lang="pug">
  v-app.white(:class="{ ready: ready }")
    v-container
      v-card.green-theme.my-2.ma-auto.main-content(style="height: 350px;")
        vue-cal.vuecal--green-theme(selected-date="2018-11-19" :time="false" :disable-views="['years', 'year', 'month', 'day', 'week']" :events="timelessEvents")
</template>

<script>
import VueCal from '@/components/vue-cal'
import highlightMessage from '@/components/highlight-message'

const events = [
  {
    start: '2018-11-16 10:30',
    end: '2018-11-16 11:30',
    title: 'Doctor appointment',
    content: '<i class="v-icon material-icons">local_hospital</i>',
    class: 'health',
    split: 1
  }
]

export default {
  name: 'app',
  components: {
    VueCal,
    highlightMessage
  },
  data: () => ({
    ready: false,
    splitsExampleMinCellWidth: 400,
    example1theme: 'green',
    now: new Date(),
    events,
    overlappingEvents: [...events],
    splitEvents: [...events],
    backgroundEvents: [...events],
    timelessEvents: [
      {
        start: '2018-11-21',
        end: '2018-11-23',
        title: 'Need to go shopping',
        content: '<i class="v-icon material-icons">shopping_cart</i>',
        class: 'leisure'
      }
    ]
  }),
  created () {
    setTimeout(() => (this.ready = true), 500)
  },
  computed: {
    currentDateFormatted () {
      const y = this.now.getFullYear()
      const m = this.now.getMonth()
      const d = this.now.getDate()
      const h = this.now.getHours()
      const min = this.now.getMinutes()
      return `${y}-${(m < 10 ? '0' : '') + m}-${(d < 10 ? '0' : '') + d} ${(h < 10 ? '0' : '') + h}:${(min < 10 ? '0' : '') + min}`
    }
  }
}
</script>

<style lang="scss">
$primary: #42b983;

* {
  margin: 0;
  padding: 0;
}

a {
  text-decoration: none;
  color: $primary !important;

  &[name] {
    position: relative;
    top: -4em;
    display: block;
  }
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.logo {
  position: relative;
  display: inline-block;

  img {z-index: 1;position: relative;}

  .v-icon {
    position: absolute;
    left: 50%;
    font-size: 3em;
    opacity: 0;
    transition: 0.6s cubic-bezier(0.68, -0.55, 0.27, 1.55);
    transform: rotate(0deg) scale(0.3);
    z-index: 0;
    color: #ddd;
  }

  .v-icon.calendar {
    margin-left: -25px;
    transform-origin: 75% 100%;

    .ready & {
      transform: translateX(-75%) rotate(-30deg) scale(0.8);
    }
  }

  .v-icon.time {
    margin-left: -19px;
    transform-origin: 25% 100%;

    .ready & {
      transform: translateX(75%) rotate(30deg) scale(0.8);
    }
  }

  .ready & .v-icon {
    opacity: 1;
  }
}

.main-content {
  height: 650px;
}

.code {
  font-family: monospace, sans-serif;
}

.api-options {list-style-type: none;}
.api-options > li {margin-top: 2em;}
.api-options p {margin-left: 1.5em;margin-top: 0.5em;}

.v-footer {
  font-size: 0.9em;

  .v-icon {
    font-size: 1.2em;
  }
}

// Split days example.
.vuecal__cell-split.him {background-color: rgba(221, 238, 255, 0.6);}
.vuecal__cell-split.him .split-label {color: rgba(0, 84, 194, 0.1);font-size: 30px;font-weight: 500;}
.vuecal__cell-split.her {background-color: rgba(255, 232, 251, 0.6);}
.vuecal__cell-split.her .split-label {color: rgba(255, 0, 106, 0.1);font-size: 30px;font-weight: 500;}

.vuecal__event.leisure {background-color: rgba(253, 156, 66, 0.9);border: 1px solid rgb(233, 136, 46);color: #fff;}
.vuecal__event.health {background-color: rgba(164, 230, 210, 0.9);border: 1px solid rgb(144, 210, 190);}
.vuecal__event.sport {background-color: rgba(255, 102, 102, 0.9);border: 1px solid rgb(235, 82, 82);color: #fff;}

.vuecal__event.lunch {
  background: repeating-linear-gradient(45deg, transparent, transparent 10px, #f2f2f2 10px, #f2f2f2 20px);
  color: #999;
  display: flex;
  justify-content: center;
  align-items: center;
}
.vuecal__event.lunch .vuecal__event-time {display: none;align-items: center;}
</style>
