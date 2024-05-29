<template>
  <main class="main">
    <div class="filter">
      <input
        v-model="tempFilterName"
        placeholder="Поиск по имени"
        class="filter__search-bar"
      />
      <select v-model="tempFilterStatus" class="filter__select">
        <option value="">All</option>
        <option value="alive">Alive</option>
        <option value="dead">Dead</option>
        <option value="unknown">Unknown</option>
      </select>
      <button @click="applyFilters" class="filter__btn">Применить</button>
    </div>
    <div class="container">
      <div v-for="character in characters" :key="character.id" class="card">
        <div class="card__img">
          <img :src="character.image" :alt="character.name" />
        </div>
        <div class="card__info">
          <div class="card__info-header">
            <h2 class="card__info-title">{{ character.name }}</h2>
            <p class="card__info-subtitle">
              {{ character.status }} - {{ character.species }}
            </p>
          </div>

          <div class="card__info-body">
            <span>Последнее известное местоположение:</span>
            <p class="card__info-info">
              {{ character.location?.name }}
            </p>
          </div>

          <div class="card__info-footer">
            <span>Впервые появился в:</span>
            <p class="card__info-info">
              {{ character.origin?.name }}
            </p>
          </div>
        </div>
      </div>
    </div>
    <div class="pagination">
      <button @click="prevPage" :disabled="!info.prev" class="pagination__btn">
        Предыдущая
      </button>
      <button @click="nextPage" :disabled="!info.next" class="pagination__btn">
        Следующая
      </button>
    </div>
  </main>
</template>

<script setup>
import { ref, onMounted } from "vue";

const characters = ref([]);
const info = ref({});
const currentPage = ref(1);
const filterName = ref("");
const filterStatus = ref("");
const tempFilterName = ref("");
const tempFilterStatus = ref("");

const fetchCharacterData = async () => {
  try {
    let url = `https://rickandmortyapi.com/api/character?page=${currentPage.value}`;
    if (filterName.value) {
      url += `&name=${filterName.value}`;
    }
    if (filterStatus.value) {
      url += `&status=${filterStatus.value}`;
    }
    const response = await fetch(url);
    const data = await response.json();
    characters.value = data.results;
    info.value = data.info;
  } catch (error) {
    console.error("Ошибка загрузки данных:", error);
  }
};

const nextPage = () => {
  if (info.value.next) {
    currentPage.value++;
    fetchCharacterData();
  }
};

const prevPage = () => {
  if (info.value.prev) {
    currentPage.value--;
    fetchCharacterData();
  }
};

const applyFilters = () => {
  filterName.value = tempFilterName.value;
  filterStatus.value = tempFilterStatus.value;
  fetchCharacterData();
};

onMounted(fetchCharacterData);
</script>

<style scoped>
.main {
  display: flex;
  flex-direction: column;
  background: rgb(39, 43, 51);
  min-height: 100vh;
  align-items: center;
}

.filter {
  display: flex;
  gap: 10px;
  margin: 20px;
}

.filter__search-bar {
  width: 200px;
  border-radius: 10px;
  padding: 10px 15px;
  border: none;
}

.filter__select {
  cursor: pointer;
}

.filter__btn{
  cursor: pointer;
}

.container {
  max-width: 1440px;
  margin: 0 auto;
  padding: 0 20px;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: repeat(2, 1fr);
}

.card {
  display: flex;
  align-items: center;
  gap: 15px;
  color: white;
  width: 600px;
  height: 220px;
  display: flex;
  overflow: hidden;
  background: rgb(60, 62, 68);
  border-radius: 0.5rem;
  margin: 0.75rem;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
}

.card__img {
  width: auto;
  height: auto;
  object-fit: cover;
}

.card__img img {
  max-width: 220px;
  width: 100%;
  margin: 0px;
  opacity: 1;
  transition: opacity 0.5s ease 0s;
  object-position: center center;
  object-fit: cover;
}

.card__info-title {
  font-size: 1.5rem;
}

.card__info-subtitle {
  display: flex;
  align-items: center;
  text-transform: capitalize;
  margin-left: 10px;
  margin-bottom: 20px;
  position: relative;
}
.card__info-subtitle::before {
  content: "";
  height: 0.5rem;
  width: 0.5rem;
  margin-right: 0.375rem;
  background: rgb(214, 61, 46);
  border-radius: 50%;
}

.card__info-body span,
.card__info-footer span {
  color: rgb(158, 158, 158);
}

.card__info-info {
  margin-top: 10px;
  margin-bottom: 20px;
}

.pagination {
  display: flex;
  gap: 10px;
  margin: 20px;
}

.pagination__btn {
  padding: 10px 20px;
  background: rgb(251, 251, 251);
  box-shadow: rgba(188, 187, 187, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
  border: none;
  cursor: pointer;
}
</style>
