<script setup>
  import { ref, onMounted } from "vue";

  import nplus from './components/nplus.vue'
  import simple from './components/simple.vue'
  import multiple from './components/multiple.vue'
  import ui from './components/ui.vue'

  import { supabase } from './utils/supabase'
  const todos = ref([])

  async function getTodos() {
    const { data } = await supabase.from('sonar').select()
    todos.value = data
  }

  async function addEntry(user) {
    const { data, error } = await supabase
      .from('sonar')
      .insert([{
        neural_stram: user.power + '%',
        session: users.value.length,
        time: user.timestamp,
        date: user.date
      }])

    if (error) {
      console.error('Errore inserimento:', error.message)
    } else {
      console.log('Dati inseriti in Supabase:', data)
    }
  }

  onMounted(() => {
    getTodos()
  })
  // import {Niivue} from '@niivue/niivue'
  // const nv = new Niivue()

  // const volumeList = [
  //   {
  //     url: "/mni152.nii.gz",
  //   }
  // ]

  const isActive  = ref(false);

  const users = ref([]);

  let powerCollected = ref(0); 

  onMounted(async () => {
    const saved = localStorage.getItem("users");
    users.value = saved ? JSON.parse(saved) : [];

    // nv.attachTo('gl')
    // await nv.loadVolumes(volumeList)
  });

  const increaseUser = () => {
    const newUser = {
      id: Date.now(),
      date: new Date().toLocaleDateString('it-IT'),
      timestamp: new Date().toLocaleTimeString(),
      power: (Math.random() * (0.3 - 0.1) + 0.1).toFixed(2),
      exa: powerCollected.value.toFixed(3),
      freq: (Math.random() * (13 - 0.5) + 0.5).toFixed(2),
      stimuli: (Math.random() * (2.8 - 1.7) + 1.7).toFixed(2)
    };
    users.value.push(newUser);
    localStorage.setItem("users", JSON.stringify(users.value));
    console.log("Audio ended, user added:", newUser);

    addEntry(newUser)

    isActive.value = false
  };

  const audio = ref(false)
</script>

<template>
  <multiple
    @audio-started="isActive = true; powerCollected = 0"
    @audio-ended="increaseUser"
    @power-updated="power => powerCollected += power"
    :audioStatus="audio"
  />
  <ui
    @toggleAudio="audio = !audio"
    :isActive="isActive"
    :powerCollected="Number(users.reduce((sum, user) => sum + Number(user.exa), 0).toFixed(2))"
    :currentPower="Number(powerCollected)"
    :id="Number(users.length) + 1"
  />
  <div v-if="!isActive">
    <ul>
      <li>
        <strong>Sonar+D</strong>
        <strong>Session</strong>
        <strong>TIME</strong>
        <strong>Neural Processing Stream</strong>
      </li>
      <li v-for="(user, i) in users.slice().reverse()" :key="user.id">
        <span>{{ user.date }}</span>
        <span>{{ (users.length - i).toString().padStart(4, '0') }}</span>
        <span>{{ user.timestamp }}</span>
        <span>+ {{ user.power }}%</span>
      </li>
    </ul>
  </div>

  <div v-else class="video-container">
    <video src="/brain_x.mp4" loop muted playsinline autoplay></video>
    <video src="/brain_z.mp4" loop muted playsinline autoplay></video>
  </div>
  <!-- <div style="height: -webkit-fill-available; overflow: hidden;" :style="isActive ? 'display:block' : 'display:none'">
    <canvas id="gl" style="width: 100vw;"></canvas>
  </div> -->
</template>

<style lang="scss" scoped>
  .video-container {
    display: flex;
    width: 100%;
    height: webkit-fill-available;

    video {
      width: 50%;
      height: 100%;
    }
  }

  ul {
    list-style-type: none;
    padding: 0;
    display: flex;
    flex-direction: column;
    font-size: 12px;
    overflow: hidden;
    margin: 0;
    height: 380px;
    color: white;
    padding-top: 20px;

    li {
      display: flex;
      justify-content: space-between;
      padding: 0;
      height: 12px;
      text-transform: uppercase;
      width: 100%;
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
