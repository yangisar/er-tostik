# 🎵 Quick Audio Integration - Copy & Paste These

## Where to Add Sound Effects in Your Game

### 1. JUMP SOUND
Find this code in your update() function:
```javascript
if(inputs.jump) {
    if (player.grounded) {
        player.vy = -12.5; 
        player.grounded = false; 
        player.fallStartY = player.y;
        spawnParticles(player.x, player.y + player.h/2, '#888', 8);
        audio.playJump(); // ← ADD THIS LINE
    }
}
```

### 2. ATTACK SOUND
Find this code in your attack section:
```javascript
if(inputs.atk && player.atkCooldown <= 0) {
    if (player.weapon === 'sword') {
        audio.playAttack(); // ← ADD THIS LINE AT TOP
        player.atkCooldown = 12;
        // ... rest of attack code
    }
}
```

### 3. HIT SOUND (when player takes damage)
Find where player takes damage:
```javascript
if (player.invincible <= 0 && 
    Math.hypot(player.x - e.x, (player.y-10) - e.y) < 25) {
    audio.playHit(); // ← ADD THIS LINE
    player.hp -= 15; 
    shake = 10; 
    player.invincible = 60;
}
```

### 4. PICKUP SOUND (collecting items)
Find the applyItem function:
```javascript
function applyItem(type) {
    audio.playPickup(); // ← ADD THIS LINE AT TOP
    if(type === 'red') { 
        player.hp = Math.min(100, player.hp + 30); 
        showToast("HEALTH +30", "#f44"); 
    }
    // ... rest of function
}
```

### 5. START MUSIC (when game begins)
In your startGame() function:
```javascript
function startGame() {
    audio.playMusic(); // ← ADD THIS LINE AT TOP
    document.getElementById('start-screen').classList.add('hidden');
    // ... rest of function
}
```

### 6. LANDING SOUND (when player lands on platform)
Find where player becomes grounded:
```javascript
if (!wasGrounded) {
    audio.playLand(); // ← ADD THIS LINE
    const fallDist = player.y - player.fallStartY;
    // ... rest of landing code
}
```

## Complete AudioSystem Class (Add at top of <script>)

```javascript
class AudioSystem {
    constructor() {
        this.audioContext = null;
        this.bgMusic = null;
        this.musicVolume = 0.3;
        this.sfxVolume = 0.5;
        this.init();
    }

    init() {
        document.addEventListener('touchstart', () => this.initContext(), { once: true });
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
        this.bgMusic.addEventListener('error', () => console.log('No music file'));
    }

    playMusic() {
        if (this.bgMusic) this.bgMusic.play().catch(e => {});
    }

    playJump() {
        if (!this.audioContext) return;
        const osc = this.audioContext.createOscillator();
        const gain = this.audioContext.createGain();
        osc.connect(gain);
        gain.connect(this.audioContext.destination);
        osc.frequency.setValueAtTime(300, this.audioContext.currentTime);
        osc.frequency.exponentialRampToValueAtTime(600, this.audioContext.currentTime + 0.1);
        gain.gain.setValueAtTime(0.15, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.1);
        osc.start();
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
        gain.gain.setValueAtTime(0.2, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.05);
        osc.start();
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
        gain.gain.setValueAtTime(0.25, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.1);
        osc.start();
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
        gain.gain.setValueAtTime(0.15, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.15);
        osc.start();
        osc.stop(this.audioContext.currentTime + 0.15);
    }

    playLand() {
        if (!this.audioContext) return;
        const osc = this.audioContext.createOscillator();
        const gain = this.audioContext.createGain();
        osc.type = 'triangle';
        osc.connect(gain);
        gain.connect(this.audioContext.destination);
        osc.frequency.setValueAtTime(120, this.audioContext.currentTime);
        gain.gain.setValueAtTime(0.1, this.audioContext.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.audioContext.currentTime + 0.08);
        osc.start();
        osc.stop(this.audioContext.currentTime + 0.08);
    }
}

const audio = new AudioSystem();
```

## 🎮 That's it!

1. Add the AudioSystem class at the top of your `<script>` section
2. Add `const audio = new AudioSystem();` right after the class
3. Add the sound effect calls in the 6 places shown above
4. Optionally add `theme.mp3` for background music

All sounds will work automatically! 🎵✨
