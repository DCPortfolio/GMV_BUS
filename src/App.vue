<template>
  <div id="app">
    <header>
      <label>
        <input type="radio" name="dir" value="perm" v-model="direction" />
        Perm'
      </label>
      <label>
        <input type="radio" name="dir" value="gamovo" v-model="direction" />
        Gamovo
      </label>
      <label><input type="checkbox" v-model="weekend" />Holiday</label>
    </header>
    <main>
      <div
        v-for="time in timetable"
        :key="time.time"
        :class="{
          'non-available': !time.everyday && weekend,
          next: time.time === next.time
        }"
      >
        <span> {{ time.time }} </span>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      alltimetable: {},
      direction: "perm",
      weekend: null
    };
  },
  computed: {
    timetable() {
      return this.alltimetable[this.direction];
    },
    timeNow() {
      const now = new Date(Date.now());
      const hours = now.getHours() < 10 ? "0" + now.getHours() : now.getHours();
      const minutes =
        now.getMinutes() < 10 ? "0" + now.getMinutes() : now.getMinutes();
      return `${hours}:${minutes}`;
    },
    next() {
      return this.timetable.find(e => {
        return this.weekend
          ? e.everyday && e.time >= this.timeNow
          : e.time >= this.timeNow;
      });
    }
  },
  created() {
    const today = new Date(Date.now()).getDay();
    this.weekend = today === 6 || today === 0;
    fetch("/data/timetable.json").then(response => {
      response.json().then(data => {
        this.alltimetable = data;
      });
    });
  }
};
</script>

<style lang="scss">
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  user-select: none;
  cursor: pointer;
}
header {
  display: flex;
  flex-wrap: wrap;
  label {
    display: flex;
    flex-basis: 50%;
    justify-content: center;
    &:last-child {
      flex-basis: 100%;
    }
  }
}
main {
  display: flex;
  flex-wrap: wrap;
  div {
    flex-basis: 33.3%;
    display: flex;
    justify-content: center;
    align-items: center;
    &.non-available {
      color: rgb(194, 194, 194);
    }
    &.next {
      color: red;
    }
  }
}
</style>
