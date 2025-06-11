<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const actions = [
  "RECALIBRATING", "REDISTRIBUTING", "MONITORING", "SECURING", "ANALYZING",
  "REINFORCING", "OPTIMIZING", "VALIDATING", "EXECUTING", "ADJUSTING",
  "ENGAGING", "TRANSMITTING", "ASSESSING", "UPDATING", "COMPENSATING",
  "SCANNING", "RESOLVING", "SYNCHRONIZING", "ENCRYPTING", "DECRYPTING",
  "ROUTING", "RELINKING", "AMPLIFYING", "PROVISIONING", "ISOLATING", "RESTRUCTURING"
];

const objects = [
  "NEURAL WEIGHT MATRICES", "COGNITIVE MESH LOAD", "INTER-NODE COHERENCE",
  "SECURE CHANNEL HANDSHAKE", "BIO-SIGNAL ANOMALY STREAMS", "NEURO-MESH REDUNDANCIES",
  "MESH TOPOLOGY", "CONSENSUS SIGNATURE", "CROSS-CLUSTER PARITY CHECK",
  "NEURAL THREAD DENSITY", "FALLBACK NEURAL ROUTES", "DEEP HEURISTICS",
  "EXTERNAL SIGNAL NOISE FLOOR", "COGNITIVE FIREWALL HEURISTICS",
  "ADAPTIVE ROUTING PATHS", "MESH SIGNAL PATTERNS", "MESH NODE CONSENSUS",
  "NEURAL ENTROPY BALANCE", "CLASSIFIED OPS DATA PACKETS", "NEURAL MESH INTEGRITY",
  "MEMETIC PATTERN RECOGNITION", "EXAFLOP COGNITIVE ALLOCATION",
  "QUANTUM ROUTING NODES", "TACTICAL FEEDBACK CHANNELS", "PSYCHO-SEMANTIC VECTORS"
];

const prefixes = [
  "// ", ">> ", ":: ", "... ", "### ", "** ", "[[ ", "~~ "
];

const suffixes = [
  " //", " ::", " >>", "...", " ###", " **", " ]]", " ~~"
];

function randomItem(arr) {
  return arr[Math.floor(Math.random() * arr.length)];
}

function randomDelay() {
  return Math.random() * 450 + Math.random() * 750;
}

const phrase = ref('// SYSTEM INITIALIZING //');
let timer = null;

function updateText() {
  phrase.value =
    randomItem(prefixes) +
    randomItem(actions) + ' ' +
    randomItem(objects) +
    randomItem(suffixes);
  timer = setTimeout(updateText, randomDelay());
}

onMounted(() => {
  updateText();
});

onBeforeUnmount(() => {
  if (timer) clearTimeout(timer);
});
</script>

<template>
  <span id="text" class="random-words">{{ phrase }}</span>
</template>


<style scoped>
.random-words {
  white-space: nowrap;
  color: white;
  background: transparent;
  z-index: 100;
  height: 100%;
}
</style>