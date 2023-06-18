<template>
  <div class="root">
    <div  :class="['wrapper', {'hide-cursor': !isCursorMoving}]"
      ref="wrapper" 
      @mousemove="handleMouseMove" 
      @mouseleave="handleMouseLeave" 
      >
        <video ref="videoPlayer" class="video-player" @timeupdate="getUpdate" @click="togglePlay" >
          <!-- <source src="./assets/video/relaxation.mp4" type="video/mp4"> -->
        </video>

        <div :class="['opacity-box', {'hide': false}]" @click="togglePlay" >
          <ProgressBar 
        :is-add-hide-actions="isAddHideActions"
        :duration="duration"
        :value="progressSlider"
        :progress-percent="progressPercent"
        :loaded-percent="loadedPercent"
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
          :url="imgUrl"
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
import Hls from 'hls.js';
import axios from 'axios';



const videoPlayer = ref<HTMLVideoElement>();
const isPlaying = ref(false);
const duration = ref(0);
const currentTime = ref('0:00');
const volume = ref(1);
const isMuted = ref(false);
const progressSlider = ref(0);
const progressPercent = ref(0);
const loadedPercent = ref(0);
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
const imgUrl = ref('')
const images = ref<string[]>([])


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
  getThumbNails(labelTime)
}

function leaveProgressBar(){
  isMovingOnProgressBar.value = false;
}

function getThumbNails(time: number){
  const interval = Math.round(duration.value / images.value.length)
  const index = Math.floor(time / interval)
  imgUrl.value = images.value[index]
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


onMounted(async () => {
  videoPlayer.value?.addEventListener('loadedmetadata', () => {
    duration.value = Math.round(videoPlayer.value?.duration!);
    fullTimeVideo.value = formatTime(duration.value);
  });

  wrapper.value.addEventListener('fullscreenchange', handleFullscreenChange);
 
 const response = await axios.get('http://localhost:5000/')
 images.value = [...response.data]
 
 

  // const videoSrc = 'https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8';
  const videoSrc = 'http://localhost:5000/video/relaxation.m3u8';
   if(Hls.isSupported()){
    let dur = 0
    const hls = new Hls();
    hls.loadSource(videoSrc);
    hls.attachMedia(videoPlayer.value!);

    

    hls.on(Hls.Events.FRAG_CHANGED, (event:any, {frag})=>{
      const fragmentEnd = Math.round(frag.end);
      loadedPercent.value = Math.round((fragmentEnd / duration.value)*100)
    })

    // hls.on(Hls.Events.FRAG_LOADED, (event:any, data:any) => {
    //   dur += data.frag.duration
    //   const fragmentEnd = Math.round(data.frag.end)
    //   loadedPercent.value = Math.round((fragmentEnd / duration.value)*100)
    //   const fragment = data.frag;
    //   console.log('Загружен фрагмент:', fragment);
    //   console.log('minEndPTS: ', fragmentEnd);
    //   console.log('Загружено: ', dur, ' секунд');
    //   console.log('Показано: ', progressPercent.value, ' %');
    //   console.log('Загружено: ', loadedPercent.value, ' %');
    // });
   }
});


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
