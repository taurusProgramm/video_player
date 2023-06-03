<template>
  <div class="root">
    <div class="wrapper">
      <video ref="videoPlayer" class="video-player" @timeupdate="getUpdate" @click="togglePlay" @keydown="handleKey">
        <source src="./assets/video/relaxation.mp4" type="video/mp4">
      </video>
      <div class="progress-wrapper">
        <input type="range" 
        id="progress" 
        min="0" 
        :max="duration" 
        step="1" 
        v-model="progressSlider"
        :style="{
          background: `linear-gradient(to right, #cc181e 0%, #cc181e ${progressPercent}%,  #444 ${progressPercent}%, #444 100%)`
        }"
        >
      </div>
      <div class="actions">
        <div class="left-btns">
          <button class="play-btn" @click="togglePlay">
            <div v-if="isPlaying" class="image-pause"></div>
            <div v-else class="image-play"></div>
          </button>
          <div class="laud-box">
            <div class="sound-wrapper" @click="mute">
              <div v-if="!isMuted && volume >= 0.5" class="sound"></div>
              <div v-else-if="!isMuted && volume < 0.5" class="sound_half"></div>
              <div v-else class="mute"></div>
            </div>
            <input type="range" class="input-range" v-model="volume" min="0" max="1" step="0.1">
          </div>
          <div class="time"> {{ currentTime }} / {{ durationMinutes }}:{{ durationSeconds }}</div>
          
        </div>
        <div class="right-btns">
          <div class="fullscreen"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { 
  // computed, 
  onMounted, ref, watch } from 'vue';


const videoPlayer = ref<HTMLVideoElement>();
const isPlaying = ref(false);
const duration = ref(0);
const durationMinutes = ref(0);
const durationSeconds = ref(0);
const currentTime = ref('0:00');
const volume = ref(1);
const isMuted = ref(false);
const progressSlider = ref(0);
const progressPercent = ref(0);
// const updateTimeout = ref<number | null>(null)



const togglePlay = () => {
  if(isPlaying.value) {
    videoPlayer.value?.pause();
  }
  else {
    videoPlayer.value?.play();
  }
  isPlaying.value = !isPlaying.value;
}

function handleKey(event: KeyboardEvent) {
  if(videoPlayer.value){
    if (event.key === 'ArrowLeft') {
      console.log('right');
      
    event.preventDefault(); // Предотвращаем действие по умолчанию (например, прокрутку страницы)
    videoPlayer.value.currentTime -= 5; // Перематываем видео на 5 секунд назад
  } else if (event.key === 'ArrowRight') {
    event.preventDefault();
    videoPlayer.value.currentTime += 5; // Перематываем видео на 5 секунд вперед
  }
  }
}



const mute = () => {
  if(videoPlayer.value){
    if(!videoPlayer.value.muted) {
      videoPlayer.value.muted = true;
      isMuted.value = true;
      volume.value = 0      
    } else {
      videoPlayer.value.muted = false;
      isMuted.value = false;
      volume.value = 1;
    }
  }
}

watch (volume, (v) => {
  if(videoPlayer.value){
    videoPlayer.value.volume = v;
    videoPlayer.value.muted = !v;
    if ( v == 0) isMuted.value = true;
    else isMuted.value = false;
  }
})

// watch(progressSlider, (newVal) =>{
  
//     // videoPlayer.value.currentTime = newVal;
//     if (updateTimeout.value) {
//         clearTimeout(updateTimeout.value);
//       }
//       updateTimeout.value = setTimeout(() => {
//         if(videoPlayer.value){
//           videoPlayer.value.currentTime = newVal;
//           updateTimeout.value = null;
//         }
        
//       }, 300); // Задержка в миллисекундах перед обновлением времени видео
  
// })

// watch(progressSlider, (val)=>{
//   if(videoPlayer.value){
//     videoPlayer.value.currentTime = val;
//   }
// })


const getUpdate = ()=>{
  getCurrentTime();
  sliderUpdate();
  progressPercent.value = Math.round((videoPlayer.value?.currentTime! / duration.value) * 10000) / 100;
}

function sliderUpdate(){
  progressSlider.value = videoPlayer.value?.currentTime!;
  
}

function getCurrentTime(){
  let current = Math.round(videoPlayer.value?.currentTime!);
  let minutes = 0;
  let seconds = '';
  if(current < 60){
    minutes = Math.floor(current / 60);
    seconds = String((current < 10) ? '0' + current : current);
  } else {
    minutes = Math.floor(current / 60);
    const t = Math.round(((current / 60) - minutes) * 60)
    seconds = String((t < 10) ? '0' + t : t);
  }
  currentTime.value = `${minutes}:${seconds}`;
}

const getTime = () => {
  duration.value = Math.round(videoPlayer.value?.duration!);
  durationMinutes.value = Math.floor(duration.value / 60);
  durationSeconds.value = Math.floor(((duration.value / 60) - Math.floor(duration.value / 60)) * 60);
}

onMounted(() => {
  console.log('onMounted');
  videoPlayer.value!.focus();
  videoPlayer.value?.addEventListener('loadedmetadata', () => {
    getTime();
  })
});

</script>

<style>
  @import './style.css';
  .root{
    height: 100%;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .wrapper{
    position: relative;
  }

  .video-player{
    height: 550px;
    display: block;
  }


  .actions{
    position: absolute;
    bottom: 2px;
    left: 0;
    right: 12px;
    height: 51px;
    width: 100%;
    display: flex;
    justify-content: space-between;
  }

  .left-btns {
    display: flex;
  }

  .image-play{
    height: 48px;
    width: 48px;
    background-image: url('@/assets/play-buttton.png');
    background-size: 20px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }
  .image-pause{
    height: 48px;
    width: 48px;
    background-image: url('@/assets/pause.png');
    background-size: 20px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }

  .laud-box{
    height: 48px;
    width: auto;
    display: flex;
    align-items: center;
    margin: 0 5px;
  }

  .laud-box input{
    -webkit-appearance: none !important;
    appearance: none;
    background: white;
    height:3px;
    width: 0;
    transition: width 0.5s ease;
    overflow: hidden;
  }
  .laud-box input::-webkit-slider-thumb{
    -webkit-appearance: none !important;
    background:white;
    height:12px;
    width:12px;
    border-radius: 50%;
    cursor: pointer;
  }
  .laud-box:hover input{
    width: 60px;
    overflow: visible;
  }

  .sound {
    height: 48px;
    width: 48px;
    background-image: url('@/assets/sound.png');
    background-size: 25px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }
  .sound_half {
    height: 48px;
    width: 48px;
    background-image: url('@/assets/sound_half.png');
    background-size: 25px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }
  .mute {
    height: 48px;
    width: 48px;
    background-image: url('@/assets/mute.png');
    background-size: 25px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }

  .time {
    color: white;
    height: 48px;
    width: 80px;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 13px;
  }

  .right-btns{
    display: flex;
  }

  .fullscreen {
    height: 48px;
    width: 48px;
    background-image: url('@/assets/fullscreen.png');
    background-size: 25px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }

  .progress-wrapper{
    width: 100%;
    border: none;
    outline: none;
  }

  #progress {
    -webkit-appearance: none !important;
    appearance: none;
    cursor: pointer;
    position: absolute;
    left: 0;
    bottom: 55px;
    width: 100%;
    /* background: linear-gradient(to right,
    #cc181e 0%, #cc181e 50%,
    #444 50%, #444 100%); */
    height:3px;
    transition: .2s ease;
  }

  .progress-wrapper:hover #progress{
    height: 5px;
  }
  

  #progress::-webkit-slider-thumb {
    -webkit-appearance: none !important;
    background: red;
    height: 0px;
    width: 0px;
    border-radius: 50%;
    transition: opacity .2s ease;
  }

  .progress-wrapper:hover #progress::-webkit-slider-thumb{
    height: 12px;
    width: 12px;
  }

  button{
    border: none;
    outline: none;
    background-color: transparent;
  }
</style>
