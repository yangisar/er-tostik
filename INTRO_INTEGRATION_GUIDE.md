# 🎮 Er Tostik - Complete Integration Guide with Intro Scene

## 📦 What You Have Now

Your current game (`er_tostik_mobile_final.html`) has:
- ✅ All gameplay mechanics working
- ✅ Beautiful enemies and animations  
- ✅ Progressive background system
- ✅ Mobile controls
- ✅ Down button for climbing

## 🎬 Adding the Epic Intro Scene

### STEP 1: Add Audio System (Top of your `<script>` section)

```javascript
// ============================================================================
// AUDIO SYSTEM - Background Music & Procedural Sound Effects
// ============================================================================

class AudioSystem {
    constructor() {
        this.audioContext = null;
        this.bgMusic = null;
        this.musicVolume = 0.3;
        this.sfxVolume = 0.5;
    }

    init() {
        document.addEventListener('touchstart', () => this.initContext(), { once: true });
        document.addEventListener('click', () => this.initContext(), { once: true });
    }

    initContext() {
        if (!this.audioContext) {
            this.audioContext = new (window.AudioContext || window.webkitAudioContext)();
            this.loadMusic();
        }
    }

    loadMusic() {
        this.bgMusic = new Audio('./theme.mp3');
        this.bgMusic.volume = this.musicVolume;
        this.bgMusic.loop = true;
        this.bgMusic.addEventListener('error', () => {
            console.log('Background music not found');
        });
    }

    playMusic() {
        if (this.bgMusic && this.audioContext) {
            this.bgMusic.play().catch(e => console.log('Music blocked:', e));
        }
    }

    stopMusic() {
        if (this.bgMusic) {
            this.bgMusic.pause();
            this.bgMusic.currentTime = 0;
        }
    }

    // Procedural SFX
    playJump() {
        if (!this.audioContext) return;
        const osc = this.audioContext.createOscillator();
        const gain = this.audioContext.createGain();
        osc.connect(gain);
        gain.connect(this.audioContext.destination);
        osc.frequency.setValueAtTime(300, this.audioContext.currentTime);
        osc.frequency.exponentialRampToValueAtTime(600, this.audioContext.currentTime + 0.1);
        gain.gain.setValueAtTime(this.sfxVolume * 0.3, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.1);
        osc.start(this.audioContext.currentTime);
        osc.stop(this.audioContext.currentTime + 0.1);
    }

    playAttack() {
        if (!this.audioContext) return;
        const osc = this.audioContext.createOscillator();
        const gain = this.audioContext.createGain();
        osc.type = 'sawtooth';
        osc.connect(gain);
        gain.connect(this.audioContext.destination);
        osc.frequency.setValueAtTime(200, this.audioContext.currentTime);
        osc.frequency.exponentialRampToValueAtTime(100, this.audioContext.currentTime + 0.05);
        gain.gain.setValueAtTime(this.sfxVolume * 0.4, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.05);
        osc.start(this.audioContext.currentTime);
        osc.stop(this.audioContext.currentTime + 0.05);
    }

    playHit() {
        if (!this.audioContext) return;
        const osc = this.audioContext.createOscillator();
        const gain = this.audioContext.createGain();
        osc.type = 'square';
        osc.connect(gain);
        gain.connect(this.audioContext.destination);
        osc.frequency.setValueAtTime(150, this.audioContext.currentTime);
        osc.frequency.exponentialRampToValueAtTime(50, this.audioContext.currentTime + 0.1);
        gain.gain.setValueAtTime(this.sfxVolume * 0.5, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.1);
        osc.start(this.audioContext.currentTime);
        osc.stop(this.audioContext.currentTime + 0.1);
    }

    playPickup() {
        if (!this.audioContext) return;
        const osc = this.audioContext.createOscillator();
        const gain = this.audioContext.createGain();
        osc.connect(gain);
        gain.connect(this.audioContext.destination);
        osc.frequency.setValueAtTime(400, this.audioContext.currentTime);
        osc.frequency.exponentialRampToValueAtTime(800, this.audioContext.currentTime + 0.1);
        gain.gain.setValueAtTime(this.sfxVolume * 0.3, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.15);
        osc.start(this.audioContext.currentTime);
        osc.stop(this.audioContext.currentTime + 0.15);
    }
}

const audio = new AudioSystem();
audio.init();
```

### STEP 2: Add Intro Scene HTML (After game-over div)

```html
<!-- Intro Scene: Er Tostik meets the Oracle -->
<div id="intro-scene" class="screen-overlay hidden" style="background: linear-gradient(to bottom, #87ceeb 0%, #f0e68c 70%, #d2b48c 100%); justify-content: flex-start;">
    <canvas id="introCanvas" style="position: absolute; width: 100%; height: 100%; image-rendering: pixelated;"></canvas>
    <div id="oracle-bubble" class="oracle-bubble hidden">
        TO SAVE YOUR BROTHERS,<br>
        YOU MUST DESCEND<br>
        INTO THE UNDERWORLD
    </div>
    <div class="intro-instruction">
        WALK RIGHT TO THE CHASM<br>
        OR PRESS JUMP
    </div>
</div>
```

### STEP 3: Add CSS for Intro Scene (in your `<style>` section)

```css
.screen-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    pointer-events: auto;
}

.oracle-bubble {
    position: absolute;
    top: 10%;
    left: 50%;
    transform: translateX(-50%);
    max-width: 85%;
    background: rgba(255, 255, 255, 0.95);
    border: 4px solid #000;
    border-radius: 20px;
    padding: 20px 25px;
    font-family: 'Press Start 2P';
    font-size: clamp(9px, 2.2vw, 13px);
    color: #000;
    text-align: center;
    line-height: 1.8;
    box-shadow: 0 8px 30px rgba(0,0,0,0.6);
    z-index: 5;
    animation: fadeIn 0.5s ease-out;
}

@keyframes fadeIn {
    from { opacity: 0; transform: translateX(-50%) translateY(-20px); }
    to { opacity: 1; transform: translateX(-50%) translateY(0); }
}

.oracle-bubble:after {
    content: '';
    position: absolute;
    bottom: -25px;
    left: 50%;
    transform: translateX(-50%);
    width: 0;
    height: 0;
    border-left: 20px solid transparent;
    border-right: 20px solid transparent;
    border-top: 25px solid rgba(255, 255, 255, 0.95);
}

.intro-instruction {
    position: absolute;
    bottom: 30px;
    width: 100%;
    text-align: center;
    font-size: clamp(9px, 2.2vw, 12px);
    color: #000;
    text-shadow: 2px 2px 4px #fff;
    padding: 0 20px;
    line-height: 1.8;
    z-index: 5;
    animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.6; }
}
```

### STEP 4: Add Intro Scene JavaScript (Before your startGame function)

```javascript
// ============================================================================
// INTRO SCENE: Er Tostik and the Oracle
// ============================================================================

let introCanvas, introCtx, introActive = false;
let introHero = { x: 0, y: 0, vx: 0, frame: 0 };
let introFrame = 0;
let oracleShown = false;

function showIntro() {
    document.getElementById('start-screen').classList.add('hidden');
    document.getElementById('intro-scene').classList.remove('hidden');
    
    introCanvas = document.getElementById('introCanvas');
    introCanvas.width = window.innerWidth;
    introCanvas.height = window.innerHeight;
    introCtx = introCanvas.getContext('2d');
    introCtx.imageSmoothingEnabled = false;
    
    // Position hero at left edge
    introHero.x = introCanvas.width * 0.15;
    introHero.y = introCanvas.height * 0.65;
    introHero.vx = 0;
    introHero.frame = 0;
    
    introActive = true;
    oracleShown = false;
    
    // Show oracle speech after 1 second
    setTimeout(() => {
        document.getElementById('oracle-bubble').classList.remove('hidden');
        oracleShown = true;
    }, 1000);
    
    requestAnimationFrame(updateIntro);
}

function updateIntro() {
    if (!introActive) return;
    
    introFrame++;
    const W = introCanvas.width;
    const H = introCanvas.height;
    
    // Clear with gradient sky
    const gradient = introCtx.createLinearGradient(0, 0, 0, H);
    gradient.addColorStop(0, '#87ceeb');
    gradient.addColorStop(0.7, '#f0e68c');
    gradient.addColorStop(1, '#d2b48c');
    introCtx.fillStyle = gradient;
    introCtx.fillRect(0, 0, W, H);
    
    // Draw sun
    introCtx.fillStyle = '#ffd700';
    introCtx.shadowBlur = 30;
    introCtx.shadowColor = '#ff0';
    introCtx.beginPath();
    introCtx.arc(W * 0.85, H * 0.2, 40, 0, Math.PI * 2);
    introCtx.fill();
    introCtx.shadowBlur = 0;
    
    // Draw steppe grass
    introCtx.fillStyle = '#9acd32';
    for (let i = 0; i < W; i += 20) {
        const waveOffset = Math.sin((i + introFrame) * 0.05) * 5;
        introCtx.fillRect(i, H * 0.7 + waveOffset, 18, H * 0.3);
    }
    
    // Draw chasm (dark void)
    const chasmX = W * 0.7;
    const chasmW = W * 0.25;
    introCtx.fillStyle = '#000';
    introCtx.fillRect(chasmX, H * 0.7, chasmW, H * 0.3);
    
    // Draw rocky edges
    introCtx.fillStyle = '#8b7355';
    introCtx.fillRect(chasmX - 10, H * 0.7, 10, H * 0.3);
    introCtx.fillRect(chasmX + chasmW, H * 0.7, 10, H * 0.3);
    
    // Draw oracle on far side (3x taller)
    const oracleX = W * 0.88;
    const oracleY = H * 0.5;
    
    // Oracle robe
    introCtx.fillStyle = '#1a0033';
    introCtx.beginPath();
    introCtx.moveTo(oracleX, oracleY);
    introCtx.lineTo(oracleX - 30, oracleY + 120);
    introCtx.lineTo(oracleX + 30, oracleY + 120);
    introCtx.closePath();
    introCtx.fill();
    
    // Oracle hood
    introCtx.fillStyle = '#0d001a';
    introCtx.beginPath();
    introCtx.ellipse(oracleX, oracleY, 25, 35, 0, 0, Math.PI * 2);
    introCtx.fill();
    
    // Glowing eyes
    const eyeGlow = 0.5 + Math.sin(introFrame * 0.1) * 0.5;
    introCtx.fillStyle = `rgba(255, 100, 200, ${eyeGlow})`;
    introCtx.shadowBlur = 15;
    introCtx.shadowColor = '#f0f';
    introCtx.beginPath();
    introCtx.arc(oracleX - 8, oracleY + 5, 4, 0, Math.PI * 2);
    introCtx.arc(oracleX + 8, oracleY + 5, 4, 0, Math.PI * 2);
    introCtx.fill();
    introCtx.shadowBlur = 0;
    
    // Handle input - move hero right
    if (inputs.x > 0.3 || inputs.jump) {
        introHero.vx = 3;
    }
    
    introHero.x += introHero.vx;
    
    // Draw hero (using warrior sprite)
    const heroAnim = introHero.vx > 0 ? 'walk' : 'idle';
    if (introHero.vx > 0) {
        introHero.frame++;
    }
    warrior.draw(introCtx, heroAnim, Math.floor(introHero.frame / 10), 
                 introHero.x, introHero.y, 1, false, false);
    
    // Check if hero reached chasm
    if (introHero.x > chasmX) {
        // Transition to main game
        introActive = false;
        document.getElementById('intro-scene').classList.add('hidden');
        audio.playMusic();
        startGame();
        return;
    }
    
    requestAnimationFrame(updateIntro);
}
```

### STEP 5: Update START button

Change:
```html
<button class="start-btn" onclick="startGame()">▶ START ◀</button>
```

To:
```html
<button class="start-btn" onclick="showIntro()">▶ START ◀</button>
```

### STEP 6: Add Sound Effects to Gameplay

In your jump code, add:
```javascript
audio.playJump();
```

In your attack code, add:
```javascript
audio.playAttack();
```

When taking damage, add:
```javascript
audio.playHit();
```

When picking up items, add:
```javascript
audio.playPickup();
```

## 🎵 Adding Your Soundtrack

1. Save your MP3 file as `theme.mp3`
2. Place it in the same folder as your HTML file
3. The game will automatically load and play it!

## 🎮 How It Works

1. **Title Screen** → Player clicks START
2. **Intro Scene** → Shows Er Tostik at chasm edge, Oracle speaks
3. **Player walks right** or **presses jump** → Falls into chasm
4. **Main Game Begins** → Full gameplay in the underworld!

## 📝 Notes

- The intro scene uses the same warrior sprite you already have
- All sound effects are procedurally generated (no files needed!)
- Background music is optional (works without theme.mp3)
- Controls are active during intro scene
- Smooth transitions between all screens

Your game will now have an EPIC cinematic intro! 🌅👁️⚔️
