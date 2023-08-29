<script setup>
import { ref } from 'vue'
import ReleaseTable from './ReleaseTable.vue'

const titleQuery = ref('')
const releaseGroups = ref({})

async function fetchReleases() {
  const endpoint = new URL('https://musicbrainz.org/ws/2/release-group')
  endpoint.searchParams.set('fmt', 'json')
  endpoint.searchParams.set('query', `${titleQuery.value}`)

  const response = await fetch(endpoint)
  releaseGroups.value = (await response.json())["release-groups"]
}
</script>

<template>
  <div class="selector">
    <form @submit.prevent="fetchReleases" placeholder="I'm looking for...">
      <input v-model="titleQuery" placeholder="Search for an album, EP, single, etc.">
      <input type="submit" value="Search">
    </form>

    <ReleaseTable :release-groups="releaseGroups" @selected="s=> $emit('selected', s)"/>
  </div>
</template>

<style>
.selector {
  box-sizing: border-box;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  height: 100%;
  max-height: 100%;
}

form {
  display: flex;
  flex-direction: column;
}
</style>