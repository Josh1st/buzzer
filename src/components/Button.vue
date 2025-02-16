<template>
  <div class="container">
    <div class="button_container">
    <button class="button" @mousedown="startBuzzer">
      
    </button>
  </div>
  </div>

</template>

<script setup>
import { useWakeLock } from '@vueuse/core'
import { ref, onMounted, watch } from 'vue'

const { isSupported, request, release } = useWakeLock()
const pushed = ref(false)

const audio = new Audio("/buzzer/buzzer.wav")
audio.preload = "auto"

const startBuzzer = async () => {
  if (!pushed.value && audio.paused) { // Only play if not already playing
    pushed.value = true
    try {
      await audio.play()
    } catch (err) {
      console.error("Audio play error:", err)
    }

    audio.onended = () => {
      pushed.value = false // Reset after sound finishes
    }
  }
}

const stopBuzzer = () => {
  if (!audio.paused) {
    audio.pause()
    audio.currentTime = 0
    pushed.value = false
  }
}

onMounted(async () => {
  if (isSupported.value) {
    await request()
  }
})

</script>


<style scoped>
.container {
  padding: 0;
  margin: 0;
  min-height: 100vh;
  width: 100vw;
  position: relative;
}
.button_container{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 90%;
  padding-bottom: 90%;
}
.button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  height: 100%;
  border-radius: 100%;
  background: radial-gradient(100% 100% at 50% 0%, #FF0000 76%, #990000 100%);
  box-shadow: 0px 0px 50px rgba(255, 0, 0, 0.5);
  cursor: pointer;
}
.button:active {
  background: radial-gradient(100% 100% at 50% 0%, #CB0000 76%, #6C0000 100%);
}
</style>
