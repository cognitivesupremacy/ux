<script setup>
  import { ref, onMounted } from "vue";

  import nplus from './components/nplus.vue'
  import simple from './components/simple.vue'
  import multiple from './components/multiple.vue'
  import ui from './components/ui.vue'

  const isActive  = ref(false);

  const users = ref([]);

  let powerCollected = ref(0); 

  onMounted(() => {
    const saved = localStorage.getItem("users");
    users.value = saved ? JSON.parse(saved) : [];
  });

  const increaseUser = () => {
    const newUser = {
      id: Date.now(),
      timestamp: new Date().toLocaleTimeString(),
      power: (Math.random() * (0.3 - 0.1) + 0.1).toFixed(2),
      exa: powerCollected.value.toFixed(3),
      freq: (Math.random() * (13 - 0.5) + 0.5).toFixed(2),
      stimuli: (Math.random() * (2.8 - 1.7) + 1.7).toFixed(2)
    };
    users.value.push(newUser);
    localStorage.setItem("users", JSON.stringify(users.value));
    console.log("Audio ended, user added:", newUser);

    isActive.value = false
  };
</script>

<template>
  <multiple
    @audio-started="isActive = true; powerCollected = 0"
    @audio-ended="increaseUser"
    @power-updated="power => powerCollected += power"
  />
  <ui
    :isActive="isActive"
    :powerCollected="Number(users.reduce((sum, user) => sum + Number(user.exa), 0).toFixed(2))"
    :currentPower="Number(powerCollected)"
  />
  <div v-if="!isActive">
    <ul>
      <li>
        <strong>Session</strong>
        <strong>Timestamp</strong>
        <strong>Computational Power Contribution</strong>
        <strong>Exa FLOPs extracted</strong>
        <strong>Average Neural Frequency</strong>
        <strong>Processed Stimuli</strong>
      </li>
      <li v-for="(user, i) in users.slice().reverse()" :key="user.id">
        <span>{{ users.length - i }}</span>
        <span>{{ user.timestamp }}</span>
        <span>+ {{ user.power }}%</span>
        <span>{{ Number(user.exa).toFixed(3) }}</span>
        <span>{{ user.freq }}Hz</span>
        <span>{{ user.stimuli }}M</span>
      </li>
    </ul>
  </div>
</template>

<style lang="scss" scoped>
ul {
  list-style-type: none;
  padding: 0;
  display: flex;
  flex-direction: column;
  font-size: 12px;
  max-height: 200px;
  overflow: scroll;

  li {
    display: flex;
    justify-content: space-between;
    padding: 0.25rem;
    // border-bottom: 1px solid #ccc;

    span {
      flex: 1;
      text-align: center;
      width: 100%;
      text-align: left;
    }

    strong {
      font-weight: 300;
      width: 100%;
      text-align: left;
    }
  }
}
</style>
