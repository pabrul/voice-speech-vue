<script setup>
import { ref, onMounted } from "vue";

const transcript = ref("");
const isRecording = ref(false);

const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition;
const speech = new Recognition();

onMounted(() => {
  speech.continuous = true;
  speech.interimResults = true;

  speech.onstart = () => {
    console.log("speech Starded");
    isRecording.value = true;
  };

  speech.onend = () => {
    console.log("speech Stopped");
    isRecording.value = false;
  };

  speech.onresult = (evt) => {
    for (let i = 0; i < evt.results.length; i++) {
      const result = evt.results[i];

      if (result.finalRecognization) checkForCommand(result);
    }
    const finalSpeech = Array.from(evt.results)
      .map((result) => result[0])
      .map((result) => result.transcript)
      .join("");

    transcript.value = finalSpeech;
  };
});

const checkForCommand = (result) => {
  if (result[0].transcript.includes("stop recording")) {
    speech.stop();
  }
};

const toggleMic = () => {
  if (isRecording.value) {
    speech.stop();
  } else {
    speech.start();
  }
};
</script>

<template>
  <div class="app">
    <button :class="`mic`" @click="toggleMic">Record</button>
    <div class="transcript" v-text="transcript"></div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Fira Sans", sans-serif;
}

body {
  background: #281936;
  color: #fff;
}
</style>
