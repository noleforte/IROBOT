<template>
  <div v-if="isVisible" class="modal-overlay">
    <div class="modal-content">
      <!-- Шаг 1: Checkbox для подтверждения -->
      <div v-if="!isLoading && !isSuccess" class="checkbox-container">
        <div 
          class="checkbox-label"
          @click="toggleCheckbox"
          @mousedown.prevent
        >
          <span class="checkbox-box" :class="{ checked: isChecked }">
            <svg v-if="isChecked" class="check-icon" viewBox="0 0 24 24">
              <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z" fill="currentColor"/>
            </svg>
          </span>
          <span class="checkbox-text">I'm a robot (not a bot, promise)</span>
        </div>
        <div class="recaptcha-brand">
          <div class="recaptcha-logo">
            <svg viewBox="0 0 24 24" width="24" height="24">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z" fill="#4285F4"/>
            </svg>
          </div>
          <div class="recaptcha-text">reCAPTCHA</div>
          <div class="recaptcha-links">Privacy - Terms</div>
        </div>
      </div>

      <!-- Шаг 2: Загрузка -->
      <div v-if="isChecked && isLoading" class="loading-container">
        <div class="spinner"></div>
      </div>

      <!-- Шаг 3: Успех -->
      <div v-if="isSuccess" class="success-container">
        <div class="success-icon">✓</div>
        <p class="success-text">Welcome to the Robot Army! 🤖 Let's make some gains together.</p>
      </div>
    </div>
  </div>
</template>

<script setup>
const emit = defineEmits(['modal-closed'])

const isVisible = ref(true)
const isChecked = ref(false)
const isLoading = ref(false)
const isSuccess = ref(false)

const toggleCheckbox = () => {
  console.log('CLICK! toggleCheckbox вызван, isChecked до:', isChecked.value)
  
  if (isChecked.value) {
    console.log('Уже отмечено, выходим')
    return
  }
  
  isChecked.value = true
  console.log('isChecked установлен в true:', isChecked.value)
  
  // Небольшая задержка, чтобы пользователь увидел, что галочка поставлена
  setTimeout(() => {
    console.log('Начинаем загрузку')
    isLoading.value = true
    // Симуляция загрузки на 3 секунды
    setTimeout(() => {
      isLoading.value = false
      isSuccess.value = true
      console.log('Загрузка завершена')
      
      // Закрываем модальное окно через 3 секунды после показа успеха
      setTimeout(() => {
        isVisible.value = false
        console.log('Модальное окно закрыто')
        // Эмитим событие о закрытии модалки
        emit('modal-closed')
      }, 3000)
    }, 3000)
  }, 300)
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease-in-out;
}

.modal-content {
  background: white;
  border-radius: 16px;
  padding: 48px 64px;
  max-width: 500px;
  width: 90%;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  animation: slideUp 0.3s ease-out;
}

.checkbox-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 12px;
  cursor: pointer;
  user-select: none;
  padding: 12px 16px;
  border: 2px solid #dadce0;
  border-radius: 4px;
  background: #fff;
  transition: all 0.2s ease;
  width: 100%;
  max-width: 300px;
}

.checkbox-label:hover {
  border-color: #4285f4;
  box-shadow: 0 2px 4px rgba(66, 133, 244, 0.1);
}

.checkbox-label:active {
  transform: scale(0.98);
}

.checkbox-box {
  width: 24px;
  height: 24px;
  border: 2px solid #dadce0;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
  background: #fff;
  flex-shrink: 0;
}

.checkbox-box.checked {
  background: #4285f4;
  border-color: #4285f4;
}

.check-icon {
  width: 18px;
  height: 18px;
  color: white;
}

.checkbox-text {
  font-size: 16px;
  font-weight: 400;
  color: #202124;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
}

.recaptcha-brand {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-top: 8px;
}

.recaptcha-logo {
  display: flex;
  align-items: center;
}

.recaptcha-text {
  font-size: 12px;
  color: #80868b;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  font-weight: 400;
}

.recaptcha-links {
  font-size: 11px;
  color: #80868b;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  margin-left: 4px;
}

.loading-container,
.success-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 24px;
}

.spinner {
  width: 60px;
  height: 60px;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

.success-text {
  font-size: 18px;
  font-weight: 500;
  color: #333;
  text-align: center;
  margin: 0;
}

.success-icon {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: #10b981;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 48px;
  font-weight: bold;
  animation: scaleIn 0.5s ease-out;
}

.success-text {
  color: #10b981;
  font-size: 20px;
  font-weight: 600;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideUp {
  from {
    transform: translateY(50px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes scaleIn {
  from {
    transform: scale(0);
  }
  to {
    transform: scale(1);
  }
}
</style>
