<template>
   
    

        <svg width="20px" height="20px" viewBox="-1 0 8 8" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    
            <title></title>
            <desc>Created with Sketch.</desc>
            <defs>

        </defs>
            <g id="Page-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                <g id="Dribbble-Light-Preview" transform="translate(-67.000000, -3765.000000)" fill="#ffffff">
                    <g id="icons" transform="translate(56.000000, 160.000000)">
                        <path d="M11,3613 L14,3611 L14,3607 L11,3605 L11,3613 Z M14,3611 L17,3609 L17,3609 L14,3607 L14,3611 Z" id="pause">

        </path>
                    </g>
                </g>
            </g>
        </svg>
    


</template>

<script lang="ts" setup>
import {  watch } from 'vue';
import { gsap } from 'gsap';
const props = defineProps({
    isPlaying : Boolean,
})

watch(()=>props.isPlaying, (val)=>{
    const isPlaying = val;
    const pause = document.getElementById('pause');
    
    if(!isPlaying){
        gsap.to(pause,{
            duration: 0.3,
            attr: {d: 'M11,3613 L14,3611 L14,3607 L11,3605 L11,3613 Z M14,3611 L17,3609 L17,3609 L14,3607 L14,3611 Z' }
        })
    }else{
        gsap.to(pause, {
            duration:0.3,
            attr: {
                d: 'M11,3613 L13,3613 L13,3605 L11,3605 L11,3613 Z M15,3613 L17,3613 L17,3605 L15,3605 L15,3613 Z'
            }
        })
    }
    
})

</script>
<style lang="scss" scoped>

</style>

