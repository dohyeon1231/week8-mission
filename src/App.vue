<template>
  <div class="container">
    <h2>🎬 영화 리스트</h2>

    <!-- 옵션 A: 정렬 버튼 -->
    <div class="sort-bar">
      <button
        v-for="opt in sortOptions"
        :key="opt.key"
        class="sort-btn"
        :class="{ active: sortBy === opt.key }"
        @click="setSort(opt.key)"
      >
        {{ opt.label }}
        <span v-if="sortBy === opt.key" class="arrow">
          {{ sortDir === 'desc' ? '▼' : '▲' }}
        </span>
      </button>
    </div>

    <!-- 옵션 B: 영화 등록 폼 -->
    <AddMovieForm @add-movie="handleAdd" />

    <main v-if="sortedMovies.length > 0" class="movie-grid">
      <MovieCard
        v-for="m in sortedMovies"
        :key="m.id"
        :movie="m"
        @like-movie="handleLike"
        @delete-movie="handleDelete"
        @update-movie="handleUpdate"
      />
    </main>
    <div v-else class="empty-state">
      <p class="empty-icon">🎬</p>
      <p class="empty-text">등록된 영화가 없습니다.</p>
      <p class="empty-sub">위 폼에서 새 영화를 추가해보세요!</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import MovieCard from './components/MovieCard.vue';
import AddMovieForm from './components/AddMovieForm.vue';

const movies = ref([
  { id: 1, title: '인셉션',    rating: 9.5, likes: 0, poster: 'https://picsum.photos/seed/inception/300/450' },
  { id: 2, title: '어바웃 타임', rating: 9.2, likes: 0, poster: 'https://picsum.photos/seed/abouttime/300/450' },
  { id: 3, title: '다크 나이트', rating: 9.2, likes: 0, poster: 'https://picsum.photos/seed/darkknight/300/450' },
  { id: 4, title: '기생충',    rating: 8.9, likes: 0, poster: 'https://picsum.photos/seed/parasite/300/450' },
]);

// 옵션 A: 정렬 — 기본값 '최신순 내림차순'
const sortOptions = [
  { key: 'latest', label: '최신순' },
  { key: 'rating', label: '평점 순' },
  { key: 'likes',  label: '좋아요 순' },
];

const sortBy  = ref('latest');
const sortDir = ref('desc');

const setSort = (key) => {
  if (sortBy.value === key) {
    sortDir.value = sortDir.value === 'desc' ? 'asc' : 'desc';
  } else {
    sortBy.value  = key;
    sortDir.value = 'desc';
  }
};

const sortedMovies = computed(() => {
  const dir = sortDir.value === 'desc' ? -1 : 1;
  const list = [...movies.value];
  if (sortBy.value === 'rating') return list.sort((a, b) => (b.rating - a.rating) * dir);
  if (sortBy.value === 'likes')  return list.sort((a, b) => (b.likes  - a.likes)  * dir);
  return list.sort((a, b) => (b.id - a.id) * dir); // latest: id 기준
});

const handleLike = (targetId) => {
  const movie = movies.value.find(m => m.id === targetId);
  if (movie) movie.likes++;
};

const handleDelete = (targetId) => {
  movies.value = movies.value.filter(m => m.id !== targetId);
};

// 옵션 B: 영화 추가
const handleAdd = (newMovie) => {
  const id = Date.now();
  movies.value.push({
    ...newMovie,
    id,
    likes: 0,
    poster: `https://picsum.photos/seed/${id}/300/450`,
  });
};

// 옵션 C: 영화 수정
const handleUpdate = (updated) => {
  const movie = movies.value.find(m => m.id === updated.id);
  if (movie) {
    movie.title  = updated.title;
    movie.rating = updated.rating;
  }
};
</script>

<style>
body { margin: 0; background-color: #f0f2f5; }
</style>

<style scoped>
.container {
  padding: 40px;
  font-family: sans-serif;
  max-width: 1100px;
  margin: 0 auto;
  min-height: 100vh;
}

h2 {
  text-align: center;
  margin-bottom: 24px;
  color: #2c3e50;
  font-weight: 800;
  font-size: 1.8rem;
}

/* 옵션 A 정렬 버튼 영역 */
.sort-bar {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 28px;
}

.sort-btn {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 9px 20px;
  border: 2px solid #bdc3c7;
  border-radius: 20px;
  background: #fff;
  color: #7f8c8d;
  font-weight: 600;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.2s ease;
}

.sort-btn:hover {
  border-color: #3498db;
  color: #3498db;
}

.sort-btn.active {
  background-color: #3498db;
  border-color: #3498db;
  color: #fff;
}

.arrow {
  font-size: 0.75rem;
  line-height: 1;
}

.movie-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  gap: 25px;
}

.empty-state {
  text-align: center;
  padding: 60px 20px;
}

.empty-icon {
  font-size: 3rem;
  margin: 0 0 12px 0;
}

.empty-text {
  font-size: 1.1rem;
  font-weight: 700;
  margin: 0 0 6px 0;
  color: #7f8c8d;
}

.empty-sub {
  font-size: 0.9rem;
  margin: 0;
  color: #95a5a6;
}
</style>
