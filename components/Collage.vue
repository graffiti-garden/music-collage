<script setup>
import ReleaseSelector from './ReleaseSelector.vue';
import ReleaseImg from './ReleaseImg.vue'
import ReleaseTable from './ReleaseTable.vue'
import { ref, computed, inject } from 'vue'

// Convert the collage ID to a URI
const props = defineProps(['id'])
const collageID = computed(()=> `music-collage:${props.id}`)

// Get all the posts associated with that URI
const gf = inject('graffiti')
const { posts: collagePosts } = gf.usePosts(collageID)

// Filter the collage posts for
// ones that are relevant to our application
const musicBrainzPosts = computed(()=>
  collagePosts.value.filter(p=>
    // a MusicBainz ID (MBID)
    // https://musicbrainz.org/doc/MusicBrainz_Identifier
    typeof p.mbid == 'string'  &&
    /^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/i.test(p.mbid) &&
 
    // a timestamp,
    // https://www.w3.org/TR/activitystreams-vocabulary/#dfn-updated
    typeof p.updated == 'string' &&
 
    // a type of either Add or Delete
    // https://www.w3.org/TR/activitystreams-vocabulary/#dfn-add
    // https://www.w3.org/TR/activitystreams-vocabulary/#dfn-delete
    ( p.type == "Add"    || 
      p.type == "Delete" )
  )
)

// Filter to get the most recent post
// associated with each MBID, and then filter
// out posts that have type "Delete"
const musicBrainzIds = computed(()=> {
  // Group the posts according to their MBID
  const grouped = musicBrainzPosts.value.groupBy('mbid')

  return Object.values(grouped)
    // Choose the most recent post in each group
    .map(group=> group.sortBy("-updated")[0])
    // and filter out delete actions
    .filter(p=> p.type != "Delete") //
    // And map to just the music brain ids
    .map(p=> p.mbid)
})

// A simple interface to add and delete releases
const addingRelease = ref(false)
async function changeRelease(mbid, type) {
  musicBrainzPosts.value.post({
    type, mbid,
    updated: (new Date()).toISOString()
  })
  addingRelease.value = false
}
const selectedRelease = ref(null)
</script>

<template>
  <form>
    <button @click.prevent="addingRelease=true">
      Add a release
    </button>
    <button
      :disabled="!selectedRelease"
      @click.prevent="changeRelease(selectedRelease, 'Delete');selectedRelease=null">
      Delete selected release
    </button>
  </form>


  <dialog :open="addingRelease" v-click-away="()=> addingRelease=false">
    <form></form>
    <ReleaseSelector @selected="id=> changeRelease(id, 'Add')" />
  </dialog>

  <ol class="collage">
    <li v-for="mbid in musicBrainzIds" :key="mbid">
      <ReleaseImg :mbid="mbid" />
    </li>
  </ol>

  <ReleaseTable :release-group-ids="musicBrainzIds" @selected="id=>selectedRelease=id"/>
</template>

<style scoped>
.collage {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.collage li {
  width: 10rem;
  height: 10rem;
  display: flex;
  align-items: center;
  justify-content: center;
  background: black;
}

.collage li img {
  max-width: 100%;
  max-height: 100%;
}

dialog {
  max-width: 45rem;
  width: 90dvw;
  min-height: 90dvh;
  height: 90dvh;
  overflow: hidden;
  padding: 0;
  z-index: 1;
  box-shadow: 0 0 4rem black;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  position: fixed;
}

</style>