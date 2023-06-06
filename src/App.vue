<template>
  <div class="root">
    <div  :class="['wrapper', {'hide-cursor': !isCursorMoving}]"
      ref="wrapper" 
      @mousemove="handleMouseMove" 
      @mouseleave="handleMouseLeave" 
      >
        <video ref="videoPlayer" class="video-player" @timeupdate="getUpdate" @click="togglePlay" >
          <source src="./assets/video/relaxation.mp4" type="video/mp4">
        </video>

        <div :class="['opacity-box', {'hide': false}]" @click="togglePlay" >
          <ProgressBar 
        :is-add-hide-actions="isAddHideActions"
        :duration="duration"
        :value="progressSlider"
        :progress-percent="progressPercent"
        @handle-input="handleInput"
        @leave-progress-bar="leaveProgressBar"
        @move-on-progress-bar="moveOnProgressBar"
        />

        <ActionsBar 
        :data="{isAddHideActions, isFullscreen, isMuted, isPlaying}"
        @toggle-play="togglePlay"
        @toggle-full-screen="toggleFullscreen"
        @toggle-mute="toggleMute"
        v-model="volume"> 
        {{ currentTime }} / {{ fullTimeVideo }}
        </ActionsBar>

        <TimeLabel 
          :x="x" 
          :is-moving-on-progress-bar="isMovingOnProgressBar"
          :offset-width="offsetWidth"
          >
            {{ timestamp }}
        </TimeLabel>
        </div>
    </div>
    
  </div>
</template>

<script lang="ts" setup>
import { onMounted, ref, watch } from 'vue';
import ActionsBar from './components/ActionsBar.vue';
import TimeLabel from './components/TimeLabel.vue';
import ProgressBar from './components/ProgressBar.vue';




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
const isFullscreen = ref(false);
const timestamp = ref('');
const x = ref(0);
const isMovingOnProgressBar = ref(false);
const offsetWidth = ref(0);


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

function moveOnProgressBar(value:any){
  isMovingOnProgressBar.value = true;
  const target = value.target as HTMLInputElement;
  // console.log(`Ширина элемента: ${target.offsetWidth}`);
  // console.log(`Текущая точка: ${event.offsetX}`);
  x.value = value.offsetX;
  offsetWidth.value = target.offsetWidth;
  const labelTime = Math.floor((value.offsetX * duration.value) / target.offsetWidth);
  timestamp.value = getCurrentTime(labelTime);
  
}

function leaveProgressBar(){
  isMovingOnProgressBar.value = false;
}



let lastVolume = 0;
const toggleMute = () => {
  if(videoPlayer.value){
    if(videoPlayer.value.muted){      
      videoPlayer.value.muted = false;
      isMuted.value = false;
      volume.value = lastVolume;
    } else {
      videoPlayer.value.muted = true;
      isMuted.value = true;
      lastVolume = volume.value;
      volume.value = 0;
    }
  }
}

watch (volume, (v) => {  
  if(videoPlayer.value){
    videoPlayer.value.volume = v;
    videoPlayer.value.muted = !Number(v);
    if ( v == 0) isMuted.value = true;
    else isMuted.value = false;
  }
})


function sliderUpdate(){
  progressSlider.value = videoPlayer.value?.currentTime!;
}

function getCurrentTime(value: number){
  let current = value;
  let hours = Math.floor(current / 3600);
  let minutes = Math.floor((current % 3600) / 60);
  let seconds = current % 60;

  if (hours > 0) {
    return`${hours}:${padNumber(minutes)}:${padNumber(seconds)}`;
  } else {
    return `${minutes}:${padNumber(seconds)}`;
  }
}

function padNumber(num: number) {
  return String(num).padStart(2, '0');
}

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


const getUpdate = ()=>{
  currentTime.value = getCurrentTime(Math.round(videoPlayer.value?.currentTime!));
  sliderUpdate();
  progressPercent.value = Math.round((videoPlayer.value?.currentTime! / duration.value) * 10000) / 100;
}

function handleInput(value:string){
  if(videoPlayer.value){
    videoPlayer.value.currentTime = Number(value)
  }
}

function handleMouseMove(){
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
function handleFullscreenChange(){
        isFullscreen.value = !isFullscreen.value
        if(videoPlayer.value) videoPlayer.value.classList.toggle('fullvideo')

}

// function captureFrameAtTime(time: number) {
//   return new Promise((resolve, reject) => {
//     if (!videoPlayer2.value) {
//       reject(new Error('Video player element is not defined'));
//       return;
//     }
    
//     const canvas = document.createElement('canvas');
//     const context = canvas.getContext('2d');
//     if (!context) {
//       reject(new Error('Canvas 2D context is not supported'));
//       return;
//     }
//     // Перемотайте видео к указанному времени
//     videoPlayer2.value.currentTime = time;
//     // Дождитесь загрузки кадра
//     videoPlayer2.value.onloadeddata = () => {
//       console.log('5');
//       // Установите размеры холста такими же, как у видео
//       canvas.width = videoPlayer.value!.videoWidth;
//       canvas.height = videoPlayer.value!.videoHeight;

//       // Отрисуйте текущий кадр видео на холст
//       context.drawImage(videoPlayer2.value!, 0, 0, canvas.width, canvas.height);

//       // Получите данные изображения в формате base64
//       const imageDataURL = canvas.toDataURL('image/png');

//       resolve(imageDataURL);
//     };
//   });
// }



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

<style scoped lang="scss" >
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
    .opacity-box{
      position: absolute;
      bottom: 0;
      right: 0;
      left: 0;
      height: 160px;
      background: transparent;
    }
    .hide{
      background: linear-gradient(0deg, rgba(65, 65, 65, .6), rgba(202, 202, 202, .4), rgba(246, 246, 246, .3));
      }
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

</style>
