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
  },
  id: {
    type: Number,
    default: 0,
  }
});
</script>

<template>
  <header>
    <div>
      <img src="/nplus-logo.svg" alt="" width="110px">
    </div>

    <div v-if="!isActive">
      <span>{{ (powerCollected/90*100).toFixed(2) + '%' }}</span>
      <span>ORGANON™ requirement </span>
    </div>
    <div v-else>
      <span>SESSION {{ id }}</span>
      <span>{{ currentPower.toFixed(3) }} exaFLOPs</span>
    </div>

    
    <div v-if="!isActive">
      <span>cumulated computational <br> power extracted</span>
      <!-- <span>{{ powerCollected.toFixed(3) }} exaFLOPs</span> -->
    </div>
    <div v-else>
      <span>Current computational power extracted</span>
    </div>

    <div @click="$emit('toggleAudio')">
      <button>{{ isActive ? 'STOP BRAIN MINING' : 'START BRAIN MINING' }}</button>
    </div>

  </header>
  <main v-if="!isActive">
    <div class="loader-values">
      <span>0%</span>
      <span>10%</span>
      <span>20%</span>
      <span>30%</span>
      <span>40%</span>
      <span>50%</span>
      <span>60%</span>
      <span>70%</span>
      <span>80%</span>
      <span>90%</span>
      <span>100%</span>
    </div>
    <div class="loader-bar">
      <div class="inner" :style="{ width: (powerCollected/90*100).toFixed(2) + '%' }"></div>
    </div>
    <div class="loader-values">
      <span>0</span>
      <span>5</span>
      <span>10</span>
      <span>15</span>
      <span>20</span>
      <span>25</span>
      <span>30</span>
      <span>35</span>
      <span>40</span>
      <span>45</span>
      <span>50</span>
      <span>55</span>
      <span>60</span>
      <span>65</span>
      <span>70</span>
      <span style="transform: translateX(-100%);">EXAFLOPS</span>
    </div>
  </main>
</template>

<style lang="scss" scoped>
  header {
    display: grid;
    grid-template-columns: 2fr 2fr 2fr 2fr;
    justify-content: space-between;
    align-items: start;
    justify-content: start;
    color: white;
    width: calc(100vw - 1rem);
    // left: 0;
    // position: fixed;
    // top: 0;
    font-size: 12px;
    line-height: 1;
    text-transform: uppercase;
    font-weight: 400;

    div {
      display: flex;
      flex-direction: column; 
      height: 100%;
    }

    div::before {
      content: "nplus";
      position: relative;
      width: 50px;
      height: 10px;
      color: transparent;
      background-image: url(/cosino.png);
      background-repeat: no-repeat;
      margin-bottom: 0.5rem;
    }

    div::after {
      content: "nplus";
      position: absolute;
      bottom: 0;
      width: 50px;
      height: 10px;
      color: transparent;
      background-image: url(/cosino.png);
      background-repeat: no-repeat;
      margin-bottom: 0.5rem;
    }

    span {
      display: flex;
      flex-direction: column;
      font-size: 12px;
      text-align: left;
    }

    button {
      width: 100%;
      line-height: 1;
      background: white;
      border-radius: 0;
      color: black;
      height: 14px;
      padding: 0;
      margin: 0;
    }
  }

  main {
    margin: 20px 0;

    .loader-values {
      display: flex;
      width: 100%;
      justify-content: space-between;
      font-size: 9px;
      margin-bottom: 5px;
      margin-top: 5px;
      text-align: left;

      span {
        width: 24px;
      }
    }
    
    .loader-bar {
      width: 100%;
      height: 36px;
      background-color: #1B1B1B;
      
      .inner {
        height: 100%;
        background-image: url(loader.jpg);
        background-repeat: repeat;
        background-size: 100%;
      }
    }
  }
</style>
