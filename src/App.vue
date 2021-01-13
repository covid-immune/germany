<template>
  <div id="app">
    <div style="text-align: center; margin-bottom: 20px">
      <a href="https://www.dw.com/de/herdenimmunit%C3%A4t-bei-impfrate-um-70-prozent/a-55755841">70% Impfquote</a> wird erreicht am
    </div>
    <table v-if="loaded" style="margin: 0 auto">
      <tr v-for="(state, key) in vaccinations.data.states" :key="key">
        <td>{{ state.name }}<td>
        <td style="text-align: left">{{ days(state.vaccinatedPer1k / 1000) }}</td>
      </tr>
      <tr>
        <th>Durchschnitt Deutschland<th>
        <th style="text-align: left">{{ days(vaccinations.data.vaccinatedPer1k / 1000) }}</th>
      </tr>
    </table>
    <div class="footer">
      Zahlen
      <span v-if="loaded">des {{ vaccinations.meta.source }}s</span>
      über <a href="https://api.corona-zahlen.org">api.corona-zahlen.org</a><br>
      <span v-if="loaded">Letztes Update {{ format(vaccinations.meta.lastUpdate) }}</span>
    </div>
  </div>
</template>

<script>
import moment from "moment";

export default {
  name: 'App',
  data: function () {
    return {
      loaded: false,
      vaccinations: {},
    }
  },
  methods: {
    format: function (d) {
      return moment(d).format("DD.MM.YYYY H:m");
    },
    days: function (quote) {
      const start = moment("2020-12-27", "YYYY-MM-DD");

      var days = moment.duration(moment().diff(start)).asDays();
      console.log(days);
      return start.add((1 / this.vacQuotePerDay(quote, days)) * 0.7, 'days').format('DD.MM.YYYY');
    },
    vacQuotePerDay: function (quote, days) {
      return quote / days
    }
  },
  mounted: function () {
    this.$nextTick(function () {
      this.$http.get("https://api.corona-zahlen.org/vaccinations").then((response) => {
        this.vaccinations = response.data;
        this.loaded = true;
      })
    })
  }
}
</script>

<style>
html, body {
  height: 100%;
  width: 100%;
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: right;
  color: #2c3e50;
  padding-top: 30px;
}

td, th {
  width: 150px;
}

.footer {
  font-size: 10pt;
  margin: 10px auto;
  text-align: center;
}
</style>
