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
              <h6 class="mb-0">Floor {{ floor.level }}</h6>
              <div
                class="ml-4 elevator--indicator"
                :class="{ active: floor.level == elevatorPosition }"
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
        { level: 0 },
      ],

      elevatorPosition: 0,
      elevatorDirectionUp: true,

      /**
       * direction elevator is going
       *
       * direction 1 is up
       * direction 2 is down
       *
       */
      
      elevatorLevelRequests: [{ direction: 1, level: 0 }],
    };
  },
  methods: {
    hasAlreadyBeenRequested(request) {
      return this.elevatorLevelRequests.find(
        (r) => request.level === r.level && request.direction == r.direction
      );
    },
    requestElevator(request) {
      let directions = {
        1: "up",
        2: "down",
      };

      {
        this.$emit(
          "log",
          this.hasAlreadyBeenRequested(request)
            ? `Has already been requested to go ${
                directions[request.direction]
              } from floor ${request.level}`
            : `Requested to go ${
                directions[request.direction]
              } from floor number ${request.level}`
        );
        this.elevatorLevelRequests.push(request);
      }
    },
  },
};
</script>
