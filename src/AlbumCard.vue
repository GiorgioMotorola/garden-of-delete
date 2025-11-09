<template>
  <div
    :id="`album-${album.id}`"
    class="album-card"
    @mouseenter="onGridHover"
    @mouseleave="onGridLeave"
    @click.stop="onClick"
  >
    <img :src="album.cover" :alt="album.title" class="album-image" />

    <div v-if="visible" class="album-popup">
      <h3 class="title">{{ album.title }}</h3>
      <p class="artist">{{ album.artist }}</p>
      <p class="meta">{{ album.year }} • {{ album.genre }}</p>
      <ul class="tracklist">
        <li v-for="track in album.tracks" :key="track">{{ track }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  album: Object,
  openId: Number,
  hoveredId: Number,
  setOpenId: Function,
  setHoveredId: Function
});

const onGridHover = () => {
  props.setHoveredId(props.album.id);
};

const onGridLeave = () => {
  if (props.hoveredId === props.album.id) props.setHoveredId(null);
};

const onClick = () => {
  props.setOpenId(props.openId === props.album.id ? null : props.album.id);
};

const visible = computed(() => {
  if (props.hoveredId !== null) return props.hoveredId === props.album.id;
  return props.openId === props.album.id;
});
</script>

<style scoped>
.album-card {
  position: relative;
  cursor: pointer;
  aspect-ratio: 1 / 1;
}

.album-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 0px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.25s ease, box-shadow 0.25s ease;
}

.album-card:hover .album-image {
  transform: scale(1.03);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
}

.album-popup {
  position: absolute;
  bottom: -200%;
  left: 130%;
  transform: translateX(-50%);
  background-color: white;
  border-radius: 16px;
  padding: 1rem;
  z-index: 10;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.3);
  max-height: 75vh;
  overflow-y: auto;
  border: 1px solid #ddd;
  width: 260px;
  animation: fadeIn 0.2s ease-out;
}

.title {
  font-weight: 700;
  font-size: 1.1rem;
  margin: 0;
}

.artist {
  font-style: italic;
  font-size: 0.9rem;
  margin-top: 0.25rem;
}

.meta {
  font-size: 0.8rem;
  color: #777;
  margin: 0.25rem 0 0.75rem 0;
}

.tracklist {
  list-style-type: disc;
  padding-left: 1rem;
  font-size: 0.9rem;
  line-height: 1.4;
  margin: 0;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translate(-50%, -4px);
  }
  to {
    opacity: 1;
    transform: translate(-50%, 0);
  }
}
</style>
