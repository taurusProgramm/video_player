<template>
   
    
   
        <svg width="20px" height="20px" viewBox="0 0 8 8" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    
        <title></title>
        <desc>Created with Sketch.</desc>
        <defs>

        </defs>
            <g id="Page-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                <g id="Dribbble-Light-Preview" transform="translate(-67.000000, -3765.000000)" fill="#ffffff">
                    <g id="icons" transform="translate(56.000000, 160.000000)">
                        <path 
                        :d=" `M11,3613 
                        L${props.isPlaying? 13  : 14},${props.isPlaying ? 3613: 3611} 
                        L${props.isPlaying? 13  : 14},${props.isPlaying ? 3605: 3607} 
                        L11,3605 
                        L11,3613 Z 
                        M${props.isPlaying ? 15: 14},${props.isPlaying ? 3613 : 3611} 
                        L17,${props.isPlaying ? 3613 : 3609} 
                        L17,${props.isPlaying ? 3605 : 3609} 
                        L${props.isPlaying ? 15: 14},${props.isPlaying ? 3605 : 3607} 
                        L${props.isPlaying ? 15: 14},${props.isPlaying ? 3613 : 3611} Z` " 
                        id="pause"/>
                    </g>
                </g>
            </g>
        </svg>
    


</template>

<script lang="ts" setup>
import { ref } from 'vue';
const props = defineProps({
    isPlaying : Boolean,
})


</script>
<style lang="scss" scoped>

</style>

<!-- <path d="M11,3613 L13,3613 L13,3605 L11,3605 L11,3613 Z M15,3613 L17,3613 L17,3605 L15,3605 L15,3613 Z" id="pause"/> -->