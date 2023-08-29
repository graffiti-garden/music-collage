<script setup>
import { ref } from 'vue'
import ReleaseImg from './ReleaseImg.vue';

const props = defineProps(['mbid', 'releaseGroup'])

const releaseGroup = ref(props.releaseGroup??{})

if (!Object.keys(releaseGroup.value).length) {
  fetch(`https://musicbrainz.org/ws/2/release-group/${props.mbid}?inc=artist-credits&fmt=json`)
  .then(async response=> {
    releaseGroup.value = await response.json()
  })
}
</script>

<template>
  <tr>
    <td>
      <button @click="$emit('selected', releaseGroup.id)">
        Select
      </button>
    </td>
    <td>
      <ReleaseImg :mbid="releaseGroup.id" />
    </td>
    <td>
      <a target="_blank" :href="`https://musicbrainz.org/release-group/${releaseGroup.id}`">
        {{ releaseGroup.title }}
      </a>
    </td>
    <td>
      <ul class="artist-list">
        <li v-for="artist in releaseGroup['artist-credit']">
          <a target="_blank" 
            :href="`https://musicbrainz.org/artist/${artist.artist.id}`"
            :key="artist.artist.id" >
            {{ artist.name }}
          </a>
        </li>
      </ul>
    </td>
    <td>
      {{ new Date(releaseGroup["first-release-date"]).getFullYear() }}
    </td>
  </tr>
</template>

<style scoped>
tr td {
  height: 5rem;
  text-align: center;
  padding: 0.5rem;
}

tr:nth-child(even) {
  background: lightgrey;
}

tr:nth-child(odd) {
  background: white;
}

.artist-list {
  display: inline;
  list-style: none;
  padding: 0;
}

.artist-list li {
  display: inline;
}

.artist-list li:after {
  content: ", ";
}

.artist-list li:last-child:after {
  content: "";
}
</style>