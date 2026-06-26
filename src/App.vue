<script setup>
import { ref, computed, onUnmounted } from 'vue'

// Local Assets Mock Database Matrix Array (Using public open-source audio nodes)
const TRACKS_DATABASE = ref([
  { id: 'rain', name: ' Deep Ambient Rain', volume: 40, active: false, audioUrl: 'https://assets.mixkit.co/active_storage/sfx/2433/2433-84.wav', instance: null },
  { id: 'keyboard', name: '⌨ Mechanical Keystrokes', volume: 50, active: false, audioUrl: 'https://assets.mixkit.co/active_storage/sfx/1169/1169-84.wav', instance: null },
  { id: 'cafe', name: ' Coffee Shop Chatter', volume: 30, active: false, audioUrl: 'https://assets.mixkit.co/active_storage/sfx/2568/2568-84.wav', instance: null }
])


const toggleTrack = (track) => {
  track.active = !track.active

  if (track.active) {
   
    if (!track.instance) {
      track.instance = new Audio(track.audioUrl)
      track.instance.loop = true
    }
    track.instance.volume = track.volume / 100
    track.instance.play().catch(err => console.log("Audio node playback error:", err))
  } else {
    if (track.instance) {
      track.instance.pause()
    }
  }
}


const updateVolume = (track) => {
  if (track.instance && track.active) {
    track.instance.volume = track.volume / 100
  }
}


const globalAudioLoad = computed(() => {
  const activeTracks = TRACKS_DATABASE.value.filter(t => t.active)
  if (activeTracks.length === 0) return 0
  const sum = activeTracks.reduce((acc, curr) => acc + curr.volume, 0)
  return Math.round(sum / activeTracks.length)
})


onUnmounted(() => {
  TRACKS_DATABASE.value.forEach(track => {
    if (track.instance) {
      track.instance.pause()
      track.instance = null
    }
  })
})
</script>

<template>
  <div class="app-wrapper">
    
    <header class="app-header">
      <h1> DevSound Matrix</h1>
      <p>An interactive Vue 3 audio architecture platform engineered to stream and mix multi-layered focus soundscapes.</p>
    </header>

    <main class="studio-desk">
      
      <section class="control-panel">
        <div class="panel-header">
          <h3>Ambient Focus Channels</h3>
          <span class="load-badge">Matrix Output Load: {{ globalAudioLoad }}%</span>
        </div>

        <div class="tracks-grid">
          <div 
            v-for="track in TRACKS_DATABASE" 
            :key="track.id" 
            class="track-card"
            :class="{ 'card-active': track.active }"
          >
            <div class="track-info-row">
              <span class="track-title">{{ track.name }}</span>
              <button @click="toggleTrack(track)" class="toggle-btn">
                {{ track.active ? '🛑 Stop' : '▶ Stream' }}
              </button>
            </div>

            <div class="volume-slider-group">
              <label>Channel Gain: {{ track.volume }}%</label>
              <input 
                type="range" 
                min="0" 
                max="100" 
                v-model.number="track.volume"
                @input="updateVolume(track)"
                :disabled="!track.active"
              >
            </div>
          </div>
        </div>
      </section>

      <section class="visualizer-panel">
        <h3>Live Audio Spectrum Telemetry</h3>
        <div class="spectrum-canvas-box">
          <div class="frequency-bars">
            <div 
              v-for="i in 12" 
              :key="i" 
              class="freq-bar"
              :style="{ 
                height: globalAudioLoad > 0 ? `${Math.max(10, Math.random() * globalAudioLoad * 1.5)}px` : '4px',
                animationDuration: `${0.3 + (i * 0.05)}s`
              }"
              :class="{ 'animating': globalAudioLoad > 0 }"
            ></div>
          </div>
          <p class="matrix-status-text">
            {{ globalAudioLoad > 0 ? '📡 Blending active ecosystem telemetry vectors...' : '💤 Signal standby. Initialize an ambient audio stream node.' }}
          </p>
        </div>
      </section>

    </main>
  </div>
</template>

<style scoped>
.app-wrapper {
  max-width: 1200px;
  margin: 40px auto;
  padding: 0 24px;
  font-family: system-ui, -apple-system, sans-serif;
  color: #f8fafc;
}

.app-header {
  border-bottom: 2px solid #1e293b;
  padding-bottom: 20px;
  margin-bottom: 35px;
}

.app-header h1 {
  margin: 0;
  font-size: 30px;
  color: #a78bfa;
  letter-spacing: -0.5px;
}

.app-header p {
  margin: 4px 0 0 0;
  color: #64748b;
  font-size: 14px;
}

.studio-desk {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(360px, 1fr));
  gap: 40px;
}

.control-panel {
  background-color: #0f172a;
  border: 1px solid #1e293b;
  padding: 25px;
  border-radius: 16px;
  box-shadow: 0 10px 15px -3px rgba(0,0,0,0.3);
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  border-bottom: 1px solid #1e293b;
  padding-bottom: 12px;
}

.panel-header h3 {
  margin: 0;
  font-size: 14px;
  color: #64748b;
  text-transform: uppercase;
}

.load-badge {
  font-size: 11px;
  font-weight: 700;
  color: #a78bfa;
  background-color: rgba(167, 139, 250, 0.1);
  padding: 4px 10px;
  border-radius: 6px;
  border: 1px solid rgba(167, 139, 250, 0.2);
}

.tracks-grid {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.track-card {
  background-color: #020617;
  border: 1px solid #1e293b;
  padding: 18px;
  border-radius: 12px;
  transition: all 0.2s ease;
}

.track-card.card-active {
  border-color: #a78bfa;
  background-color: rgba(167, 139, 250, 0.02);
}

.track-info-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 14px;
}

.track-title {
  font-weight: 600;
  font-size: 15px;
}

.toggle-btn {
  padding: 6px 14px;
  border: none;
  border-radius: 6px;
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
  background-color: #1e293b;
  color: #cbd5e1;
  transition: all 0.15s;
}

.track-card.card-active .toggle-btn {
  background-color: #ef4444;
  color: #fff;
}

.volume-slider-group label {
  display: block;
  font-size: 11px;
  color: #64748b;
  margin-bottom: 6px;
}

.volume-slider-group input[type="range"] {
  width: 100%;
  accent-color: #a78bfa;
  cursor: pointer;
}

.volume-slider-group input[type="range"]:disabled {
  opacity: 0.3;
  cursor: not-allowed;
}

.visualizer-panel {
  display: flex;
  flex-direction: column;
}

.visualizer-panel h3 {
  margin-top: 0;
  margin-bottom: 15px;
  font-size: 14px;
  color: #64748b;
  text-transform: uppercase;
}

.spectrum-canvas-box {
  background-color: #0f172a;
  border: 1px solid #1e293b;
  border-radius: 16px;
  padding: 30px;
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 250px;
}

.frequency-bars {
  display: flex;
  align-items: flex-end;
  gap: 6px;
  height: 120px;
  margin-bottom: 25px;
}

.freq-bar {
  width: 10px;
  background-color: #a78bfa;
  border-radius: 4px 4px 0 0;
  transition: height 0.1s linear;
}

.freq-bar.animating {
  animation: pulse infinite ease-in-out alternate;
}

.matrix-status-text {
  font-family: monospace;
  font-size: 12px;
  color: #64748b;
  text-align: center;
  margin: 0;
  line-height: 1.5;
}

@keyframes pulse {
  0% { transform: scaleY(1); opacity: 0.6; }
  100% { transform: scaleY(1.3); opacity: 1; filter: drop-shadow(0 0 8px #a78bfa); }
}
</style>