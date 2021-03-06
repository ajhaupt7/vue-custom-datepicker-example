<template>
  <div id="app" @click="closeColorSelector">
    <section :style="formStyles" class="style-selector">
      <h1>Vue Custom Datepicker</h1>
      <pre>
        <div>
          <label>dateRange:</label><md-checkbox class="md-primary" name="dateRange" v-model="dateRange">Check to turn on range select</md-checkbox>
        </div>
        <div>
          <label>primaryColor:</label><input @click.stop="showColorSelector" class="color-input" :value="calPrimaryColor" readonly/><span>,</span><Swatches v-if="colorSelectorOpen" v-model="primaryColor" @change-color="changeColor" />
          <label>dateFormat:&nbsp;&nbsp;</label><input type="text" v-model="dateFormat"><span>,</span>
        </div>
        <div>
          <label>wrapperStyles: {</label>
          <textarea type="text" v-model="wrapperStylesString" />
          <label>},</label>
        </div>
        <div>
          <label>headerStyles: {</label>
          <textarea type="text" v-model="headerStylesString" />
          <label>},</label>
        </div>
        <div>
          <label>weekdayStyles: {</label>
          <textarea type="text" v-model="weekdayStylesString" />
          <label>},</label>
        </div>
        <div class="limits">
          <label>limits: {</label>
            <label>start:</label><input type="text" v-model="limitsStart"><span>,</span>
            <label>end:&nbsp;&nbsp;</label><input type="text" v-model="limitsEnd">
          <label>}</label>
        </div>
      </pre>
    </section>
    <section>
      <div class="calendar">
        <h2>{{ dateRange ? formattedDateRange : formattedDate }}</h2>
        <CustomDatepicker 
          @dateSelected       = "setDate($event)"
          @dateRangeSelected  = "setDateRange($event)"
          :date               = "selectedDate" 
          :dateRange          = "dateRange" 
          :primaryColor       = "calPrimaryColor"
          :dateFormat         = "dateFormat"
          :wrapperStyles      = "wrapperStyles"
          :headerStyles       = "headerStyles"
          :weekdayStyles      = "weekdayStyles"
          :limits             = "limits"
        />
      </div>
    </section>
  </div>
</template>

<script>
import Vue from 'vue';
import VueMaterial from 'vue-material';
import CustomDatepicker from 'vue-custom-datepicker'
import moment from 'moment'
import { Swatches } from 'vue-color'
import 'vue-material/dist/vue-material.css';

Vue.use(VueMaterial);

const colorProps = {
  hex: '#0918bc',
  hsl: {
    h: 150,
    s: 0.5,
    l: 0.2,
    a: 1
  },
  hsv: {
    h: 150,
    s: 0.66,
    v: 0.30,
    a: 1
  },
  rgba: {
    r: 25,
    g: 77,
    b: 51,
    a: 1
  },
  a: 1
}

const parseStyles = (stylesString) => {
  let attrs = {}
  stylesString.split(/,(?=(?:(?:[^'"]*(?:'|")){2})*[^'"]*$)/).map((attribute) => {
    try {
      let vals = attribute.split(":")
      attrs[vals[0].trim()] = eval(vals[1].trim())
    }
    catch (e) {
     console.log(e)
    }
  })
  return attrs
}

export default {
  name: 'app',
  components: {
    CustomDatepicker,
    Swatches,
  },
  data () {
    return {
      dateRange: false,
      wrapperStylesString: "width:'325px'",
      headerStylesString: "",
      weekdayStylesString: "fontWeight:'100'",
      primaryColor: colorProps,
      selectedDate: moment(),
      selectedDateRangeStart: null,
      selectedDateRangeEnd: null,
      dateFormat: "YYYY-MM-DD",
      colorSelectorOpen: false,
      limitsStart: "",
      limitsEnd: ""
    }
  },
  computed: {
    formStyles() {
      return {
        background: this.primaryColor.hex
      }
    },
    calPrimaryColor() {
      return this.primaryColor.hex
    },
    formattedDate() {
      return moment(this.selectedDate).format(this.dateFormat)
    },
    formattedDateRange() {
      const start = this.selectedDateRangeStart ? moment(this.selectedDateRangeStart).format(this.dateFormat) : '...';
      const end   = this.selectedDateRangeEnd ? moment(this.selectedDateRangeEnd).format(this.dateFormat) : '...';
      return `${start} to ${end}`;
    },
    wrapperStyles() {
      return parseStyles(this.wrapperStylesString)
    },
    headerStyles() {
      return parseStyles(this.headerStylesString)
    },
    weekdayStyles() {
      return parseStyles(this.weekdayStylesString)
    },
    limits() {
      return {
        start: this.limitsStart,
        end: this.limitsEnd
      }
    }
  },
  methods: {
    setDate(date) {
      this.selectedDate = date
    },
    setDateRange(range) {
      this.selectedDateRangeStart = range.start;
      this.selectedDateRangeEnd = range.end;
    },
    changeColor(val) {
      this.primaryColor = val
    },
    showColorSelector() {
      this.colorSelectorOpen = true
    },
    closeColorSelector(e) {
      this.colorSelectorOpen = e.target.classList.contains('vue-color__swatches') || e.target.closest('.vue-color__swatches')
    }
  }
}
</script>

<style lang="scss">
html, body {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Cabin', monospace;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  box-sizing: border-box;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  h1 {
    color: white;
    letter-spacing: 2px;
    font-weight: 400;
    font-size: 2.5em;
    opacity: 0.8;
    &:after {
      content: "";
      height: 5px;
      background: white;
      width: 20%;
      display: block;
      margin: auto;
      position: relative;
      top: 20px;
    }
  }
  > section {
    flex-basis: 50%;
    &:last-child {
      position: relative;
      h2 {
        margin-top:0;
        position: absolute;
        top: -100px;
        left: 50%;
        transform: translateX(-50%);
      }
    }
  }
}

.style-selector {
  min-height: 100vh;
  box-shadow: rgba(0, 0, 0, 0.188235) 0px 10px 30px, rgba(0, 0, 0, 0.227451) 0px 6px 10px;
  font-family: 'Titillium Web', sans-serif;
  color: white;
  font-size: 1em;
  transition: all 0.4s ease-in-out;
  > pre {
    display: flex;
    flex-wrap: wrap;
    margin-top: 50px;
    > div {
      flex-basis: 100%;
      margin: -15px auto;
      text-align: left;
      position: relative;
    }
  }
}

input, textarea {
  background: rgba(0,0,0,0.12);
  border: none;
  outline: none;
  padding: 5px 10px;
  margin:10px 25px;
  color: white;
  font-family: monospace;
  white-space: pre;
  font-size: 1em;
  max-width: 100%;
  width: 300px;
  + span {
    position: relative;
    top:7.5px;
    left: -15px;
  }
}

.limits {
  input {
    width: 345px;
  }
}

textarea {
  width: 412.5px;
  height: 50px;
}

.color-input {
  cursor: pointer;
}

.vue-color__swatches {
  position: absolute;
  z-index: 100;
  height: 285px;
  left: 25%;
  border-radius: 2px;
}

.md-checkbox .md-checkbox-container {
  border: 2px solid rgba(255,255,255, 0.74) !important;
  margin-left: 2em; 
}

.md-checkbox {
  margin: 10px 25px !important;
}

.md-theme-default.md-checkbox.md-primary.md-checked .md-checkbox-container {
  background-color: rgba(0,0,0,0.2) !important;
}


</style>
