<template>
  <div class="app">
    <div class="container">
      <h1 class="title">📶 WiFi信号放大器</h1>
      <p class="subtitle">量子级信号增幅技术 · 遥遥领先</p>

      <div class="wifi-area" :style="{ paddingTop: isAmplified ? '160px' : '20px', transition: isAmplified ? 'padding-top 0.6s cubic-bezier(0.34, 1.56, 0.64, 1)' : 'none' }">
        <div class="wifi-icon-wrapper" :style="{ transform: `scale(${wifiScale})`, transition: isAmplified ? 'transform 0.6s cubic-bezier(0.34, 1.56, 0.64, 1)' : 'none' }">
          <img class="wifi-img" src="/img/WiFi.png" alt="WiFi" />
          <div class="signal-text">
            当前信号强度：<strong>40%</strong>
          </div>
        </div>
      </div>

      <div v-if="isProcessing" class="progress-section">
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: progress + '%' }">
            <span class="progress-glow"></span>
          </div>
        </div>
        <div class="progress-text">
          <span class="progress-percent">{{ progress }}%</span>
        </div>
        <div class="tip-text">
          <transition name="tip-fade" mode="out-in">
            <p :key="currentTip">{{ currentTip }}</p>
          </transition>
        </div>
      </div>

      <div v-if="isAmplified" class="success-section">
        <!-- <div class="success-badge">✅信号放大成功！</div> -->
        <p class="success-detail">信号强度已放大 <strong>200%</strong></p>
      </div>

      <button
        v-if="!isProcessing"
        class="amplify-btn"
        :class="{ amplified: isAmplified }"
        @click="startAmplify"
      >
        <span v-if="!isAmplified">⚡ 一键放大信号</span>
      </button>


    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const isProcessing = ref(false)
const isAmplified = ref(false)
const progress = ref(0)
const wifiScale = ref(1)
const tipIndex = ref(0)

const tips = [
    
  '🔍 正在扫描周围WiFi信号...',
  
  '🌊 正在优化5G信道...',
 
  '💾 正在压缩信号密度...'
]

const currentTip = computed(() => tips[tipIndex.value])

let progressTimer = null
let tipTimer = null

function startAmplify() {
  isProcessing.value = true
  progress.value = 0
  tipIndex.value = 0
  wifiScale.value = 1

  const totalDuration = 6000
  const interval = 50
  const steps = totalDuration / interval
  let step = 0

  tipTimer = setInterval(() => {
    if (tipIndex.value < tips.length - 1) {
      tipIndex.value++
    }
  }, 2000)

  progressTimer = setInterval(() => {
    step++
    // 使用非线性进度，前面快后面慢，最后突然加速
    const t = step / steps
    if (t < 0.7) {
      progress.value = Math.floor((t / 0.7) * 85)
    } else if (t < 0.95) {
      progress.value = Math.floor(85 + ((t - 0.7) / 0.25) * 10)
    } else {
      progress.value = Math.floor(95 + ((t - 0.95) / 0.05) * 5)
    }
    progress.value = Math.min(progress.value, 100)

    if (progress.value >= 100) {
      clearInterval(progressTimer)
      clearInterval(tipTimer)
      progress.value = 100

      setTimeout(() => {
        isProcessing.value = false
        isAmplified.value = true
        wifiScale.value = 2
      }, 500)
    }
  }, interval)
}


</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@400;700;900&display=swap');

.app {
  min-height: 100vh;
  background: linear-gradient(135deg, #e8ecf1, #d5dce6, #e0e5ec);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-family: 'Noto Sans SC', sans-serif;
  color: #2c3e50;
  padding: 20px;
}

.container {
  background: #ffffff;
  border-radius: 28px;
  padding: 40px 52px 48px;
  max-width: 680px;
  width: 100%;
  text-align: center;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.08), 0 2px 8px rgba(0, 0, 0, 0.04);
}

.title {
  font-size: 1.8rem;
  font-weight: 900;
  margin: 0 0 8px;
  color: #2c3e50;
}

.subtitle {
  font-size: 0.85rem;
  color: #95a5a6;
  margin: 0 0 8px;
  letter-spacing: 2px;
}

/* WiFi Area */
.wifi-area {
  margin: 20px auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
  position: relative;
}

.wifi-icon-wrapper {
  transform-origin: center bottom;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.wifi-img {
  width: 120px;
  height: auto;
  display: block;
  margin: 0 auto;
}

.signal-text {
  font-size: 1.1rem;
  color: #7f8c8d;
}

.signal-text strong {
  color: #2c3e50;
  font-size: 1.3rem;
}

/* Progress */
.progress-section {
  margin: 28px 0;
}

.progress-bar {
  height: 20px;
  background: #ecf0f1;
  border-radius: 10px;
  overflow: hidden;
  position: relative;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #3498db, #2ecc71);
  border-radius: 10px;
  transition: width 0.1s linear;
  position: relative;
  overflow: hidden;
}

.progress-glow {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.4), transparent);
  animation: shimmer 1.5s infinite;
}

@keyframes shimmer {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}

.progress-text {
  margin-top: 8px;
}

.progress-percent {
  font-size: 1.4rem;
  font-weight: 900;
  color: #3498db;
}

.tip-text {
  margin-top: 16px;
  min-height: 28px;
}

.tip-text p {
  margin: 0;
  font-size: 0.95rem;
  color: #7f8c8d;
  line-height: 1.5;
}

.tip-fade-enter-active,
.tip-fade-leave-active {
  transition: all 0.25s ease;
}

.tip-fade-enter-from {
  opacity: 0;
  transform: translateY(8px);
}

.tip-fade-leave-to {
  opacity: 0;
  transform: translateY(-8px);
}

/* Success */
.success-section {
  margin: 24px 0;
  animation: fadeIn 0.5s ease;
}

@keyframes fadeIn {
  from { opacity: 0; transform: scale(0.9); }
  to { opacity: 1; transform: scale(1); }
}

.success-badge {
  display: inline-block;
  background: linear-gradient(135deg, #2ecc71, #27ae60);
  color: #fff;
  padding: 8px 20px;
  border-radius: 20px;
  font-weight: 700;
  font-size: 1rem;
}

.success-detail {
  margin: 12px 0 4px;
  font-size: 1.1rem;
  color: #2c3e50;
}

.success-detail strong {
  color: #27ae60;
  font-size: 1.3rem;
}

/* Button */
.amplify-btn {
  margin-top: 20px;
  width: 100%;
  padding: 16px 32px;
  font-size: 1.15rem;
  font-weight: 700;
  border: none;
  border-radius: 14px;
  cursor: pointer;
  font-family: 'Noto Sans SC', sans-serif;
  background: linear-gradient(135deg, #3498db, #2ecc71);
  color: #fff;
  box-shadow: 0 6px 20px rgba(52, 152, 219, 0.3);
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.amplify-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 10px 28px rgba(52, 152, 219, 0.4);
}

.amplify-btn:active:not(:disabled) {
  transform: translateY(0);
}

.amplify-btn:disabled {
  cursor: not-allowed;
}

.amplify-btn.processing {
  background: linear-gradient(135deg, #3498db, #2980b9);
  animation: pulse 1.5s infinite;
}

@keyframes pulse {
  0%, 100% { box-shadow: 0 6px 20px rgba(52, 152, 219, 0.2); }
  50% { box-shadow: 0 6px 28px rgba(52, 152, 219, 0.5); }
}

.amplify-btn.amplified {
  background: linear-gradient(135deg, #2ecc71, #27ae60);
  box-shadow: 0 6px 20px rgba(46, 204, 113, 0.3);
}
</style>
