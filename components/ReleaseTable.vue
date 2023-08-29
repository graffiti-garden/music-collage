<script setup>
import ReleaseTableRow from './ReleaseTableRow.vue'
const props = defineProps(['releaseGroups', 'releaseGroupIds'])
</script>

<template>
  <table>
    <thead>
      <tr>
        <th>
        </th>
        <th>
          Cover
        </th>
        <th>
          Title
        </th>
        <th>
          Albums
        </th>
        <th>
          Year
        </th>
      </tr>
    </thead>
    <tbody>
      <ReleaseTableRow 
        v-for="releaseGroup in releaseGroups"
        :key="releaseGroup.id"
        :release-group="releaseGroup"
        @selected="$emit('selected', releaseGroup.id)" />
      <ReleaseTableRow 
        v-for="mbid in releaseGroupIds"
        :key="mbid"
        :mbid="mbid"
        @selected="$emit('selected', mbid)" />
    </tbody>
  </table>
</template>

<style>
table, th, td {
  border: 1px solid;
}

table {
  border-collapse: collapse;
  display: block;
  overflow-y: scroll;
  overflow-x: hidden;
}

table thead, table tbody tr {
  display: table;
  width: 100%;
  table-layout: fixed;
}

thead {
  position: sticky;
  top: 0;
  box-shadow: 0 2px 2px -1px rgba(0, 0, 0, 0.4);
  background: white;
}
</style>