<script setup>
import { ref } from "vue";

const emit = defineEmits(["audio-started", "audio-ended", "power-updated"]);

const random = (min, max) => Math.random() * (max - min) + min;

let audioCtx = new AudioContext();
let firstStart = ref(true);

let powerInterval = null; // Variabile globale per l'id dell'intervallo

let filter = ref(2000);
let delay = ref(0.1);

let globalFilterNode = null;
let globalDelayNode = null;
let sources = [];

async function loadAudioBuffer(url) {
  const response = await fetch(url);
  const arrayBuffer = await response.arrayBuffer();
  return await audioCtx.decodeAudioData(arrayBuffer);
}

function disconnectAndCleanup() {
  // Stoppa tutte le sorgenti e scollega i nodi globali
  sources.forEach(src => {
    try { src.stop(); } catch {}
    try { src.disconnect(); } catch {}
  });
  sources = [];
  if (globalFilterNode) {
    try { globalFilterNode.disconnect(); } catch {}
    globalFilterNode = null;
  }
  if (globalDelayNode) {
    try { globalDelayNode.disconnect(); } catch {}
    globalDelayNode = null;
  }

  if (powerInterval) {
    clearInterval(powerInterval);
    powerInterval = null;
  }
}

async function playSeamlessSequence(introUrl, centralUrl, outroUrl) {
  disconnectAndCleanup();

  emit("audio-started");
  
  powerInterval = setInterval(() => {
    emit("power-updated", Number(random(0, 0.03).toFixed(3)));
  }, 1000*random(1, 3));

  // Crea filtro e delay globali
  globalFilterNode = audioCtx.createBiquadFilter();
  globalFilterNode.type = "lowpass";
  globalFilterNode.frequency.value = filter.value;

  globalDelayNode = audioCtx.createDelay();
  globalDelayNode.delayTime.value = delay.value;

  globalFilterNode.connect(globalDelayNode);
  globalDelayNode.connect(audioCtx.destination);

  const introBuffer = await loadAudioBuffer(introUrl);
  const centralBuffer = await loadAudioBuffer(centralUrl);
  const outroBuffer = await loadAudioBuffer(outroUrl);

  const now = audioCtx.currentTime;

  // Intro
  const introSource = audioCtx.createBufferSource();
  introSource.buffer = introBuffer;
  introSource.connect(globalFilterNode);
  introSource.start(now);
  sources.push(introSource);

  // Centrale
  const centralSource = audioCtx.createBufferSource();
  centralSource.buffer = centralBuffer;
  centralSource.connect(globalFilterNode);
  centralSource.start(now + introBuffer.duration);
  sources.push(centralSource);

  // Outro
  const outroSource = audioCtx.createBufferSource();
  outroSource.buffer = outroBuffer;
  outroSource.connect(globalFilterNode);
  outroSource.start(now + introBuffer.duration + centralBuffer.duration);
  sources.push(outroSource);

  outroSource.onended = () => {
    if (powerInterval) {
      clearInterval(powerInterval);
      powerInterval = null;
    }
    emit("audio-ended");
    firstStart.value = true;
  };
}

function updateFilter() {
  if (globalFilterNode) globalFilterNode.frequency.value = filter.value;
}
function updateDelay() {
  if (globalDelayNode) globalDelayNode.delayTime.value = delay.value;
}

async function toggleAudio() {
  if (firstStart.value) {
    await audioCtx.resume();
    await playSeamlessSequence(
      "/audio/intro.mp3",
      "/nplus/bb_A_loop.wav",
      "/audio/outro.mp3"
    );
    firstStart.value = false;
  } else if (audioCtx.state === "running") {
    audioCtx.suspend();
  } else {
    audioCtx.resume();
  }
}
</script>

<template>
  <div style="display: flex; gap: 2rem; flex-direction: column">
    <span @click="toggleAudio" style="cursor: pointer">
      Play/Pausa Sequenza
    </span>
    <div>
      <label>
        Filtro (Hz):
        <input
          type="range"
          v-model="filter"
          min="200"
          max="5000"
          step="1"
          @input="updateFilter"
        />
      </label>
      <label>
        Delay (s):
        <input
          type="range"
          v-model="delay"
          min="0"
          max="1"
          step="0.01"
          @input="updateDelay"
        />
      </label>
    </div>
  </div>
</template>