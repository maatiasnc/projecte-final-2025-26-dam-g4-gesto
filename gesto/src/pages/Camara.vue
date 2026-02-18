<template>
<div class = "camera-container">
    <video ref="videoRef" autoplay muted playsinline class="fullscreen-video"></video>
    <button @click="switchCamera" class="switch-btn">🔄</button>
    <p v-if="error" class="error-msg">{{ error }}</p>
</div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const videoRef = ref(null);
const error = ref(null);
const facingMode = ref('environment');
let currentStream = null;

const startCamera = async ()=> {
    if (currentStream){
     currentStream.getTracks().forEach(track => track.stop());
    }
}
const constraints = {
    video: {
      facingMode: facingMode.value,
      width: { ideal: 1920 },
      height: { ideal: 1080 }
    },
    audio: false 
  };
  try {
    const stream= await navigator.mediaDevices.getUserMedia(constraints);
    currentStream=stream;
    if(videoRef.value){
        videoRef.value.srcObject = stream;
    }  
    error.value=null;

  } catch (error) {
    console.error("Error al accedir a la càmera:", error);
    error.value = "No s'ha pogut accedir a la càmera. Verifica els permisos.";
  }

const switchCamera = () => {
    facingMode.value = facingMode.value === 'environment' ? 'user' : 'environment';
    startCamera();
}
onMounted(()=>{
    startCamera();
});
onBeforeUnmount(()=>{
    if(currentStream){
        currentStream.getTracks().forEach(track => track.stop());
    }
});

</script>
<style scoped>
.camera-container {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background-color: black;
}

.fullscreen-video {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.switch-btn {
  position: absolute;
  bottom: 40px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
  
  background-color: rgba(255, 255, 255, 0.9);
  border: none;
  border-radius: 50px;
  padding: 12px 24px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  box-shadow: 0 4px 10px rgba(0,0,0,0.3);
  transition: transform 0.2s;
}

.switch-btn:active {
  transform: translateX(-50%) scale(0.95);
}

.error-msg {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: white;
  background: rgba(255, 0, 0, 0.7);
  padding: 20px;
  border-radius: 8px;
  text-align: center;
}
</style>