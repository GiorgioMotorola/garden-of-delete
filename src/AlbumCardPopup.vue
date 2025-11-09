<template>
  <div v-if="visible" class="album-popup" :style="style">
    <h3 class="title">{{ album.title }}</h3>
    <p class="artist">{{ album.artist }}</p>
    <p class="meta">{{ album.year }} • {{ album.genre }}</p>
    <ul class="tracklist">
      <li v-for="track in album.tracks" :key="track">{{ track }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from "vue";

const props = defineProps({
  album: Object,
  visible: Boolean,
  targetId: String
});

const style = ref({});

const updatePosition = () => {
  const target = document.getElementById(props.targetId);
  if (!target) return;

  const rect = target.getBoundingClientRect();
  style.value = {
    top: `${rect.top + window.scrollY}px`,
    left: `${rect.right + 10 + window.scrollX}px`,
    pointerEvents: "auto"
  };
};

watch(() => props.visible, (v) => {
  if (v) updatePosition();
});

onMounted(() => {
  window.addEventListener("resize", updatePosition);
  window.addEventListener("scroll", updatePosition);
});
onBeforeUnmount(() => {
  window.removeEventListener("resize", updatePosition);
  window.removeEventListener("scroll", updatePosition);
});
</script>

<style scoped>
.album-popup {
  position: absolute;
  background-color: white;
  border-radius: 16px;
  padding: 1rem;
  z-index: 100;
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
  from { opacity: 0; transform: translateY(-4px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>
