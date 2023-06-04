<template>
  <div class="root">
    <div class="wrapper" ref="wrapper" @mousemove="handleMouseMove" @mouseleave="handleMouseLeave" :class="{'hide-cursor': !isCursorMoving}">
      <video ref="videoPlayer" class="video-player" @timeupdate="getUpdate" @click="togglePlay" >
        <source src="./assets/video/relaxation.mp4" type="video/mp4">
      </video>
      <div class="progress-wrapper" >
        <input type="range"
        @input="handleInput" 
        class="progress" 
        :class="{'progress-down': isAddHideActions}"
        min="0" 
        :max="duration" 
        step="1" 
        v-model="progressSlider"
        :style="{
          background: `linear-gradient(to right, #cc181e 0%, #cc181e ${progressPercent}%,  #444 ${progressPercent}%, #444 100%)`
        }"
        >
      </div>
      <div class="actions" :class="{'move-down': isAddHideActions}">
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
          <div class="time">  {{ currentTime }} / {{ fullTimeVideo }} </div>
          <!-- <div class="time">  {{ fullTimeVideo }}</div> -->
        </div>
        <div class="right-btns">
          <!-- <div v-if="!isFullscreen" class="no-fullscreen" @click="toggleFullscreen"></div> -->
          <!-- <div v-else class="fullscreen" @click="toggleFullscreen"></div> -->
          <div :class="isFullscreen ? 'fullscreen': 'no-fullscreen'" @click="toggleFullscreen"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { 
  // computed, 
  onMounted, onUnmounted, ref, watch } from 'vue';


const videoPlayer = ref<HTMLVideoElement>();
const isPlaying = ref(false);
const duration = ref(0);
const currentTime = ref('0:00');
const volume = ref(1);
const isMuted = ref(false);
const progressSlider = ref(0);
const progressPercent = ref(0);
const isAddHideActions = ref(false);
const isCursorMoving  = ref(false);
const timeout = ref();
const fullTimeVideo = ref('');
const wrapper = ref();
const isFullscreen = ref(false)


const togglePlay = () => {
  if(isPlaying.value) {
    videoPlayer.value?.pause();
    isAddHideActions.value = false;
  }
  else {
    videoPlayer.value?.play();
    setTimeout(() =>{
      if(isPlaying.value && !isCursorMoving.value) isAddHideActions.value = true;
    }, 3500)
  }
  isPlaying.value = !isPlaying.value;
}

const toggleFullscreen = ()=> {
  if(wrapper.value) {
    if(!document.fullscreenElement){
      if (wrapper.value.requestFullscreen) {
          wrapper.value.requestFullscreen();
        }
    } else {
      if (document.exitFullscreen) {
            document.exitFullscreen();
          }
    }
  }
  
}



const mute = () => {
  if(videoPlayer.value){
    if(videoPlayer.value.muted || videoPlayer.value.volume === 0){      
      videoPlayer.value.muted = false;
      isMuted.value = false;
      // volume.value = 1;
    } else {
      videoPlayer.value.muted = true;
      isMuted.value = true;
      // volume.value = 0;
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
let lastVolume = 0;
watch(isMuted, (val)=>{
  if(videoPlayer.value){
    videoPlayer.value.muted = val;
    if(val){
      videoPlayer.value.volume = 0;
      lastVolume = volume.value;
      volume.value = 0
    } else {
      videoPlayer.value.volume = volume.value;
      volume.value = lastVolume;
    }
  }
})


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
    const t = Math.round(((current / 60) - minutes) * 60);
    seconds = String((t < 10) ? '0' + t : t);
  }
  currentTime.value = `${minutes}:${seconds}`;
}

const getUpdate = ()=>{
  getCurrentTime();
  sliderUpdate();
  progressPercent.value = Math.round((videoPlayer.value?.currentTime! / duration.value) * 10000) / 100;
}

function handleInput(event: Event){
  const target = event.target as HTMLInputElement;  
  if(videoPlayer.value){
    videoPlayer.value.currentTime = Number(target.value)
  }
}

function handleMouseMove(event:MouseEvent){
  clearTimeout(timeout.value);
  isCursorMoving.value = true;
  if(isPlaying.value){
    isAddHideActions.value = false;
  }
  timeout.value = setTimeout(handleMouseStop, 3500);
}

function handleMouseStop() {
  if(isPlaying.value){
    isCursorMoving.value = false;
    isAddHideActions.value = true;
  }
}


function handleMouseLeave(event:Event){
  if(isPlaying.value){
    isAddHideActions.value = true;
  }
}

document.body.addEventListener('keydown', (event: KeyboardEvent) => {
    // console.log(event.key)
    if(videoPlayer.value){
          if(event.key === 'ArrowRight') {
            event.preventDefault();
            videoPlayer.value.currentTime += 5;
          }
          if(event.key === 'ArrowLeft') {
            event.preventDefault();
            videoPlayer.value.currentTime -= 5; 
          }
          if(event.key === ' '){
            event.preventDefault();
              if(isPlaying.value) {
                isPlaying.value = false;
                isAddHideActions.value = false;
                videoPlayer.value.pause();
              } else {
                isPlaying.value = true;
                videoPlayer.value.play();
                setTimeout(() =>{
                  if(isPlaying.value && !isCursorMoving.value) isAddHideActions.value = true;                  
                }, 3500)                
              }
          }
    }
  });


function formatTime(seconds: number) {
    const hours = Math.floor(seconds / 3600);
    const minutes = Math.floor((seconds % 3600) / 60);
    seconds = seconds % 60;

    if (hours > 0) {
        return hours + ":" + ("0" + minutes).slice(-2) + ":" + ("0" + seconds).slice(-2);
    } else {
        return minutes + ":" + ("0" + seconds).slice(-2);
    }
}


function handleFullscreenChange(){
        isFullscreen.value = !isFullscreen.value
        if(videoPlayer.value) videoPlayer.value.classList.toggle('fullvideo')

}

onMounted(() => {
  videoPlayer.value?.addEventListener('loadedmetadata', () => {
    duration.value = Math.round(videoPlayer.value?.duration!);
    fullTimeVideo.value = formatTime(duration.value);
  });

  wrapper.value.addEventListener('fullscreenchange', handleFullscreenChange);
 
});

// onUnmounted(() => {
//   wrapper.value.removeEventListener('fullscreenchange', handleFullscreenChange);
//     });

</script>

<style scoped lang="scss">
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
  .hide-cursor{
    cursor: none;
  }

  .video-player{
    height: 550px;
    display: block;
    &.fullvideo{
      height: 864px;
    }
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
    opacity: 1;
    transition: .2s ease;
  }

  .move-down{
    opacity: 0;
  }

  .left-btns {
    display: flex;
  }

  .image-play{
    height: 48px;
    width: 48px;
    background-image: url('@/assets/play-svg.svg');
    background-size: 28px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }
  .image-pause{
    height: 48px;
    width: 48px;
    background-image: url('@/assets/pause-svg.svg');
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
    background-image: url('@/assets/volume-up.svg');
    background-size: 22px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }
  .sound_half {
    height: 48px;
    width: 48px;
    background-image: url('@/assets/volume-down.svg');
    background-size: 22px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }
  .mute {
    height: 48px;
    width: 48px;
    background-image: url('@/assets/volume-off.svg');
    background-size: 22px;
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

  .no-fullscreen {
    height: 48px;
    width: 48px;
    background-image: url('@/assets/fullscreen.svg');
    background-size: 25px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }
  .fullscreen {
    height: 48px;
    width: 48px;
    background-image: url('@/assets/fullscreen-exit.svg');
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

  .progress {
    -webkit-appearance: none !important;
    appearance: none;
    cursor: pointer;
    position: absolute;
    left: 0;
    bottom: 55px;
    width: 100%;
    height:3px;
    opacity: 1;
    transition: .2s ease;
  }
  .progress-down{
    opacity: 0;
  }

  .progress-wrapper:hover .progress{
    height: 5px;
  }
  

  .progress::-webkit-slider-thumb {
    -webkit-appearance: none !important;
    background: red;
    height: 0px;
    width: 0px;
    border-radius: 50%;
    transition: opacity .2s ease;
  }

  .progress-wrapper:hover .progress::-webkit-slider-thumb{
    height: 12px;
    width: 12px;
  }

  button{
    border: none;
    outline: none;
    background-color: transparent;
  }
</style>
