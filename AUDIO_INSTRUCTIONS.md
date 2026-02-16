# Audio Setup Instructions

## Background Music
To add background music to the game, place a file named `theme.mp3` in the same directory as the HTML file.

The audio system will automatically:
- Load the music file on game start
- Loop the music continuously
- Handle errors gracefully if the file is not found
- Volume: 30% (adjustable in code)

## File Structure
```
/your-game-folder/
  ├── er_tostik_final.html
  └── theme.mp3  (your soundtrack file)
```

## Procedural Sound Effects
The game includes built-in procedural sound effects using Web Audio API:
- Jump sound (rising tone 300Hz → 600Hz)
- Attack/Sword slash (descending sawtooth 200Hz → 100Hz)
- Hit/Impact (square wave 150Hz → 50Hz)
- Item pickup (rising tone 400Hz → 800Hz)
- Death sound (long descending tone 300Hz → 50Hz)
- Landing sound (short triangle wave 120Hz)

These work automatically without any additional files!

## Testing
1. Open the HTML file in a browser
2. Background music will start when you press START
3. All sound effects work immediately with player actions
