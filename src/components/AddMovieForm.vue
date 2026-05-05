<template>
  <div class="form-wrap">
    <h3 class="form-title">➕ 새 영화 등록</h3>
    <div class="form-row">
      <input
        v-model="title"
        type="text"
        placeholder="영화 제목을 입력하세요"
        class="form-input"
        :class="{ 'input-error': errorField === 'title' }"
      />
      <input
        v-model.number="rating"
        type="number"
        placeholder="평점 (0 ~ 10)"
        min="0"
        max="10"
        step="0.1"
        class="form-input rating-input"
        :class="{ 'input-error': errorField === 'rating' }"
      />
      <button class="add-btn" @click="submit">등록</button>
    </div>

    <transition name="slide">
      <p v-if="errorMsg" class="error-msg">⚠️ {{ errorMsg }}</p>
      <p v-else-if="successMsg" class="success-msg">✅ {{ successMsg }}</p>
    </transition>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits(['add-movie']);

const title = ref('');
const rating = ref('');
const errorMsg = ref('');
const errorField = ref('');
const successMsg = ref('');
let msgTimer = null;

const showError = (msg, field) => {
  successMsg.value = '';
  errorMsg.value = msg;
  errorField.value = field;
  clearTimeout(msgTimer);
  msgTimer = setTimeout(() => {
    errorMsg.value = '';
    errorField.value = '';
  }, 3000);
};

const showSuccess = (msg) => {
  errorMsg.value = '';
  errorField.value = '';
  successMsg.value = msg;
  clearTimeout(msgTimer);
  msgTimer = setTimeout(() => {
    successMsg.value = '';
  }, 2500);
};

const submit = () => {
  if (!title.value.trim()) {
    showError('영화 제목을 입력해주세요.', 'title');
    return;
  }
  if (rating.value === '' || isNaN(Number(rating.value))) {
    showError('평점을 입력해주세요.', 'rating');
    return;
  }
  const numRating = Number(rating.value);
  if (numRating < 0 || numRating > 10) {
    showError('평점은 0에서 10 사이로 입력해주세요.', 'rating');
    return;
  }

  emit('add-movie', { title: title.value.trim(), rating: numRating });
  showSuccess(`"${title.value.trim()}" 이(가) 등록되었습니다!`);
  title.value = '';
  rating.value = '';
};
</script>

<style scoped>
.form-wrap {
  background: #fff;
  border-radius: 14px;
  padding: 24px 28px;
  margin-bottom: 32px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.07);
}

.form-title {
  margin: 0 0 16px 0;
  font-size: 1.05rem;
  color: #2c3e50;
  font-weight: 700;
}

.form-row {
  display: flex;
  gap: 12px;
  align-items: center;
  flex-wrap: wrap;
}

.form-input {
  flex: 1;
  min-width: 160px;
  padding: 10px 14px;
  border: 1.5px solid #dde1e7;
  border-radius: 8px;
  font-size: 0.95rem;
  color: #2c3e50;
  background: #f8f9fa;
  outline: none;
  transition: border-color 0.2s, background 0.2s;
}

.form-input:focus {
  border-color: #3498db;
  background: #fff;
}

.form-input.input-error {
  border-color: #e74c3c;
  background: #fff5f5;
}

.rating-input {
  max-width: 150px;
  flex: 0 0 auto;
}

.add-btn {
  padding: 10px 26px;
  background: #2ecc71;
  color: #fff;
  border: none;
  border-radius: 8px;
  font-weight: 700;
  font-size: 0.95rem;
  cursor: pointer;
  transition: opacity 0.2s, transform 0.1s;
  white-space: nowrap;
}

.add-btn:hover  { opacity: 0.85; transform: translateY(-1px); }
.add-btn:active { transform: translateY(0); }

.error-msg,
.success-msg {
  margin: 12px 0 0 0;
  padding: 10px 14px;
  border-radius: 8px;
  font-size: 0.88rem;
  font-weight: 600;
}

.error-msg {
  background: #fff0f0;
  border-left: 4px solid #e74c3c;
  color: #c0392b;
}

.success-msg {
  background: #f0fff4;
  border-left: 4px solid #2ecc71;
  color: #27ae60;
}

.slide-enter-active,
.slide-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.slide-enter-from,
.slide-leave-to {
  opacity: 0;
  transform: translateY(-6px);
}
</style>
