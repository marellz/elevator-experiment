<template>
  <div id="app">
    <header class="navbar navbar-dark bg-dark">
      <div class="navbar-brand">
        <h2 class="title">Elevator</h2>
      </div>
    </header>
    <main class="bg-light">
      <div class="container my-5">
        <div class="row">
          <div class="col-sm-12 col-lg-6">
            <elevator @log="logger"></elevator>
          </div>
          <div class="col-sm-12 col-lg-6 elevator--logger">
            <p class="text-underlined">Elevator logs:</p>
            <pre
              style="height:300px"
              class="d-flex flex-column-reverse border overflow-auto"
            >
              <code class="d-block" v-for="(log,i) in logs.reverse()" :key="i">elevator: {{log}}</code>
            </pre>
          </div>
        </div>
      </div>
    </main>
    <footer class="bg-light">
      <div class="container">
        <p class="small text-center">
          Made by <a href="https://waweru.design">Dave.</a>
        </p>
      </div>
    </footer>
  </div>
</template>

<script>
import Elevator from "@/components/Elevator";
export default {
  name: "App",
  components: {
    Elevator,
  },
  data() {
    return {
      logs: [],
    };
  },
  methods: {
    logger(data) {
      this.logs.push(data);
    },
    clearLogs() {
      this.logs = [];
    },
  },
};
</script>
