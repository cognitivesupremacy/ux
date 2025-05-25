<script setup>
defineProps({
  isActive: {
    type: Boolean,
    default: false,
  },
  powerCollected: {
    type: Number,
    default: 0,
  },
  currentPower: {
    type: Number,
    default: 0,
  }
});
</script>

<template>
  <header>
    <span> logo </span>
    <div>
      <div v-if="!isActive">
        <span>51.34%</span>
        <span>ORGANON requirement</span>
      </div>
      <div v-else>
        <span>N+BMx01TM</span>
        <span>BRAIN MINING ACTIVE</span>
      </div>
    </div>
    <div v-if="!isActive">
      <span>Total brain computational power extracted</span>
      <span>{{ powerCollected.toFixed(3) }} exaFLOPs</span>
    </div>
    <div v-else>
      <span>Current brain computational power extracted</span>
      <span>{{ currentPower.toFixed(3) }} exaFLOPs</span>
    </div>
  </header>
  <main></main>
</template>

<style lang="scss" scoped>
header {
  display: grid;
  grid-template-columns: 2fr 2fr 2fr;
  justify-content: space-between;
  align-items: start;
  justify-content: start;
  padding: 0.5rem;
  color: white;
  width: 100vw;
  left: 0;
  position: fixed;
  top: 0;

  span {
    display: flex;
    flex-direction: column;
    font-size: 0.6rem;
    font-weight: bold;
    text-align: left;
  }
}
</style>
