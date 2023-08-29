<script setup>
import { ref, watch, toRefs } from 'vue'

const props = defineProps(['mbid'])
const { mbid } = toRefs(props)
const loaded = ref(false)
const error = ref(false)

watch(mbid, ()=> {
  loaded.value = false
  error.value = false
})
</script>

<template>
  <img
    v-if="mbid"
    v-show="loaded"
    :src="`https://coverartarchive.org/release-group/${mbid}/front-250`"
    @load="()=> loaded=true"
    @error="()=> error=true">
  <img v-if="!loaded&&!error" src="../media/spinner.gif">
  <img v-if="error&&!loaded" src="../media/404.jpg">
</template>

<style>
img {
  max-width: 100%;
  max-height: 100%;
}
</style>