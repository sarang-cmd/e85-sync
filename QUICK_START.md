# VisiPiano Rhythm Game - Quick Setup Guide

## 📋 What You Have

✅ `index.html` - The complete rhythm game (ready to play)  
✅ `VisiPiano.json` - The chart file (already included)  
✅ `README.md` - Full documentation  

## 🚀 To Play Right Now

### Option 1: No Audio (Test Mode)
1. Open `index.html` in your browser
2. Click "Start Game"
3. Play with keyboard: **D, F, J, K**
4. Game runs in silent mode using chart timer

### Option 2: With Audio (Full Experience)
1. Create a folder: `assets/audio/` (next to index.html)
2. Place your audio file in it with one of these names:
   - `song.mp3` ← **Recommended**
   - `song.wav`
   - `song.ogg`
3. Open `index.html` in your browser
4. Start screen will show "Audio: Ready"
5. Click "Start Game"
6. Game syncs to audio playback

## 🎮 How to Play

| Action | Key |
|--------|-----|
| Lane 1 | **D** |
| Lane 2 | **F** |
| Lane 3 | **J** |
| Lane 4 | **K** |
| Pause | **ESC** |

**Goal:** Hit notes at the moment they reach the hit line at the bottom

## 🔧 Debug Panel (For Tuning)

1. On start screen, click **"Debug Panel"** button
2. During gameplay, adjust:
   - **Offset**: Fix sync issues (in milliseconds)
   - **Scroll Speed**: How fast notes move down
   - **Spawn Lead**: How far ahead notes appear
   - Click **Restart Chart** to test immediately

### Quick Sync Fix
If audio and chart are out of sync:
1. Enable debug panel
2. Listen to a note and watch when it hits
3. Adjust **Offset** ±10 or ±20 ms
4. Click **Restart Chart**
5. Repeat until perfect

## 📁 Final File Structure

```
your-project-folder/
├── index.html          ← Open this in browser
├── VisiPiano.json      ← Chart (already here)
├── README.md           ← Full documentation
├── assets/             ← Create this folder
│   └── audio/          ← Create this subfolder
│       └── song.mp3    ← Place your audio here
```

## ⚠️ What If Audio Doesn't Load?

The game will:
1. Show a warning banner: "Audio file not found. Running in silent chart preview mode."
2. Continue running perfectly with chart timer
3. Display "Audio: Not Found (Silent Mode)" on start screen

You can still play normally! Just place audio file later if desired.

## 🎯 Debug Settings Reference

**In Debug Panel during gameplay:**

| Setting | Range | What It Does |
|---------|-------|--------------|
| BPM | 30–300 | Chart metadata (info only) |
| Offset (ms) | -500 to +500 | Shifts chart time. Use ±20–50 for sync issues |
| Scroll Speed | 100–800 | How fast notes scroll down |
| Spawn Lead | 0.5–3 sec | How early notes appear before hit time |
| Autoplay | On/Off | Computer plays perfectly on all notes |
| Restart Chart | Button | Instantly restart with new settings |

## 💡 Tips

- **First time?** Try without audio to learn the patterns
- **Sync issues?** Use debug offset to calibrate by ear
- **Too hard?** Lower scroll speed or increase spawn lead
- **Too easy?** Increase scroll speed or enable autoplay to watch

## 🐛 Troubleshooting

**Can't hear audio?**
- Check file is at `./assets/audio/song.mp3`
- Try opening browser DevTools (F12) for errors
- Try a different audio format (.wav or .ogg)

**Notes feel sluggish?**
- Increase scroll speed in debug panel
- Reduce spawn lead time

**Notes feel rushed?**
- Decrease scroll speed
- Increase spawn lead time

**Keys not responding?**
- Click on the game area to focus it
- Check you're using D, F, J, K (not WASD)
- Press ESC to unpause if frozen

## 🎵 Audio File Support

Game auto-detects and loads in this order:
1. `./assets/audio/song.mp3`
2. `./assets/audio/song.wav`
3. `./assets/audio/song.ogg`

Use whichever format you have. MP3 works everywhere.

## 📖 Need More Help?

- Open `README.md` for detailed documentation
- Check browser console (F12) for error messages
- Review the code comments in `index.html`
- Use debug panel to test different settings

## 🎮 Game Features

✅ Real-time audio sync  
✅ 4-lane rhythm gameplay  
✅ Tap and hold notes  
✅ Hit judgments (Perfect, Great, Good, Miss)  
✅ Score & combo tracking  
✅ Visual feedback and effects  
✅ Debug panel for live tuning  
✅ Silent mode fallback  
✅ Responsive keyboard controls  
✅ Results screen with stats  

---

**You're all set!** Open `index.html` and start playing! 🎵🎮
