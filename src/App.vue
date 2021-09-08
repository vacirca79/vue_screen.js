<template>
  <div id="app">
  <h1 class="site-title">{{ title }}</h1>
  <span class="site-description">{{ currentDate }}</span>

  <ul class="entry-list">
    <li
      v-for="entry in filteredEntries"
      :key="entry.id"
      class="entry-item"
    >
      <span class="entry-daytime">{{ entry[0] }} Uhr, {{ entry[1].replaceAll("/", ".") }}</span>
      <h3 class="entry-title">{{ entry[2] }}</h3>
      <span class="entry-description">{{ entry[3] }}</span>
      <br>
    </li>
  </ul>

<footer class="footer">
  <img src="./assets/STZH_SEB_Logo.png"
  alt="Stadt Zürich Soziale Betriebe und Einrichtungen"
  >
  <img src="./assets/Opportunity.png"
  alt="Opportunity Logo"
  >
  <img src="./assets/SAG_Logo_De.png"
  alt="SAG Logo"
  >
</footer>

  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
        title: "Welcome to Opportunity",
        currentDate: "",
        gsheet_url:
          "https://sheets.googleapis.com/v4/spreadsheets/1iWI-m-Dk2Q8vQNSbPNRfHFjRJgGg0-0l5l_05xNIHr4/values:batchGet?ranges=A1%3AE100&valueRenderOption=FORMATTED_VALUE&key=AIzaSyDTrl60fDojFmKpRE1LS-6Yq8T44VzwYOY",
        entries: [],
      };
    },
    computed: { //computed are like data properties, but with a combined, it gets executed automatictly instaed of caling a function explicitly 
      filteredEntries() {
        return [...this.entries].slice(1); //remove first item of array
      },
    },
    methods: {
      getData() {
        axios.get(this.gsheet_url).then((response) => {
          this.entries = response.data.valueRanges[0].values;
          console.log(response);
        });
      },
      updateCurrentDate() {
        let today = new Date();
        const date = `${today.getDate()}.${
          today.getMonth() + 1
        }.${today.getFullYear()}`;
        this.currentDate = date
      },
      refreshData() {
        this.getData();
        this.updateCurrentDate();
      },
    },
    mounted() {
      this.refreshData();
      setInterval(() => {
        this.refreshData();
      }, 1800000); // wait 30min for next update
    },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@500;900&display=swap");

body {
  background: #e8eff4;
}

#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.site-title {
  font-size: 62px;
  font-weight: 900;
  margin: 80 0 ;
}

.site-description {
  font-weight: 500;
  font-size: 62px;
  color: #9aa7b1;
  margin: 0;
}

.entry-list {
  padding-left: 0;
}

.entry-item {
  padding: 35px 40px;
  margin: 40px 0;
  font-size: 28px;
  line-height: 1.3;
  list-style: none;
  background: #0f05a0;
}

.entry-daytime {
  font-weight: 900;
  color: #eb5e00;
}

.entry-title {
  font-size: inherit;
  font-weight: 900;
  color: #ffbfab;
  margin: 0;
}

.entry-description {
  font-weight: 500;
  color: #ffbfab;
}

.footer {
  display: flex;
  justify-content: space-between;
  padding: 40px;
  box-sizing: border-box;
  background: #fff;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
}

.footer img {
  height: 50px;
}
</style>
