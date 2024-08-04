<script setup>
defineProps({ hidden: Boolean });
</script>

<template>
  <article class="message" :class="{ 'message--hidden': hidden }">
    <div class="message-header">
      <slot name="header"></slot>
      <button class="delete"></button>
    </div>

    <div class="message-body">
      <slot></slot>
    </div>
  </article>
</template>

<style scoped lang="scss">
.message {
  transform-origin: top center;
  transition-property: opacity, transform;
  transition-duration: .3s;

  &--hidden {
    transform: scaleY(0);
    opacity: 0;
    pointer-events: none;
  }
}
</style>