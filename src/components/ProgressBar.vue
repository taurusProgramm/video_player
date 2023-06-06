<template>
    <div class="progress-wrapper" @click.stop >
        <input type="range"
        @input="handleInput"
        @mouseleave="leaveProgressBar"
        @mousemove="moveOnProgressBar"
        :class="['progress', {'progress-down': props.isAddHideActions}]"
        min="0" 
        :max="props.duration" 
        step="1" 
        :value="props.value"
        :style="{
          background: `linear-gradient(to right, #cc181e 0%, #cc181e ${props.progressPercent}%,  #444 ${props.progressPercent}%, #444 100%)`
        }"
        >
      </div>
</template>
<script setup lang="ts">
 const props = defineProps({
    duration: {type: Number, required: true},
    isAddHideActions: Boolean,
    progressPercent: {type: Number, required: true},
    value:{type: Number, required: true},
 });
 const emits = defineEmits(['moveOnProgressBar', 'leaveProgressBar', 'handleInput',  ]);
 function moveOnProgressBar(event:Event){
    emits('moveOnProgressBar', event);
 }
 function leaveProgressBar(){
    emits('leaveProgressBar');
 }
 function handleInput(event: Event){
    const target = event.target as HTMLInputElement;
    emits('handleInput', target.value);
 }
 
</script>

<style lang="scss" scoped>
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
</style>