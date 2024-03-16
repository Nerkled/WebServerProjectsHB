<template>
  <img :src="activitiesPhotos" :onerror="onError">
</template>

<script setup lang="ts">
import { computed } from 'vue';

// onError event handler
// If the image fails to load, it sets the image source to a default image
const onError = (event: Event) => {
  const target = event.target as HTMLImageElement;
  target.src = new URL(`/src/assets/activities_photos/Default.webp`, import.meta.url).href;
}

// Computed property that returns the URL of the activity photo
// The URL is constructed using the userId and activityIndex props
const activitiesPhotos = computed<string>(() => {
  return new URL(`/src/assets/activities_photos/${props.userId}_${props.activityIndex}.webp`, import.meta.url).href;
})

// Define props for the component
// userId and activityIndex are required and must be numbers
const props = defineProps({
  userId: {
    type: Number,
    required: true,
  },
  activityIndex: {
    type: Number,
    required: true,
  }
});
</script>

<style scoped>

</style>