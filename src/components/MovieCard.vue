<template>
  <div class="card" :class="{ editing: isEditing }">
    <div class="poster-area">
      <img :src="movie.poster" :alt="movie.title" class="poster" />
    </div>
    <div class="content-area">

      <!-- 뷰 모드 -->
      <template v-if="!isEditing">
        <h3>{{ movie.title }}</h3>
        <p class="rating">⭐ {{ movie.rating }} / 10</p>
        <p class="likes">❤️ {{ movie.likes }}</p>
        <div class="btn-group">
          <button class="btn like-btn" @click="onLike">추천</button>
          <button class="btn edit-btn" @click="startEdit">수정</button>
          <button class="btn del-btn" @click="onDelete">삭제</button>
        </div>
      </template>

      <!-- 수정 모드 (옵션 C) -->
      <template v-else>
        <p class="edit-label">제목</p>
        <input
          v-model="editTitle"
          type="text"
          class="edit-input"
          :class="{ 'input-error': editErrorField === 'title' }"
          placeholder="영화 제목"
        />
        <p class="edit-label">평점</p>
        <input
          v-model.number="editRating"
          type="number"
          min="0"
          max="10"
          step="0.1"
          class="edit-input"
          :class="{ 'input-error': editErrorField === 'rating' }"
          placeholder="0 ~ 10"
        />
        <transition name="fade">
          <p v-if="editErrorMsg" class="error-msg">⚠️ {{ editErrorMsg }}</p>
        </transition>
        <div class="btn-group">
          <button class="btn save-btn" @click="save">저장</button>
          <button class="btn cancel-btn" @click="cancelEdit">취소</button>
        </div>
      </template>

    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
  movie: { type: Object, required: true },
});

const emit = defineEmits(['like-movie', 'delete-movie', 'update-movie']);

const isEditing = ref(false);
const editTitle = ref('');
const editRating = ref(0);
const editErrorMsg = ref('');
const editErrorField = ref('');
let errorTimer = null;

const onLike = () => emit('like-movie', props.movie.id);
const onDelete = () => emit('delete-movie', props.movie.id);

const showError = (msg, field) => {
  editErrorMsg.value = msg;
  editErrorField.value = field;
  clearTimeout(errorTimer);
  errorTimer = setTimeout(() => {
    editErrorMsg.value = '';
    editErrorField.value = '';
  }, 3000);
};

const startEdit = () => {
  editTitle.value = props.movie.title;
  editRating.value = props.movie.rating;
  editErrorMsg.value = '';
  editErrorField.value = '';
  isEditing.value = true;
};

const cancelEdit = () => {
  isEditing.value = false;
  editErrorMsg.value = '';
  editErrorField.value = '';
};

const save = () => {
  if (!editTitle.value.trim()) {
    showError('영화 제목을 입력해주세요.', 'title');
    return;
  }
  const numRating = Number(editRating.value);
  if (editRating.value === '' || isNaN(numRating)) {
    showError('평점을 입력해주세요.', 'rating');
    return;
  }
  if (numRating < 0 || numRating > 10) {
    showError('평점은 0에서 10 사이로 입력해주세요.', 'rating');
    return;
  }
  emit('update-movie', {
    id: props.movie.id,
    title: editTitle.value.trim(),
    rating: numRating,
  });
  isEditing.value = false;
};
</script>

<style scoped>
.card {
  background: #fff;
  border: 2px solid transparent;
  border-radius: 14px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.07);
  transition: transform 0.2s ease, box-shadow 0.2s ease, border-color 0.2s ease;
}

.card:not(.editing):hover {
  transform: translateY(-6px);
  box-shadow: 0 10px 24px rgba(0, 0, 0, 0.12);
}

.card.editing {
  border-color: #f39c12;
  box-shadow: 0 0 0 4px rgba(243, 156, 18, 0.15);
}

.poster-area {
  width: 100%;
  height: 280px;
  overflow: hidden;
}

.poster {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.card:not(.editing):hover .poster {
  transform: scale(1.04);
}

.content-area {
  padding: 20px;
  text-align: center;
}

h3 {
  margin: 0 0 8px 0;
  color: #2c3e50;
  font-size: 1.2rem;
  font-weight: 700;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.rating {
  font-weight: 600;
  color: #e67e22;
  margin: 4px 0;
  font-size: 0.95rem;
}

.likes {
  font-weight: 600;
  color: #e74c3c;
  margin: 4px 0 18px 0;
  font-size: 0.95rem;
}

.btn-group {
  display: flex;
  gap: 8px;
}

.btn {
  flex: 1;
  padding: 9px 0;
  cursor: pointer;
  border: none;
  border-radius: 8px;
  font-weight: 700;
  font-size: 0.85rem;
  transition: opacity 0.15s, transform 0.1s;
}

.btn:hover {
  opacity: 0.85;
  transform: translateY(-1px);
}

.btn:active {
  transform: translateY(0);
}

.like-btn  { background: #3498db; color: #fff; }
.edit-btn  { background: #f39c12; color: #fff; }
.del-btn   { background: #e74c3c; color: #fff; }
.save-btn  { background: #2ecc71; color: #fff; }
.cancel-btn { background: #95a5a6; color: #fff; }

/* 수정 모드 */
.edit-label {
  margin: 10px 0 4px 0;
  font-size: 0.78rem;
  font-weight: 600;
  color: #7f8c8d;
  text-align: left;
}

.edit-label:first-child {
  margin-top: 0;
}

.edit-input {
  display: block;
  width: 100%;
  box-sizing: border-box;
  padding: 9px 12px;
  border: 1.5px solid #dde1e7;
  border-radius: 8px;
  font-size: 0.95rem;
  color: #2c3e50;
  background: #f8f9fa;
  outline: none;
  transition: border-color 0.2s, background 0.2s;
}

.edit-input:focus {
  border-color: #f39c12;
  background: #fff;
}

.edit-input.input-error {
  border-color: #e74c3c;
  background: #fff5f5;
}

.error-msg {
  margin: 10px 0 0 0;
  padding: 8px 12px;
  background: #fff0f0;
  border-left: 3px solid #e74c3c;
  border-radius: 6px;
  color: #c0392b;
  font-size: 0.82rem;
  font-weight: 600;
  text-align: left;
}

.fade-enter-active,
.fade-leave-active { transition: opacity 0.3s ease; }
.fade-enter-from,
.fade-leave-to     { opacity: 0; }
</style>
