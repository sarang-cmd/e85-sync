# VisiPiano Rhythm Game

A fully playable browser-based rhythm game built with HTML5, CSS3, and vanilla JavaScript. Features real-time audio sync, hit detection, scoring, and an integrated debug panel for tuning.

## Quick Start

### 1. Setup Files

1. **Copy the chart file** (`VisiPiano.json`) into the same directory as `index.html`
2. **Place the audio file** in `./assets/audio/` with one of these names:
   - `song.mp3` (recommended)
   - `song.wav`
   - `song.ogg`

Your directory structure should look like:
```
project-folder/
├── index.html
├── VisiPiano.json
└── assets/
    └── audio/
        └── song.mp3
```

### 2. Run the Game

Simply open `index.html` in a modern web browser (Chrome, Firefox, Edge, Safari). The game will automatically:
- Load the chart from `VisiPiano.json`
- Attempt to load audio from `./assets/audio/`
- Show the start screen with status information

## Keyboard Controls

| Key | Lane | Action |
|-----|------|--------|
| **D** | Lane 1 (Left) | Tap/Hold Note |
| **F** | Lane 2 | Tap/Hold Note |
| **J** | Lane 3 | Tap/Hold Note |
| **K** | Lane 4 (Right) | Tap/Hold Note |
| **ESC** | - | Pause/Resume Game |

**Note:** Keys can be reassigned by modifying the `keyMap` object in the JavaScript code.

## Game Features

### Gameplay

- **4 Lanes:** Notes scroll down the screen at configurable speed
- **Tap Notes:** Quick taps that must be hit at the exact moment
- **Hold Notes:** Notes that require holding down for their duration
- **Hit Judgments:** Perfect, Great, Good, Miss (with scoring)
- **Combo System:** Build combos for consecutive perfect/great hits
- **Accuracy Tracking:** Real-time accuracy percentage
- **Score:** Points based on hit quality (Perfect=100, Great=75, Good=50)

### Audio Sync

- **Real-time Sync:** Game syncs to audio playback using `audio.currentTime`
- **Silent Mode Fallback:** If audio file not found, game runs with chart timer
- **Offset Calibration:** Adjustable offset (in milliseconds) to fix sync issues
- **Audio Status Display:** Start screen shows whether audio loaded successfully

### Visual Polish

- Modern dark theme with cyan/magenta accents
- Smooth note animations and feedback
- Glowing effects and shadows
- Lane activation visual feedback
- Progress display and song timer

## Debug Panel

Press the **"Debug Panel"** button on the start screen to enable live debugging during gameplay.

### Available Debug Controls

All debug parameters update in real-time without requiring restart:

| Parameter | Range | Purpose |
|-----------|-------|---------|
| **BPM** | 30–300 | Affects note timing (informational) |
| **Offset (ms)** | -500 to +500 | Adjusts chart time relative to audio |
| **Scroll Speed** | 100–800 px/s | How fast notes move down |
| **Spawn Lead** | 0.5–3 s | How far ahead notes appear |
| **Autoplay** | Toggle | Computer plays perfect on all notes |
| **Restart Chart** | Button | Instantly restart with new settings |

### Debugging Workflow

1. **Audio out of sync?**
   - Adjust the **Offset** value up or down
   - Listen for when notes hit versus when they sound
   - Use **Restart Chart** to test immediately

2. **Notes appearing too early/late?**
   - Adjust **Spawn Lead** if you want earlier warning
   - Adjust **Scroll Speed** if notes move too fast/slow

3. **Testing a specific section?**
   - Use **Restart Chart** to replay from the beginning
   - Adjust **Offset** to synchronize perfectly
   - Note: Chart seeking not yet implemented (always starts at 0:00)

## Chart Format (VisiPiano.json)

The game parses the compressed MIDI-derived chart with this structure:

```json
{
  "header": {
    "PPQ": 480,
    "bpm": 120,
    "name": "Song Title"
  },
  "tempo": [
    {
      "absoluteTime": 0,
      "seconds": 0,
      "bpm": 120
    }
  ],
  "tracks": [
    {
      "notes": [
        {
          "name": "D4",
          "midi": 62,
          "time": 0.0115,      // in seconds
          "velocity": 0.64,
          "duration": 0.151    // hold length in seconds
        }
      ]
    }
  ]
}
```

### How Notes Are Processed

1. **MIDI Pitch → Lane Mapping**
   - Pitches are mapped to 4 lanes octave-agnostically
   - Lower MIDI values → Lower lanes
   - Formula: `lane = (midiPitch - 47) % 12 / 3`
   - Fully customizable in `midiToLane()` method

2. **Hold Detection**
   - Notes with `duration > 0.2` seconds become hold notes
   - Threshold is configurable via `settings.holdThreshold`

3. **Note Sorting**
   - All notes automatically sorted by `time` before gameplay
   - Ensures proper spawn order even if source is slightly out of order

## Settings & Configuration

All game settings are accessible in the code under the `settings` object in the `RhythmGame` constructor:

```javascript
this.settings = {
    bpm: 120,                           // Chart BPM (informational)
    offset: 0,                          // Global offset in milliseconds
    scrollSpeed: 400,                   // Pixels per second
    spawnLeadTime: 1.5,                 // Seconds to spawn before hit
    hitWindow: { 
        perfect: 0.05,                  // Perfect window in seconds
        great: 0.1,                     // Great window in seconds
        good: 0.2                       // Good window in seconds
    },
    holdThreshold: 0.2,                 // Minimum duration for hold notes
    masterVolume: 1.0,                  // Audio volume (0–1)
    autoplay: false,                    // Auto-play mode
    silentMode: false,                  // Force chart timer (no audio)
};
```

**To change defaults:** Edit these values directly in the code before loading the game.

## Audio Files

### Supported Formats
- **.mp3** (recommended, best browser support)
- **.wav** (high quality, larger files)
- **.ogg** (alternative, good compression)

### Placement
```
./assets/audio/song.mp3
./assets/audio/song.wav
./assets/audio/song.ogg
```

The game tries each format in order and uses the first one found.

### Silent Mode
If no audio file is found:
- Game automatically switches to **silent mode**
- Chart timer runs using JavaScript timing (`performance.now()`)
- A warning banner appears on startup
- Gameplay is fully functional without audio

## Scoring System

| Judgment | Criteria | Points | Combo |
|----------|----------|--------|-------|
| **Perfect** | ±50ms | 100 | ✓ |
| **Great** | ±100ms | 75 | ✓ |
| **Good** | ±200ms | 50 | ✓ |
| **Miss** | Outside windows | 0 | ✗ |

**Accuracy Calculation:**
```
accuracy = (perfect_hits + great_hits * 0.8) / total_hits * 100%
```

## Architecture

### Core Modules

- **RhythmGame Class**: Main engine managing state, input, rendering, and audio
- **Chart Loading**: Async JSON parsing and MIDI-to-lane conversion
- **Audio Manager**: Handles multiple format attempts, fallback to silent mode
- **Game Loop**: requestAnimationFrame-based sync and 60 FPS rendering
- **Input Handler**: Real-time keyboard input with lane state tracking
- **Debug Panel**: Live parameter adjustment without code editing

### Key Methods

| Method | Purpose |
|--------|---------|
| `loadChart()` | Fetches and parses VisiPiano.json |
| `loadAudio()` | Tries multiple audio formats with fallback |
| `gameLoop()` | Main update/render loop |
| `getChartTime()` | Returns current playback time (audio sync or timer) |
| `spawnNotes()` | Creates rendered note objects when they enter spawn window |
| `activateLane()` | Handles tap input, hit detection, judging |
| `render()` | Draws notes, feedback, and visual effects |

### Audio Sync Strategy

1. **With Audio:** `audio.currentTime` is the source of truth for gameplay timing
2. **Without Audio:** `performance.now() / 1000 - chartStartTime` provides chart clock
3. **Offset Handling:** Global millisecond offset applied before timing comparisons
4. **Stability:** All spawn/judgment calculations use the same `getChartTime()` reference

## Customization

### Change Keyboard Controls

Edit the `keyMap` in the constructor:
```javascript
this.keyMap = { 
    d: 0, f: 1, j: 2, k: 3,  // Current mapping
    // Example: remap to arrow keys
    // arrowup: 0, arrowdown: 1, arrowleft: 2, arrowright: 3
};
```

### Change Lane Mapping

Edit the `midiToLane()` method to customize how MIDI pitches map to lanes:
```javascript
midiToLane(midiPitch) {
    // Current: octave-agnostic, 4 lanes
    const normalized = ((midiPitch - 47) % 12);
    return Math.floor(normalized / 3) % 4;
    
    // Alternative: map specific pitch ranges
    // if (midiPitch < 50) return 0;
    // if (midiPitch < 60) return 1;
    // etc...
}
```

### Change Colors & Styling

All colors use CSS variables in `:root`:
```css
:root {
    --primary: #00d4ff;           /* Cyan */
    --secondary: #ff006e;         /* Magenta */
    --accent-green: #00ff88;      /* Green */
    --accent-orange: #ff6b35;     /* Orange */
    --accent-purple: #a366ff;     /* Purple */
    /* ... more variables ... */
}
```

Modify these to rebrand the entire game.

### Change Hit Windows

Edit the `hitWindow` in settings:
```javascript
hitWindow: { 
    perfect: 0.05,   // ±50ms
    great: 0.1,      // ±100ms
    good: 0.2        // ±200ms
},
```

Smaller windows = stricter. Larger windows = more forgiving.

## Troubleshooting

### Audio Not Playing

1. **Check file path:** Ensure `./assets/audio/song.mp3` exists relative to `index.html`
2. **Check file format:** Browser may not support the format (use .mp3 for widest support)
3. **Check browser permissions:** Some browsers block autoplay with sound
4. **Check console:** Open browser DevTools (F12) for error messages
5. **Silent mode:** If file truly missing, game runs with chart timer automatically

### Notes Out of Sync

1. **Use Debug Panel:** Adjust **Offset** slider while playing
2. **Try different values:** Start with ±100ms and fine-tune
3. **Check audio delay:** Some audio systems introduce latency
4. **Verify chart format:** Ensure chart `time` values are in seconds, not ticks

### Notes Appearing Too Early/Late

1. Adjust **Spawn Lead** value (default 1.5 seconds)
2. Adjust **Scroll Speed** to change how fast notes move
3. Use **Restart Chart** to test immediately in debug panel

### Keyboard Input Not Working

1. **Check key bindings:** Verify you're using D, F, J, K (case-insensitive)
2. **Check focus:** Click on the page to ensure it has focus
3. **Check console:** DevTools may show input errors

### Game Crashes or Freezes

1. **Open DevTools:** Press F12 to check console for errors
2. **Check chart file:** Ensure VisiPiano.json is valid JSON
3. **Try smaller chart:** Test with a shorter song first
4. **Restart browser:** Clear cache and reload

## Browser Support

- **Chrome/Chromium:** Full support
- **Firefox:** Full support
- **Safari:** Full support (may require user gesture for audio playback)
- **Edge:** Full support
- **Mobile browsers:** Limited support (game designed for desktop)

## Performance

- **Target:** 60 FPS on modern hardware
- **Rendering:** Canvas-based with requestAnimationFrame
- **Audio:** HTML5 Web Audio API (via `<audio>` element)
- **No external libraries:** Pure JavaScript, no dependencies

## What's Next?

Potential improvements you can add:

1. **Chart Editor:** UI to create/modify charts
2. **Note Seeking:** Jump to specific timestamps
3. **Multiplayer:** Network sync for competitive play
4. **More Lanes:** Extend to 6+ lanes
5. **Animations:** Intro/outro sequences
6. **Leaderboards:** Local storage of high scores
7. **Difficulty Levels:** Easy/Normal/Hard variants
8. **Video Sync:** Background video playback with charts
9. **Effect Combos:** Special effects for combo milestones
10. **Mobile Touch:** Touch input for mobile devices

## License

This game is provided as-is for development and educational purposes.

## Support & Feedback

For issues, improvements, or questions:
1. Check the troubleshooting section above
2. Review the code comments in `index.html`
3. Test with the debug panel to isolate problems
4. Examine browser console (F12) for error details

---

**Enjoy the game!** 🎮🎵
