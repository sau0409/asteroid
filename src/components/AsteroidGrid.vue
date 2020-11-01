<template>
  <div class="card mt-5">
    <h2 class="card-header">
      {{ header }}
    </h2>
    <transition name="shooting-star">
      <div class="m-3" v-cloak v-if="asteroids.length > 0 && showSummary">
        <p>Showing {{ numAsteroids }} items</p>
        <p>{{ closestObject }} has the shortest miss distance.</p>
      </div>
    </transition>
    <div class="m-3">
      <a href="#" @click="showSummary = !showSummary">Show/Hide Summary</a>
    </div>
    <table
      class="table table-striped"
      :class="[{ 'table-dark': false }, 'table-bordered']"
    >
      <thead class="thead-light">
        <th>S.No</th>
        <th>Name</th>
        <th>Close Approach Date</th>
        <th>Miss Distance</th>
        <th>Remove</th>
      </thead>
      <tbody is="transition-group" name="neoList" v-cloak>
        <tr
          v-for="(asteroid, index) in asteroids"
          :key="asteroid.neo_reference_id"
          :style="getRowStyle(asteroid)"
          style="color: green"
        >
          <td>{{ index + 1 }}</td>
          <td>{{ asteroid.name }}</td>
          <td>{{ getCloseApproachDate(asteroid) }}</td>
          <td>
            <ul v-if="asteroid.close_approach_data.length > 0">
              <li
                v-for="(value, key) in asteroid.close_approach_data[0]
                  .miss_distance"
                :key="key"
              >
                {{ key }}: {{ value }}
              </li>
            </ul>
          </td>
          <td>
            <button class="btn btn-warning" @click="remove(index)">
              Remove
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "asteroid-grid",
  props: ["asteroids", "header"],
  data() {
    return {
      showSummary: true,
    };
  },
  methods: {
    getCloseApproachDate(ast) {
      if (ast.close_approach_data.length > 0) {
        return ast.close_approach_data[0].close_approach_date;
      }
    },
    remove(index) {
      this.$emit("remove", index);
    },
    getRowStyle(ast) {
      if (ast.close_approach_data.length === 0) {
        return { border: "solid 3px red", color: "red" };
      }
    },
  },
  computed: {
    numAsteroids() {
      return this.asteroids.length;
    },
    closestObject() {
      let neoHavingData = this.asteroids.filter((el) => {
        return (
          el.close_approach_data[0] !== undefined &&
          el.close_approach_data[0].miss_distance !== undefined
        );
      });
      let simpleNeo = neoHavingData.map((el) => {
        return {
          name: el.name,
          distanceInMile: el.close_approach_data[0].miss_distance.miles,
        };
      });
      let sortedNeos = simpleNeo.sort(
        (a, b) => a.distanceInMile - b.distanceInMile
      );
      return sortedNeos[0].name;
    },
  },
};
</script>

<style scoped>
[v-cloak] {
  display: none;
}
.shooting-star-leave-to,
.shooting-star-enter {
  transform: translateX(150px) rotate(30deg);
  opacity: 0;
}
.shooting-star-enter-active,
.shooting-star-leave-active {
  transition: all 0.5s ease;
}
.neoList-leave-to,
.neoList-enter {
  opacity: 0;
  transform: translateY(30px);
}
.neoList-enter-active,
.neoList-leave-active {
  transition: all 0.5s ease;
}
</style>