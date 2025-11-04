<template>
  <div class="page-container" v-if="pageLoaded">
    <!-- Первая секция - на весь экран -->
    <section 
      ref="firstSection"
      class="first-section"
    >
      <div class="scroll-text-container">
        <p class="scroll-text" :class="{ fade: isScrolling }">{{ scrollText }}</p>
      </div>
    </section>

    <!-- Вторая секция - с модальным окном (загружается заранее, но скрыта) -->
    <section class="second-section" v-if="modalReady" :style="{ display: showSecondSection ? 'flex' : 'none' }">
      <RobotModal key="robot-modal" />
    </section>
  </div>
  <div v-else class="loading-screen">
    <div class="loading-spinner"></div>
  </div>
</template>

<script setup>
const firstSection = ref(null)
const scrollCount = ref(0)
const scrollText = ref('Scroll Down')
const isScrolling = ref(false)
const showSecondSection = ref(false)
const canScroll = ref(true)
const pageLoaded = ref(false)
const modalReady = ref(false)

const scrollTexts = [
  'Scroll Down',
  'Scroll Harder',
  'Just continue scrolling',
  'A little bit',
  'You are freak',
  'Here we go, nice man, you do this'
]

const handleScroll = async () => {
  if (showSecondSection.value || !canScroll.value) return
  
  canScroll.value = false
  isScrolling.value = true
  
  scrollCount.value++
  
  if (scrollCount.value < scrollTexts.length) {
    scrollText.value = scrollTexts[scrollCount.value]
    
    setTimeout(() => {
      isScrolling.value = false
      canScroll.value = true
    }, 2500)
  } else {
    // Переход на вторую секцию
    showSecondSection.value = true
    await nextTick()
    setTimeout(() => {
      window.scrollTo({
        top: window.innerHeight,
        behavior: 'smooth'
      })
    }, 200)
  }
}

// Предзагрузка компонентов и показ страницы
onBeforeMount(async () => {
  // Предзагружаем модалку ДО монтирования страницы
  try {
    await import('~/components/RobotModal.vue')
    modalReady.value = true
    console.log('Modal preloaded successfully')
  } catch (error) {
    console.error('Error preloading modal:', error)
    modalReady.value = true // Все равно показываем, если ошибка
  }
})

onMounted(async () => {
  // Если модалка еще не загружена, ждем
  if (!modalReady.value) {
    try {
      await import('~/components/RobotModal.vue')
      modalReady.value = true
    } catch (error) {
      modalReady.value = true
    }
  }
  
  // Небольшая задержка для завершения загрузки
  await new Promise(resolve => setTimeout(resolve, 50))
  pageLoaded.value = true
  
  // Даем время на рендер
  await nextTick()
  
  let scrollTimeout
  let isProcessing = false
  
  const handleWheel = (e) => {
    if (showSecondSection.value || isProcessing) return
    
    const currentScrollTop = window.pageYOffset || document.documentElement.scrollTop
    
    // Только если мы в начале страницы и скроллим вниз
    if (currentScrollTop === 0 && e.deltaY > 0) {
      e.preventDefault()
      e.stopPropagation()
      
      if (!isProcessing) {
        isProcessing = true
        
        clearTimeout(scrollTimeout)
        scrollTimeout = setTimeout(() => {
          handleScroll()
          setTimeout(() => {
            isProcessing = false
          }, 1000)
        }, 50)
      }
    }
  }
  
  // Обработка скролла клавишами и тач-событиями
  const handleKeyDown = (e) => {
    if (showSecondSection.value) return
    
    const currentScrollTop = window.pageYOffset || document.documentElement.scrollTop
    
    if ((e.key === 'ArrowDown' || e.key === 'PageDown' || e.key === ' ') && currentScrollTop === 0) {
      e.preventDefault()
      handleScroll()
    }
  }
  
  window.addEventListener('wheel', handleWheel, { passive: false })
  window.addEventListener('keydown', handleKeyDown)
  
  // Блокируем обычный скролл на первой секции
  const preventScroll = (e) => {
    if (!showSecondSection.value && window.pageYOffset === 0 && e.deltaY > 0) {
      e.preventDefault()
    }
  }
  
  window.addEventListener('wheel', preventScroll, { passive: false })
  
  onUnmounted(() => {
    window.removeEventListener('wheel', handleWheel)
    window.removeEventListener('wheel', preventScroll)
    window.removeEventListener('keydown', handleKeyDown)
  })
})
</script>

<style scoped>
/* Скрытие scrollbar для всех браузеров */
.page-container {
  width: 100%;
  height: 100vh;
  overflow-x: hidden;
  overflow-y: auto;
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE и Edge */
}

.page-container::-webkit-scrollbar {
  display: none; /* Chrome, Safari, Opera */
}

.loading-screen {
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #000;
}

.loading-spinner {
  width: 50px;
  height: 50px;
  border: 3px solid rgba(0, 255, 255, 0.3);
  border-top-color: #00ffff;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

.first-section {
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  padding-bottom: 80px;
  background: linear-gradient(135deg, #000000 0%, #1a0033 25%, #000000 50%, #003333 75%, #000000 100%);
  background-size: 400% 400%;
  animation: gradientShift 15s ease infinite;
  position: relative;
  overflow: hidden;
}

.first-section::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: 
    radial-gradient(circle at 20% 50%, rgba(0, 255, 255, 0.1) 0%, transparent 50%),
    radial-gradient(circle at 80% 80%, rgba(255, 0, 255, 0.1) 0%, transparent 50%),
    radial-gradient(circle at 40% 20%, rgba(0, 255, 0, 0.05) 0%, transparent 50%);
  pointer-events: none;
}

.scroll-text-container {
  text-align: center;
}

.scroll-text {
  font-size: 32px;
  font-weight: 600;
  color: #00ffff;
  text-shadow: 
    0 0 10px rgba(0, 255, 255, 0.8),
    0 0 20px rgba(0, 255, 255, 0.5),
    0 0 30px rgba(0, 255, 255, 0.3),
    0 2px 10px rgba(0, 0, 0, 0.8);
  transition: opacity 0.4s ease, transform 0.4s ease, color 0.4s ease;
  font-family: 'Courier New', 'Monaco', monospace;
  letter-spacing: 2px;
  text-transform: uppercase;
  position: relative;
  z-index: 1;
}

.scroll-text.fade {
  opacity: 0.3;
  transform: translateY(15px);
  color: #00ff88;
}

.second-section {
  width: 100%;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #0a0a0a;
}

@keyframes gradientShift {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
