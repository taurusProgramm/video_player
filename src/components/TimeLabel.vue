<template>
    <div class="label-wrap" 
    :style="{
      left: `${props.x! < 90 ? 5 : (props.offsetWidth! - props.x!) < 100 ? props.offsetWidth! -95 : (props.x!+5) }px`, 
      opacity: `${props.isMovingOnProgressBar ? 1 : 0}`,
      transform: `translateX(${props.x! < 90 ? 0: -50}%)`,
      }">
        <div class="box" 
        :style="{
          'background-image': `url(${ props.url })`,
          border: `${props.url ? '2px solid white' : 'none'}`
        }"
        ></div>
        <div class="label-time"  > <slot> </slot></div>
    </div>
</template>

<script setup lang="ts">
const props = defineProps({
    isMovingOnProgressBar: Boolean,
    x: Number,
    offsetWidth: Number,
    url: String
});

</script>

<style lang="scss" scoped>
.label-wrap{
  position: absolute;
  opacity: 1;
  left: 0;
  bottom: 65px;
  height: 150px;
  width: 180px;
  // background-color: grey;
  background: transparent;
  display: flex;
  justify-content: center;
  align-items: center;
  .box{
  position: absolute;
  opacity: 1;
  bottom: 32px;
  left: 50%;
  transform: translateX(-50%);
  height: calc(100% - 32px);
  width: 100%;
  // background-color: rgb(187, 172, 172);
    // background-image: url('@/assets/goblin.jpg');
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center;
}
.label-time{
      opacity: 1;
      position: absolute;
      left:50%;
      transform: translateX(-50%);
      bottom: 3px;
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 14px;
      transition: .1s ease;
    }
}

</style>