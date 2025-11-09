<template>
  <div class="page" @click="handleClickOutside">
    <div class="header">
      <!-- <button @click="showRandom">Random Album</button> -->
    </div>

    <div class="main-content">
      <div class="album-grid">
        <AlbumCard
          v-for="album in albums"
          :key="album.id"
          :album="album"
          :openId="openId"
          :hoveredId="hoveredId"
          :setOpenId="setOpenId"
          :setHoveredId="setHoveredId"
        />
      </div>

      <div class="album-list">
        <ul>
          <li
            v-for="album in albums"
            :key="album.id"
            :class="{ active: hoveredId === album.id || openId === album.id }"
            @mouseenter="() => setHoveredId(album.id)"
            @mouseleave="() => setHoveredId(null)"
            @click.stop="() => setOpenId(openId === album.id ? null : album.id)"
          >
            {{ album.title }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, nextTick } from "vue";
import AlbumCard from "./AlbumCard.vue";
import { albums as rawAlbums } from "./data/albums";

const openId = ref(null);
const hoveredId = ref(null);

const setOpenId = (id) => (openId.value = id);
const setHoveredId = (id) => (hoveredId.value = id);

const albums = computed(() =>
  [...rawAlbums].sort((a, b) => a.artist.localeCompare(b.artist))
);

const showRandom = async () => {
  const random = albums.value[Math.floor(Math.random() * albums.value.length)];
  setHoveredId(null);
  setOpenId(random.id);
  await nextTick();
  document
    .getElementById(`album-${random.id}`)
    ?.scrollIntoView({ behavior: "smooth", block: "center" });
};

const handleClickOutside = (e) => {
  const grid = document.querySelector(".album-grid");
  const list = document.querySelector(".album-list");
  if (!grid.contains(e.target) && !list.contains(e.target)) {
    setOpenId(null);
    setHoveredId(null);
  }
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  background-color: #111111;
}
* {
  font-family: "IBM Plex Mono", monospace;
}
.page {
  min-height: 100vh;
  padding: 1rem;
}

.header {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 0rem;
}

.header button {
  padding: 0.6rem 1.2rem;
  background-color: #0055cc;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: background-color 0.2s ease-in-out;
}

.header button:hover {
  background-color: #003f99;
}

.main-content {
  display: flex;
  gap: 2rem;
  align-items: flex-start;
}

.album-grid {
  display: grid;
  gap: 0.5rem;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  flex: 1;
}

.album-list {
  width: 320px;
  margin-left: 16rem;
  font-size: 12px;
  font-weight: 300;
  color: #e0e0e0;
  text-align: start;
}

.album-list ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.album-list li {
  padding: 0rem 0rem;
  cursor: pointer;
  border-radius: 0px;
  transition: background-color 0.2s ease;
}

.album-list li:hover,
.album-list li.active {
  background-color: #313131;
  color: #1ECBE1;
  font-weight: 700;
}
</style>
