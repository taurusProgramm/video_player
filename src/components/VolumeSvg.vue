<template>
    <svg width="25px" height="25px" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">

        <title/>

        <g id="Complete">

        <g id="volume-off">

        <g>

        <line fill="none" 
        id="line"
        stroke="#ffffff" 
        stroke-linecap="round" 
        stroke-linejoin="round" 
        stroke-width="2" 
        ref="line"
        :x1="x1" 
        :x2="x2" 
        :y1="y1" 
        :y2="y2"
        :opacity="props.volume! === 0 ? 1 : 0"
        :class="[{'mute-animation':props.volume! === 0}]"
        />
        
        

        <polygon fill="none" 
        points="2.9 9 6.9 9 11.9 3 11.9 21 6.9 16 1.9 16 1.9 9 2.9 9" 
        stroke="#ffffff" 
        stroke-linecap="round" 
        stroke-linejoin="round" stroke-width="2"/>

        <path d="M15.5,19.5a7.3,7.3,0,0,0,7-7.5,7.3,7.3,0,0,0-7-7.5" id="outside" 
        fill="none" 
        stroke="#ffffff" 
        stroke-linecap="round" 
        stroke-linejoin="round"
        stroke-width="2"
        :class="[{'less': props.volume! <= 0.5}, {'more': props.volume! > 0.5 || props.volume! === 0}]"
        >
        </path>

        <path d="M15.5,15a3,3,0,0,0,0-6" id="inside"
        fill="none" 
        stroke="#ffffff" 
        stroke-linecap="round" 
        stroke-linejoin="round" 
        stroke-width="2"/>

        </g>

        </g>

        </g>

</svg>
</template>

<script lang="ts" setup>
import {  ref } from 'vue';

  const props = defineProps({
      volume:Number,
  });
  
  const x1 = ref(3);
  const x2 = ref(21);
  const y1 = ref(3);
  const y2 = ref(21);


    

</script>

<style lang="scss" scoped>

@keyframes fade-out {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}

@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.less {
  animation: fade-out .5s forwards;
}
.more {
  animation: fade-in .5s forwards;
}


@keyframes draw {
    from {
    stroke-dashoffset: 100;
  }
  to {
    stroke-dashoffset: 0;
  }
}

.mute-animation {
  stroke-dasharray: 100;
  stroke-dashoffset: 100;
  animation: draw 1.5s linear forwards;
}



</style>