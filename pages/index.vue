<template>
  <div>
    <!-- Сначала показываем модалку -->
    <RobotModal v-if="modalReady && !modalCompleted" key="robot-modal" @modal-closed="handleModalClosed" />

    <!-- Экран загрузки (пока модалка не загружена) -->
    <div v-if="!modalReady && !pageLoaded" class="loading-screen">
      <div class="loading-spinner"></div>
    </div>

    <!-- Основной контейнер (показывается после закрытия модалки) -->
    <div v-if="pageLoaded && modalCompleted" class="page-container">
      <!-- Первая секция - на весь экран -->
      <section 
        ref="firstSection"
        class="first-section"
        :class="{ 'section-scrolling': isScrolling }"
      >
        <!-- Заголовок сверху -->
        <div class="top-header">
          <h1 class="section-title">I AM ROBOT</h1>
          <div class="solana-chain">
            <span class="chain-icon">◎</span>
            <span>SOLANA CHAIN</span>
          </div>
        </div>

        <!-- Индикатор прогресса -->
        <div class="progress-indicator">
          <div class="progress-bar">
            <div 
              class="progress-fill" 
              :style="{ width: `${(scrollCount / scrollTexts.length) * 100}%` }"
            ></div>
          </div>
          <p class="progress-text">{{ scrollCount }}/{{ scrollTexts.length }}</p>
        </div>

        <!-- Основной текст -->
        <div class="scroll-text-container">
          <div class="robot-emoji">🤖</div>
          <p class="scroll-text" :class="{ fade: isScrolling }">{{ scrollText }}</p>
          <p class="scroll-hint">Keep scrolling down ↓</p>
        </div>

        <!-- Анимация частиц при скролле -->
        <div class="scroll-particles" v-if="isScrolling">
          <div class="particle"></div>
          <div class="particle"></div>
          <div class="particle"></div>
          <div class="particle"></div>
          <div class="particle"></div>
        </div>
      </section>

      <!-- Вторая секция - с модальным окном -->
      <section 
        v-if="showSecondSection" 
        class="second-section"
      >
        <div class="crypto-content">
          <!-- Заголовок с персонажем -->
          <div class="robot-header">
            <h1 class="main-title">I AM ROBOT</h1>
            <p class="subtitle">The Most Human-Like Token on Solana</p>
          </div>

          <!-- Токен информация -->
          <div class="token-info">
            <div class="token-card">
              <div class="token-icon">🤖</div>
              <h2>$ROBOT</h2>
              <p class="token-description">
                Because even robots need financial freedom. 
                Built on Solana, because we're fast AF.
              </p>
            </div>
          </div>

          <!-- Тролльные секции -->
          <div class="features-grid">
            <div class="feature-card troll-card">
              <div class="feature-icon">🧠</div>
              <h3>AI-Powered</h3>
              <p>We're so smart, we don't even need developers. The token trades itself.</p>
            </div>
            <div class="feature-card troll-card">
              <div class="feature-icon">⚡</div>
              <h3>Lightning Fast</h3>
              <p>Solana speed. Robot precision. Human greed. Perfect combo.</p>
            </div>
            <div class="feature-card troll-card">
              <div class="feature-icon">🔒</div>
              <h3>"Secure"</h3>
              <p>Trust us, we're robots. We don't make mistakes... usually.</p>
            </div>
            <div class="feature-card troll-card">
              <div class="feature-icon">📈</div>
              <h3>To The Moon?</h3>
              <p>Maybe. Or maybe to zero. Life is a gamble, just like crypto.</p>
            </div>
          </div>

          <!-- Solana info -->
          <div class="solana-badge">
            <span class="solana-icon">◎</span>
            <span>Powered by Solana</span>
          </div>

          <!-- Социальные ссылки -->
          <div class="social-links">
            <a href="https://x.com/irobot" target="_blank" class="social-btn twitter">
              <span>𝕏</span> Twitter
            </a>
            <a href="https://t.me/irobot" target="_blank" class="social-btn telegram">
              <span>✈</span> Telegram
            </a>
            <a href="https://dexscreener.com/solana/irobot" target="_blank" class="social-btn dex">
              <span>📊</span> DexScreener
            </a>
            <a href="https://www.dextools.io/app/en/solana/pairs/irobot" target="_blank" class="social-btn dex">
              <span>📈</span> DexTools
            </a>
          </div>

          <!-- Дисклеймер (тролльный) -->
          <div class="disclaimer troll-disclaimer">
            <p>⚠️ <strong>DISCLAIMER:</strong> This is a meme token. We're not financial advisors. 
            We're robots. We barely understand human money. Invest at your own risk. 
            Or don't. We literally don't care. 🤖</p>
          </div>

          <!-- Мемный текст -->
          <div class="meme-text">
            <p>"I am Robot. I trade tokens. I make gains. I don't sleep. I don't eat. 
            I just trade. Are you human enough to keep up?"</p>
          </div>
        </div>
      </section>
    </div>
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
const modalCompleted = ref(false)

const scrollTexts = [
  'Welcome, Human',
  'Keep Scrolling',
  'You\'re Almost There',
  'One More Scroll',
  'Are You Still Here?',
  'Impressive. Most Humans Give Up.',
  'Here We Go...'
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

// Обработчик закрытия модалки
const handleModalClosed = () => {
  modalCompleted.value = true
  // После закрытия модалки показываем страницу
  setTimeout(() => {
    pageLoaded.value = true
    setupScrollHandlers()
  }, 300)
}

// Предзагрузка модалки и показ страницы
onBeforeMount(async () => {
  // Предзагружаем модалку ДО монтирования страницы
  try {
    await import('~/components/RobotModal.vue')
    modalReady.value = true
    console.log('Modal preloaded successfully in onBeforeMount')
    await nextTick()
  } catch (error) {
    console.error('Error preloading modal:', error)
    modalReady.value = true
  }
})

onMounted(async () => {
  // Убеждаемся, что модалка загружена
  if (!modalReady.value) {
    try {
      await import('~/components/RobotModal.vue')
      modalReady.value = true
      await nextTick()
    } catch (error) {
      modalReady.value = true
    }
  }
})

// Настройка обработчиков скролла (вызывается после закрытия модалки)
const setupScrollHandlers = () => {
  // Даем время на рендер
  nextTick().then(() => {
  
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
}
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
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 40px 20px 80px;
  background: linear-gradient(135deg, #000000 0%, #1a0033 25%, #000000 50%, #003333 75%, #000000 100%);
  background-size: 400% 400%;
  animation: gradientShift 15s ease infinite;
  position: relative;
  overflow: hidden;
  transition: background-position 0.5s ease;
}

.first-section.section-scrolling {
  background-position: 100% 50%;
  animation-duration: 5s;
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

.top-header {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
  width: 100%;
  animation: fadeInDown 1s ease-out;
}

.section-title {
  font-size: clamp(28px, 6vw, 56px);
  font-weight: 900;
  color: #00ffff;
  text-shadow: 
    0 0 20px rgba(0, 255, 255, 0.8),
    0 0 40px rgba(0, 255, 255, 0.5),
    0 0 60px rgba(0, 255, 255, 0.3);
  margin: 0;
  font-family: 'Courier New', 'Monaco', monospace;
  letter-spacing: 3px;
  text-transform: uppercase;
  animation: glowPulse 2s ease-in-out infinite;
}

.solana-chain {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: rgba(20, 241, 149, 0.1);
  border: 1px solid rgba(20, 241, 149, 0.3);
  border-radius: 20px;
  color: #14f195;
  font-size: 12px;
  font-weight: 600;
  font-family: 'Courier New', 'Monaco', monospace;
  letter-spacing: 1px;
  text-transform: uppercase;
}

.chain-icon {
  font-size: 16px;
  color: #14f195;
}

.progress-indicator {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  width: 100%;
  max-width: 400px;
  padding: 0 20px;
}

.progress-bar {
  width: 100%;
  height: 6px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 10px;
  overflow: hidden;
  position: relative;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #00ffff, #ff00ff, #00ffff);
  background-size: 200% 100%;
  border-radius: 10px;
  transition: width 0.5s ease;
  animation: progressGlow 2s linear infinite;
  box-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
}

.progress-text {
  font-size: 14px;
  color: #00ffff;
  font-family: 'Courier New', 'Monaco', monospace;
  margin: 0;
  text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
}

.scroll-text-container {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  flex: 1;
  justify-content: center;
}

.robot-emoji {
  font-size: 80px;
  animation: robotBounce 2s ease-in-out infinite;
  filter: drop-shadow(0 0 20px rgba(0, 255, 255, 0.5));
}

.scroll-hint {
  font-size: 14px;
  color: rgba(0, 255, 255, 0.6);
  font-family: 'Courier New', 'Monaco', monospace;
  margin: 0;
  animation: fadeBlink 2s ease-in-out infinite;
  text-transform: uppercase;
  letter-spacing: 2px;
}

.scroll-text {
  font-size: clamp(24px, 5vw, 42px);
  font-weight: 600;
  color: #00ffff;
  text-shadow: 
    0 0 10px rgba(0, 255, 255, 0.8),
    0 0 20px rgba(0, 255, 255, 0.5),
    0 0 30px rgba(0, 255, 255, 0.3),
    0 2px 10px rgba(0, 0, 0, 0.8);
  transition: opacity 0.4s ease, transform 0.4s ease, color 0.4s ease;
  font-family: 'Courier New', 'Monaco', monospace;
  letter-spacing: 3px;
  text-transform: uppercase;
  position: relative;
  z-index: 1;
  min-height: 60px;
  display: flex;
  align-items: center;
}

.scroll-text.fade {
  opacity: 0.3;
  transform: translateY(15px) scale(0.95);
  color: #00ff88;
}

.scroll-particles {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  pointer-events: none;
  z-index: 0;
}

.particle {
  position: absolute;
  width: 4px;
  height: 4px;
  background: #00ffff;
  border-radius: 50%;
  box-shadow: 0 0 10px rgba(0, 255, 255, 0.8);
  animation: particleFloat 3s ease-in-out infinite;
}

.particle:nth-child(1) {
  left: 20%;
  top: 30%;
  animation-delay: 0s;
}

.particle:nth-child(2) {
  left: 50%;
  top: 20%;
  animation-delay: 0.5s;
}

.particle:nth-child(3) {
  left: 80%;
  top: 40%;
  animation-delay: 1s;
}

.particle:nth-child(4) {
  left: 30%;
  top: 60%;
  animation-delay: 1.5s;
}

.particle:nth-child(5) {
  left: 70%;
  top: 70%;
  animation-delay: 2s;
}

.second-section {
  width: 100%;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #0a0a0a 0%, #1a0033 25%, #0a0a0a 50%, #003333 75%, #0a0a0a 100%);
  background-size: 400% 400%;
  animation: gradientShift 15s ease infinite;
  position: relative;
  overflow-x: hidden;
  padding: 60px 20px;
}

.second-section::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: 
    radial-gradient(circle at 30% 30%, rgba(0, 255, 255, 0.05) 0%, transparent 50%),
    radial-gradient(circle at 70% 70%, rgba(255, 0, 255, 0.05) 0%, transparent 50%);
  pointer-events: none;
}

.crypto-content {
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;
  position: relative;
  z-index: 1;
}

.robot-header {
  text-align: center;
  margin-bottom: 60px;
  animation: fadeInUp 1s ease-out;
}

.main-title {
  font-size: clamp(36px, 8vw, 72px);
  font-weight: 900;
  color: #00ffff;
  text-shadow: 
    0 0 20px rgba(0, 255, 255, 0.8),
    0 0 40px rgba(0, 255, 255, 0.5),
    0 0 60px rgba(0, 255, 255, 0.3);
  margin: 0 0 20px 0;
  font-family: 'Courier New', 'Monaco', monospace;
  letter-spacing: 4px;
  text-transform: uppercase;
}

.subtitle {
  font-size: clamp(16px, 3vw, 24px);
  color: #00ff88;
  text-shadow: 0 0 10px rgba(0, 255, 136, 0.5);
  margin: 0;
  font-family: 'Courier New', 'Monaco', monospace;
  letter-spacing: 2px;
}

.token-info {
  margin-bottom: 60px;
  animation: fadeInUp 1s ease-out 0.2s both;
}

.token-card {
  background: rgba(0, 255, 255, 0.05);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 20px;
  padding: 40px;
  text-align: center;
  backdrop-filter: blur(10px);
  box-shadow: 0 8px 32px rgba(0, 255, 255, 0.1);
  transition: all 0.3s ease;
}

.token-card:hover {
  border-color: rgba(0, 255, 255, 0.6);
  box-shadow: 0 8px 32px rgba(0, 255, 255, 0.3);
  transform: translateY(-5px);
}

.token-icon {
  font-size: 80px;
  margin-bottom: 20px;
  animation: float 3s ease-in-out infinite;
}

.token-card h2 {
  font-size: 48px;
  color: #00ffff;
  margin: 0 0 20px 0;
  text-shadow: 0 0 20px rgba(0, 255, 255, 0.6);
  font-family: 'Courier New', 'Monaco', monospace;
}

.token-description {
  font-size: 18px;
  color: #ffffff;
  line-height: 1.6;
  margin: 0;
  font-family: 'Courier New', 'Monaco', monospace;
}

.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 30px;
  margin-bottom: 60px;
  animation: fadeInUp 1s ease-out 0.4s both;
}

.feature-card {
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  padding: 30px;
  text-align: center;
  transition: all 0.3s ease;
  backdrop-filter: blur(5px);
}

.feature-card:hover {
  background: rgba(255, 255, 255, 0.05);
  border-color: rgba(0, 255, 255, 0.4);
  transform: translateY(-5px);
  box-shadow: 0 8px 24px rgba(0, 255, 255, 0.2);
}

.feature-icon {
  font-size: 48px;
  margin-bottom: 20px;
}

.feature-card h3 {
  font-size: 24px;
  color: #00ffff;
  margin: 0 0 15px 0;
  text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
  font-family: 'Courier New', 'Monaco', monospace;
}

.feature-card p {
  font-size: 16px;
  color: #cccccc;
  line-height: 1.5;
  margin: 0;
  font-family: 'Courier New', 'Monaco', monospace;
}

.troll-card h3 {
  color: #ff00ff;
  text-shadow: 0 0 10px rgba(255, 0, 255, 0.5);
}

.solana-badge {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  background: rgba(0, 255, 255, 0.1);
  border: 1px solid rgba(0, 255, 255, 0.3);
  border-radius: 50px;
  padding: 12px 24px;
  margin: 0 auto 40px;
  color: #00ffff;
  font-family: 'Courier New', 'Monaco', monospace;
  font-size: 16px;
  text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
  animation: fadeInUp 1s ease-out 0.6s both;
}

.solana-badge {
  display: flex;
  justify-content: center;
}

.solana-icon {
  font-size: 24px;
  color: #14f195;
}

.social-links {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
  margin-bottom: 40px;
  animation: fadeInUp 1s ease-out 0.8s both;
}

.social-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 14px 28px;
  background: rgba(0, 255, 255, 0.1);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 12px;
  color: #00ffff;
  text-decoration: none;
  font-family: 'Courier New', 'Monaco', monospace;
  font-size: 16px;
  font-weight: 600;
  transition: all 0.3s ease;
  text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
  cursor: pointer;
}

.social-btn:hover {
  background: rgba(0, 255, 255, 0.2);
  border-color: rgba(0, 255, 255, 0.6);
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0, 255, 255, 0.3);
}

.social-btn.twitter {
  border-color: rgba(255, 255, 255, 0.3);
  color: #ffffff;
}

.social-btn.telegram {
  border-color: rgba(0, 136, 255, 0.3);
  color: #0088ff;
}

.social-btn.dex {
  border-color: rgba(255, 165, 0, 0.3);
  color: #ffa500;
}

.disclaimer {
  background: rgba(255, 0, 0, 0.1);
  border: 2px solid rgba(255, 0, 0, 0.3);
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 40px;
  animation: fadeInUp 1s ease-out 1s both;
}

.troll-disclaimer p {
  color: #ff6b6b;
  font-size: 14px;
  line-height: 1.6;
  margin: 0;
  font-family: 'Courier New', 'Monaco', monospace;
  text-align: center;
}

.meme-text {
  text-align: center;
  padding: 30px;
  background: rgba(0, 255, 255, 0.05);
  border-left: 4px solid #00ffff;
  border-radius: 8px;
  animation: fadeInUp 1s ease-out 1.2s both;
}

.meme-text p {
  font-size: 20px;
  color: #00ffff;
  font-style: italic;
  margin: 0;
  text-shadow: 0 0 15px rgba(0, 255, 255, 0.5);
  font-family: 'Courier New', 'Monaco', monospace;
  line-height: 1.6;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

  @keyframes float {
  0%, 100% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-10px);
  }
}

/* Адаптивность */
@media (max-width: 768px) {
  .first-section {
    padding: 30px 15px 60px;
  }

  .section-title {
    letter-spacing: 2px;
  }

  .robot-emoji {
    font-size: 60px;
  }

  .scroll-text {
    font-size: 20px;
    letter-spacing: 2px;
  }

  .progress-indicator {
    max-width: 300px;
  }

  .main-title {
    letter-spacing: 2px;
  }

  .token-card h2 {
    font-size: 36px;
  }

  .token-icon {
    font-size: 60px;
  }

  .features-grid {
    grid-template-columns: 1fr;
  }

  .social-links {
    flex-direction: column;
    align-items: center;
  }

  .social-btn {
    width: 100%;
    max-width: 250px;
    justify-content: center;
  }

  .second-section {
    padding: 40px 15px;
  }
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

@keyframes fadeInDown {
  from {
    opacity: 0;
    transform: translateY(-30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes glowPulse {
  0%, 100% {
    text-shadow: 
      0 0 20px rgba(0, 255, 255, 0.8),
      0 0 40px rgba(0, 255, 255, 0.5),
      0 0 60px rgba(0, 255, 255, 0.3);
  }
  50% {
    text-shadow: 
      0 0 30px rgba(0, 255, 255, 1),
      0 0 60px rgba(0, 255, 255, 0.8),
      0 0 90px rgba(0, 255, 255, 0.5);
  }
}

@keyframes progressGlow {
  0% {
    background-position: 0% 50%;
  }
  100% {
    background-position: 200% 50%;
  }
}

@keyframes robotBounce {
  0%, 100% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-15px);
  }
}

@keyframes fadeBlink {
  0%, 100% {
    opacity: 0.6;
  }
  50% {
    opacity: 1;
  }
}

@keyframes particleFloat {
  0%, 100% {
    transform: translateY(0px) translateX(0px);
    opacity: 0;
  }
  10% {
    opacity: 1;
  }
  90% {
    opacity: 1;
  }
  100% {
    transform: translateY(-100px) translateX(50px);
    opacity: 0;
  }
}
</style>
