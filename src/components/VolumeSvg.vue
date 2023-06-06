<template>
    <svg width="25px" height="25px" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">

        <title/>

        <g id="Complete">

        <g id="volume-off">

        <g>

        <line fill="none" 
        id="line"
        stroke="transparent" 
        stroke-linecap="round" 
        stroke-linejoin="round" 
        stroke-width="2" 
        ref="line"
        x1="3" 
        x2="21" 
        y1="3" 
        y2="21"
       
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
import {  ref, watch } from 'vue';
import { gsap } from 'gsap';

  const props = defineProps({
      volume:Number,
      isMuted: Boolean,
  });
  
  

  watch(()=>props.isMuted, (value) => {
    
    const line = document.getElementById('line');
    if(line instanceof SVGGeometryElement){
      const totalLength = line.getTotalLength();
      if(!value){
          line.style.strokeDasharray = String(totalLength);
          line.style.strokeDashoffset = String(0);
          const tl = gsap.timeline();
          tl.to(line, { strokeDashoffset: totalLength, duration: 0.3 })
          .to(line, { stroke: 'transparent', duration: 0.001 });
      }else{
          line.style.strokeDasharray = String(totalLength);
          line.style.strokeDashoffset = String(totalLength);
          line.style.stroke = '#ffffff';
          gsap.to(line, {
              duration: 0.3,
              strokeDashoffset: 0
          })
      }
      
    }
  })


    

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