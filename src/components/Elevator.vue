<template>
  <div>
    <p>
      Elevator
    </p>

    <table class="table elevator--table">
      <tbody>
        <tr v-for="(floor, i) in floors" :key="i">
          <td>
            <div class="d-flex align-items-center">
              
              <h6 class="mb-0" v-if="floor.label">{{ floor.label }}</h6>
              <h6 class="mb-0" v-else>Floor {{ floor.level }}</h6>
              <div
                class="ml-4 elevator--indicator"
                :class="{ active: floor.level == elevatorRealisticPosition }"
              />
              <div class="ml-5">
                <button
                  v-if="i > 0"
                  class="btn btn-sm elevator--button elevator--button-up"
                  :class="{
                    requested: hasAlreadyBeenRequested({
                      direction: 1,
                      level: floor.level,
                    }),
                  }"
                  @click="requestElevator({ direction: 1, level: floor.level })"
                >
                  <ArrowUpCircleIcon />
                </button>
                <button
                  v-if="i < floors.length - 1"
                  class="btn elevator--button elevator--button-down"
                  :class="{
                    requested: hasAlreadyBeenRequested({
                      direction: 2,
                      level: floor.level,
                    }),
                  }"
                  @click="requestElevator({ direction: 2, level: floor.level })"
                >
                  <ArrowDownCircleIcon />
                </button>
              </div>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
import { ArrowUpCircleIcon, ArrowDownCircleIcon } from "vue-feather-icons";
export default {
  name: "Elevator",
  components: {
    ArrowUpCircleIcon,
    ArrowDownCircleIcon,
  },
  data() {
    return {
      log: [],
      floors: [
        { level: 10 },
        { level: 9 },
        { level: 8 },
        { level: 7 },
        { level: 6 },
        { level: 5 },
        { level: 4 },
        { level: 3 },
        { level: 2 },
        { level: 1 },
        { level: 0, label:'Ground' },
      ],
      directions: {
        1: "up",
        2: "down",
      },

      elevatorPosition: 0,
      elevatorRealisticPosition: 0,
      elevatorDirection: 1,
      elevatorDoorStatus: 'closed',
      stops: [],

      /**
       * direction elevator is going
       *
       * direction 1 is up
       * direction 2 is down
       *
       */

      elevatorLevelRequests: [],
    };
  },
  mounted() {
    // this.requestElevator({ direction: 1, level: 0 });
    // this.$emit('clear-logs')
    let currentFloor = this.floors.find(f=>f.level === this.elevatorPosition)
    this.$emit('log',`Elevator is on ${currentFloor.label ?? 'Floor '+currentFloor.level}, ${this.elevatorDoorStatus}`)
  },

  watch: {
    elevatorPosition(position) {

      //var move = setInterval(() => {
        if (position < this.elevatorRealisticPosition) {
          --this.elevatorRealisticPosition;
        } else if(position>this.elevatorRealisticPosition) {
          ++this.elevatorRealisticPosition;
        } else {
          // clearInterval(move);
        }
         
      //}, 500);
    },
    elevatorLevelRequests(requests) {
      //find if elevator is at level being requested at
      let isALreadyAt = requests.findIndex(
        (r) =>
          r.level == this.elevatorPosition &&
          r.direction == this.elevatorDirection
      );

      if (isALreadyAt >= 0) {
        requests.splice(isALreadyAt, 1);
      }

      if (requests.length) {
        if (this.elevatorPosition == 0) {
          this.elevatorDirection = 1;
        }

        if (this.elevatorPosition === this.floors.length - 1) {
          this.direction = 2;
        }

        let stops = requests.filter(
          (request) => request.direction == this.elevatorDirection
        );

        if (stops.length) {
          stops.sort(this.sortStops);

          this.elevatorMoveTo(stops[0].level);

          this.$emit("log", `Elevator moved to level ${stops[0].level}`);
        }
      }
    },
  },

  methods: {
    hasAlreadyBeenRequested(request) {
      return this.elevatorLevelRequests.find(
        (r) => request.level === r.level && request.direction == r.direction
      );
    },

    requestElevator(request) {
      let log = "";

      if (this.hasAlreadyBeenRequested(request)) {
        log = `Has already been requested to go ${
          this.directions[request.direction]
        } from floor ${request.level}`;
      } else {
        log = `Requested to go ${
          this.directions[request.direction]
        } from floor number ${request.level}`;
        this.elevatorLevelRequests.push(request);
      }

      this.$emit("log", log);
    },

    sortStops(a, b) {
      return this.elevatorDirection === 1
        ? a.numberProperty + b.numberProperty
        : a.numberProperty - b.numberProperty;
    },

    elevatorMoveTo(level) {
      this.elevatorPosition = level;

      // remove the request
    },
  },
};
</script>
