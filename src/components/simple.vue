<script setup>
import { ref } from "vue";

let audioCtx = new AudioContext();
let firstStart = ref(true);

let pitch = ref(1);
let filter = ref(2000);
let delay = ref(0.1);

let filterNode = null;
let delayNode = null;
let sourceNode = null;

async function loadAudioBuffer(url) {
  const response = await fetch(url);
  const arrayBuffer = await response.arrayBuffer();
  return await audioCtx.decodeAudioData(arrayBuffer);
}

async function playTrack(url) {
  const buffer = await loadAudioBuffer(url);

  sourceNode = audioCtx.createBufferSource();
  sourceNode.buffer = buffer;
  sourceNode.loop = true; // così puoi testare a lungo

  filterNode = audioCtx.createBiquadFilter();
  filterNode.type = "lowpass";
  filterNode.frequency.value = filter.value;

  delayNode = audioCtx.createDelay();
  delayNode.delayTime.value = delay.value;

  sourceNode.playbackRate.value = pitch.value;

  sourceNode.connect(filterNode);
  filterNode.connect(delayNode);
  delayNode.connect(audioCtx.destination);

  sourceNode.start();
}

function updatePitch() {
  if (sourceNode) sourceNode.playbackRate.value = pitch.value;
}
function updateFilter() {
  if (filterNode) filterNode.frequency.value = filter.value;
}
function updateDelay() {
  if (delayNode) delayNode.delayTime.value = delay.value;
}

async function toggleAudio() {
  if (firstStart.value) {
    await audioCtx.resume();
    await playTrack("/audio/intro.mp3"); // usa la tua traccia di test
    firstStart.value = false;
  } else if (audioCtx.state === "running") {
    await audioCtx.suspend();
  } else {
    await audioCtx.resume();
  }
}
</script>

<template>
  <div style="display: flex; gap: 2rem; flex-direction: column;">
    <span @click="toggleAudio">Play/Pausa</span>
    <div>
      <label>
        Pitch:
        <input type="range" v-model="pitch" min="0.5" max="2" step="0.01" @input="updatePitch">
      </label>
      <label>
        Filtro (Hz):
        <input type="range" v-model="filter" min="200" max="5000" step="1" @input="updateFilter">
      </label>
      <label>
        Delay (s):
        <input type="range" v-model="delay" min="0" max="1" step="0.01" @input="updateDelay">
      </label>
    </div>
  </div>
</template>