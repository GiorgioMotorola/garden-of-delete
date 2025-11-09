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
      
      <div 
        class="tracklist-container" 
        :class="{ 'expanded': isExpanded }"
      >
        <div class="tracklist">
          <div v-for="(track, index) in displayedTracks" :key="index">
            {{ track }}
          </div>
        </div>
        <div v-if="shouldShowToggle && !isExpanded" class="tracklist-fade"></div>
      </div>

      <button 
        v-if="shouldShowToggle" 
        @click.stop="isExpanded = !isExpanded"
        class="toggle-button"
      >
        {{ isExpanded ? 'Show Less...' : 'Show More...' }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";

const props = defineProps({
  album: Object,
  openId: Number,
  hoveredId: Number,
  setOpenId: Function,
  setHoveredId: Function
});

const MAX_VISIBLE_TRACKS = 7;
const isExpanded = ref(false);

const onGridHover = () => {
  props.setHoveredId(props.album.id);
};

const onGridLeave = () => {
  if (props.hoveredId === props.album.id) props.setHoveredId(null);
};

const onClick = () => {
  const newOpenId = props.openId === props.album.id ? null : props.album.id;
  props.setOpenId(newOpenId);

  if (newOpenId !== null) {
      isExpanded.value = false;
  }
};

const visible = computed(() => {
  if (props.hoveredId !== null) return props.hoveredId === props.album.id;
  return props.openId === props.album.id;
});

const displayedTracks = computed(() => {
    if (isExpanded.value) {
        return props.album.tracks;
    }
    return props.album.tracks.slice(0, MAX_VISIBLE_TRACKS);
});

const shouldShowToggle = computed(() => {
    return props.album.tracks.length > MAX_VISIBLE_TRACKS;
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
  top: 50%;
  bottom: -150%;
  left: 140%;
  transform: translateX(-50%);
  background-color: rgb(238, 237, 225);
  border-radius: 1px;
  padding: 1rem;
  z-index: 10;
  max-height: 75vh;
  overflow-y: auto;
  border: 3.5px solid #E1341E;
  width: 350px;
  animation: fadeIn 0.2s ease-out;
  display: flex; 
  flex-direction: column; 
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

.tracklist-container {
  max-height: 140px;
  overflow: hidden;
  position: relative;
  transition: max-height 0.3s ease-in-out;
}

.tracklist-container.expanded {
  max-height: 1000px; 
  overflow: auto;
}

.tracklist-fade {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 40px;
  background: linear-gradient(to top, rgb(238, 237, 225), transparent);
  pointer-events: none; 
}

.tracklist {
  font-size: 12px;
  margin: 0;
}

.toggle-button {
  margin-top: 0.5rem;
  padding: 0.25rem 0.5rem;
  background: none;
  border: none;
  color: #E1341E;
  font-weight: 600;
  cursor: pointer;
  align-self: flex-start; 
  font-size: 12px;
}

.toggle-button:hover {
    text-decoration: underline;
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