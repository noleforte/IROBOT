<template>
  <div class="page-container">
    <!-- Первая секция - на весь экран -->
    <section 
      ref="firstSection"
      class="first-section"
    >
      <div class="scroll-text-container">
        <p class="scroll-text" :class="{ fade: isScrolling }">{{ scrollText }}</p>
      </div>
    </section>

    <!-- Вторая секция - с модальным окном -->
    <section class="second-section" v-if="showSecondSection">
      <RobotModal />
    </section>
  </div>
</template>

<script setup>
const firstSection = ref(null)
const scrollCount = ref(0)
const scrollText = ref('Scroll down')
const isScrolling = ref(false)
const showSecondSection = ref(false)
const canScroll = ref(true)

const scrollTexts = [
  'Scroll down',
  'Harder',
  'You are freak'
]

const handleScroll = () => {
  if (showSecondSection.value || !canScroll.value) return
  
  canScroll.value = false
  isScrolling.value = true
  
  scrollCount.value++
  
  if (scrollCount.value < scrollTexts.length) {
    scrollText.value = scrollTexts[scrollCount.value]
    
    setTimeout(() => {
      isScrolling.value = false
      canScroll.value = true
    }, 500)
  } else {
    // Переход на вторую секцию
    showSecondSection.value = true
    setTimeout(() => {
      window.scrollTo({
        top: window.innerHeight,
        behavior: 'smooth'
      })
    }, 200)
  }
}

// Обработка скролла колесиком мыши
onMounted(() => {
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
          }, 600)
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
.page-container {
  width: 100%;
  height: 100vh;
  overflow-x: hidden;
}

.first-section {
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  padding-bottom: 80px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  position: relative;
  overflow: hidden;
}

.scroll-text-container {
  text-align: center;
}

.scroll-text {
  font-size: 32px;
  font-weight: 600;
  color: white;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
  transition: opacity 0.3s ease, transform 0.3s ease;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
}

.scroll-text.fade {
  opacity: 0.5;
  transform: translateY(10px);
}

.second-section {
  width: 100%;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f5f5f5;
}
</style>
