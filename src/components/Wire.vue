<template>
  <div :class="`wire wire--${type}`">
    <div class="wire__port"></div>
    <div class="wire__line"></div>
    <div class="wire__label">ABC</div>
  </div>
</template>

<script>
export default {
  name: 'Wire',
  props: {
    type: {
      type: String,
      default: 'node'
    }
  }
}
</script>

<style lang="scss">
$port-size: 40px;
$port-radius: 16px;
$port-width: 40px;
$min-dimension: 100px;
$label-padding: 10px;
$stroke-width: 3px;
$stroke-color: #000;
$bg-color: #fff;

.wire {
  position: relative;
  
  &__port {
    position: absolute;
    border: $stroke-width solid $stroke-color;
    border-radius: 50%;
    width: $port-radius;
    height: $port-radius;
    box-sizing: border-box;
    background-color: $bg-color;
    z-index: 2;
  }
  
  &__line {
    position: absolute;
    background-color: $stroke-color;
  }
  
  &__label {
    position: absolute;
    font-family: "Lucida Console", Monaco, monospace;
    font-size: 12px;
    text-align: center;
    width: $port-width;
    padding: $label-padding 0;
  }
  
  &--left, &--right {
    height: $port-width;
    width: 100%;
    
    .wire__port {
      top: $port-width / 2 - $port-radius / 2;
    }
    
    .wire__label {
      top: $port-width / 2 - $port-width / 4;
      height: $port-width / 2;
      box-sizing: border-box;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    
    .wire__line {
      height: $stroke-width;
      width: $port-size;
      top: $port-width / 2 - $stroke-width / 2;
    }
  }
  
  &--left {
    .wire__label {
      left: $port-size;
    }
  }
  
  &--right {
    .wire__port {
      left: $port-size - $port-radius;
    }
    
    .wire__label {
      left: -$port-size;
    }
  }
  
  &--top, &--bottom {
    width: $port-width;
    height: 100%;
    
    .wire__port {
      left: $port-width / 2 - $port-radius / 2;
    }
    
    .wire__line {
      width: $stroke-width;
      height: $port-size;
      margin: auto;
      left: $port-width / 2 - $stroke-width / 2;
    }
  }
  
  &--top {
    .wire__label {
      top: $port-size;
    }
  }
  
  &--bottom {
    .wire__port {
      top: $port-size - $port-radius;
    }
    
    .wire__label {
      top: -$port-width / 2 - $port-radius;
    }
  }

  &--node {
    .wire__line,
    .wire__label {
      visibility: hidden;
    }
  }
}
</style>