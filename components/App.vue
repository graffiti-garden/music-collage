<script setup>
import Collage from './Collage.vue'
</script>

<template>

  <form v-if="!$gf.me">
    <button @click.prevent="$gf.selectActor()">
      Log In To Continue
    </button>
  </form>
  <Collage v-else id="123456" />
</template>