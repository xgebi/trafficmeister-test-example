<template>
  <div class="layout">
    <h1>TrafficMeister</h1>
    <div class="error" v-if="err">{{ err }}</div>
    <nav>
      <section>
        <label :for="'type'">Types:</label>
        <br />
        <select id="type" v-model="type" :disabled="vehicles?.length === 0">
          <option value=""></option>
          <option v-for="type in this.types" v-bind:value="type" v-bind:key="type">{{ type }}</option>
        </select>
      </section>
      <section>
        <label :for="'brand'">Brands:</label>
        <br />
        <select id="brand" v-model="brand" :disabled="vehicles?.length === 0">
          <option value=""></option>
          <option v-for="brand in this.brands" v-bind:value="brand" v-bind:key="brand">{{ brand }}</option>
        </select>
      </section>
      <section>
        <label :for="'colors'">Colors:</label>
        <br />
        <select id="colors" v-model="color" :disabled="vehicles?.length === 0">
          <option value=""></option>
          <option v-for="color in this.colors" v-bind:value="color" v-bind:key="color">{{ color }}</option>
        </select>
      </section>
    </nav>
    <article>
      <img
          v-if="brand.length > 0"
          v-bind:src="vehicles.find(vehicle => brand === vehicle.brand).img"
          v-bind:alt="vehicles.find(vehicle => brand === vehicle.brand).brand"
      />
    </article>
  </div>
</template>

<script>
import trafficMeister from '@/service/index';

export default {
  name: "Form",
  data() {
    return {
      vehicles: [],
      type: "",
      brand: "",
      color: "",
      err: null,
    }
  },
  computed: {
    types() {
      let local = [...this.vehicles];
      if (this.brand) {
        local = local.filter(vehicle => this.brand === vehicle.brand)
      }
      if (this.color) {
        local = local.filter(vehicle => vehicle.colors.indexOf(this.color) > -1);
      }
      return [...new Set(local.map(vehicle => vehicle.type))]
    },
    brands() {
      let local = [...this.vehicles];
      if (this.type) {
        local = local.filter(vehicle => this.type === vehicle.type)
      }
      if (this.color) {
        local = local.filter(vehicle => vehicle.colors.indexOf(this.color) > -1);
      }
      return [...new Set(local.map(vehicle => vehicle.brand))]
    },
    colors() {
      let temp = [...this.vehicles];
      if (this.brand) {
        temp = temp.filter(vehicle => this.brand === vehicle.brand)
      }
      if (this.type) {
        temp = temp.filter(vehicle => this.type === vehicle.type)
      }
      let local = [];
      for (const vehicle of temp) {
        local.push(...vehicle.colors);
      }
      return [...new Set(local)];
    }
  },
  mounted() {
    const self = this;
    trafficMeister.fetchData(function(err, data) {
      if (!err) {
        self.vehicles = data;
      } else {
        self.err = "Could not load data"
      }
    });
  }
}
</script>

<style scoped>
.layout {
  display: grid;
  grid-template-columns: 1fr 17rem 29rem 1fr;
  grid-template-rows: 1fr 3rem auto 1fr;
  gap: 1rem;
  align-items: start;
  font-family: sans-serif;
  height: 100%;
}

h1 {
  grid-column: 2 / 4;
  grid-row: 2 / 3;
  text-align: center;
  font-size: 3rem;
  margin: 0;
}

.error {
  grid-column: 2 / 4;
  text-align: center;
}

nav {
  display: flex;
  text-align: left;
  grid-column: 2 / 3;
  grid-row: 3 / 4;
  flex-flow: column;
  align-items: stretch;
  gap: 1rem;
}

nav section {
  flex: 1 1 33%;
}

nav label {
  font-size: 1.25rem;
}

nav select {
  font-size: 1.25rem;
  padding: 0.25rem 0.5rem;
  width: 100%;
}

article {
  grid-column: 3 / 4;
  grid-row: 3 / 4;
}

article img {
  max-width: 29rem;
}
</style>