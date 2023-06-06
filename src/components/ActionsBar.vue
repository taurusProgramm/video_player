<template>
      
      <div class="actions" :class="{'move-down': props.data.isAddHideActions}" @click.stop="">
        <div class="left-btns">
            <div class="play-pause" @click="togglePlay">
              <PlayPauseSvg :is-playing="props.data.isPlaying"/>
            </div>
          <div class="laud-box">
            <div class="sound-wrapper" @click="toggleMute">
              <VolumeSvg :volume="props.modelValue" :is-muted="props.data.isMuted"/>
            </div>
            <input type="range" class="input-range"  min="0" max="1" step="0.1" :value="props.modelValue" @input="changeInput">
          </div>
          <div class="time"> <slot></slot> </div>
        </div>
        <div class="right-btns">
          <div :class="props.data.isFullscreen ? 'fullscreen': 'no-fullscreen'" @click="toggleFullscreen"></div>
          
        </div>
      </div>
    
</template>

<script lang="ts" setup>
import VolumeSvg from './VolumeSvg.vue';
import PlayPauseSvg from './PlayPauseSvg.vue';

const props = defineProps({
    data: {
        type:Object,
        required: true,
    },
    modelValue: Number,
});
const emits = defineEmits(['togglePlay','toggleFullScreen', 'toggleMute', 'update:modelValue' ]);
function togglePlay(){
    emits('togglePlay');
}

function toggleFullscreen(){
    emits('toggleFullScreen');
}

function toggleMute(){
    emits('toggleMute');
}
function changeInput(event:any){
    emits('update:modelValue', Number(event.target.value))    
}
</script>

<style scoped lang="scss">

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

  .play-pause{
    height: 48px;
    width: 48px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
  }

  .image-play{
    height: 48px;
    width: 48px;
    background-image: url('@/assets/play.svg');
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
    background-image: url('@/assets/pause.svg');
    background-size: 20px;
    background-repeat: no-repeat;
    filter: invert(1);
    background-position: center;
    background-color: transparent;
    cursor: pointer;
  }
  .sound-wrapper{
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 5px;
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

  .time {
    color: white;
    height: 48px;
    width: 120px;
    display: flex;
    justify-content: start;
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

  

</style>