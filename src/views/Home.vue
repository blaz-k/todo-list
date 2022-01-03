<template>
  <div class="text-center">
    <p>Home: {{ count }}</p>
    <button class="btn btn-primary" @click="add">Count</button>
  </div>
</template>

<script lang="ts">
export default {
  name: "Home",

  data() {
    return {
      count: 0
    }
  },

  methods: {
    add() {
      this.count += 1;
    }
  },
}

</script>
