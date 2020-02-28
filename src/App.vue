<template>
  <div id="app">
    <header>
      <label :class="{ glow: direction === 'perm' }">
        <input type="radio" name="dir" value="perm" v-model="direction" />
        Perm'
      </label>
      <label :class="{ glow: direction === 'gamovo' }">
        <input type="radio" name="dir" value="gamovo" v-model="direction" />
        Gamovo
      </label>
      <label :class="{ glow: weekend }">
        <input type="checkbox" v-model="weekend" /> Holiday
      </label>
    </header>
    <main>
      <div
        v-for="time in timetable"
        :key="time.time"
        :class="{
          'non-available': !time.everyday && weekend,
          next: time.time === next.time,
          short: time.time >= '22:00'
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
  font-family: "digital", monospace;
  font-weight: 100;
  text-transform: uppercase;
}
body {
  background: #222831;
}
header {
  display: flex;
  flex-wrap: wrap;
  height: 10vh;
  font-size: 2.5rem;
  margin: 1rem 0;
  label {
    display: flex;
    flex-basis: 50%;
    justify-content: center;
    color: #393e46;
    transition: 0.4s;
    &.glow {
      color: #fd7014;
    }
    &:last-child {
      flex-basis: 100%;
    }
    input {
      display: none;
    }
  }
}
main {
  display: flex;
  flex-wrap: wrap;
  font-size: 2.5rem;
  div {
    flex-basis: 33.3%;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #fd7014;
    transition: 0.4s;
    &.non-available {
      color: #393e46;
    }
    &.next {
      span {
        animation: 1s slide infinite alternate;
      }
    }
    &.short {
      color: rgba($color: #fd7014, $alpha: 0.4);
    }
  }
}

@keyframes slide {
  0% {
    filter: brightness(50%);
  }
  60% {
    filter: brightness(150%);
    text-shadow: 0 0 10px #fd7014;
  }
  100% {
    filter: brightness(100%);
  }
}
</style>
