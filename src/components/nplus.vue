<script setup>
import { onMounted, ref } from "vue";

let audioCtx = new AudioContext();
let firstStart = ref(true);


let pitch = ref(1);
let filter = ref(2000);
let delay = ref(0.1);

let filterNode = null;
let delayNode = null;
let centralSource = null;


// async function loadBuffer(url) {
//   const r = await fetch(url);
//   const buffer = await r.arrayBuffer();
//   return await audioCtx.decodeAudioData(buffer);
// }

// async function startLoop() {
//   let bufferSrcNode = audioCtx.createBufferSource();
//   let buffer = await loadBuffer("/loops/ai1_atmosphere_loop_factotum_60_C.mp4");
//   bufferSrcNode.buffer = buffer;
//   bufferSrcNode.connect(audioCtx.destination);
//   bufferSrcNode.start();
// }

// function playNote({
//   duration = 0.15,
//   attack = 0.01,
//   decay = 0.1,
//   sustain = 0.5,
//   release = 2.0,
//   delayTime = 0.5,
//   delayFeedback = 0.8,
// } = {}) {
//   let now = audioCtx.currentTime;

//   // Create
//   let osc = audioCtx.createOscillator();
//   let gain = audioCtx.createGain();
//   let delay = audioCtx.createDelay();
//   let delayFeedbackGain = audioCtx.createGain();

//   // Configure
//   osc.frequency.value = 261.63;
//   gain.gain.setValueAtTime(0, now);
//   gain.gain.linearRampToValueAtTime(0.3, now + attack);
//   gain.gain.linearRampToValueAtTime(0.3 * sustain, now + attack + decay);
//   gain.gain.setValueAtTime(0.3 * sustain, now + duration);
//   gain.gain.setTargetAtTime(0, now + duration, release * 0.2);
//   delay.delayTime.value = delayTime;
//   delayFeedbackGain.gain.value = delayFeedback;

//   // Connect
//   osc.connect(gain);
//   gain.connect(audioCtx.destination);
//   gain.connect(delay);
//   delay.connect(audioCtx.destination);
//   delay.connect(delayFeedbackGain);
//   delayFeedbackGain.connect(delay);

//   // Start
//   osc.start();
//   osc.stop(audioCtx.currentTime + duration + release);
// }

// async function startEverything() {
//   await audioCtx.resume();
//   await startLoop();
// }

// async function toggleAudio() {
//   if (firstStart.value) {
//     await startEverything();
//     firstStart.value = false;
//   } else if (audioCtx.state === "running") {
//     await audioCtx.suspend();
//   } else {
//     await audioCtx.resume();
//   }
// }




// -------------- // Funzioni per caricare e riprodurre audio con effetti random

async function loadAudioBuffer(url) {
  const response = await fetch(url);
  const arrayBuffer = await response.arrayBuffer();
  return await audioCtx.decodeAudioData(arrayBuffer);
}

// Unisce intro, centrale (modificato), outro senza pause
async function playSeamlessSequence(introUrl, centralUrl, outroUrl) {
  const introBuffer = await loadAudioBuffer(introUrl);
  const centralBuffer = await loadAudioBuffer(centralUrl);
  const outroBuffer = await loadAudioBuffer(outroUrl);

  const now = audioCtx.currentTime;

  // Intro
  const introSource = audioCtx.createBufferSource();
  introSource.buffer = introBuffer;
  introSource.connect(audioCtx.destination);
  introSource.start(now);

  // Centrale con effetti random
  const centralSource = audioCtx.createBufferSource();
  centralSource.buffer = centralBuffer;
  applyRealtimeEffects(centralSource);
  centralSource.start(now + introBuffer.duration);

  // Outro
  const outroSource = audioCtx.createBufferSource();
  outroSource.buffer = outroBuffer;
  outroSource.connect(audioCtx.destination);
  outroSource.start(now + introBuffer.duration + centralBuffer.duration);
}

function applyRealtimeEffects(sourceNode) {
  sourceNode.playbackRate.value = pitch.value;

  filterNode = audioCtx.createBiquadFilter();
  filterNode.type = "lowpass";
  filterNode.frequency.value = filter.value;

  delayNode = audioCtx.createDelay();
  delayNode.delayTime.value = delay.value;

  sourceNode.connect(filterNode);
  filterNode.connect(delayNode);
  delayNode.connect(audioCtx.destination);
}

function updatePitch() {
  if (centralSource) centralSource.playbackRate.value = pitch.value;
}
function updateFilter() {
  if (filterNode) filterNode.frequency.value = filter.value;
}
function updateDelay() {
  if (delayNode) delayNode.delayTime.value = delay.value;
}

// Funzione principale da chiamare
async function playRandomizedSequence() {
  await audioCtx.resume();
  await playSeamlessSequence(
    "/audio/intro.mp3",
    "/audio/outro.mp3",
    "/audio/outro.mp3"
  );
}

async function toggleAudio() {
  if (firstStart.value) {
    await playRandomizedSequence();
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
    <!-- <span @click="toggleAudio">Audio</span> -->
    <!-- <span @click="playNote">Note</span> -->
    <span @click="toggleAudio">Sequence</span>
    <div>
      <label>Pitch: <input type="range" @input="updatePitch" min="0.5" max="2" step="0.01" value="1"></label>
      <label>Filtro (Hz): <input type="range" @input="updateFilter" min="200" max="5000" step="1" value="2000"></label>
      <label>Delay (s): <input type="range" @input="updateDelay" min="0" max="1" step="0.01" value="0.1"></label>
    </div>
  </div>
</template>

<style scoped></style>
