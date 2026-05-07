# Debug Panel & Tuning Guide

## Overview

The VisiPiano rhythm game includes a comprehensive **in-game debug panel** that lets you tune all critical parameters in real-time without touching code.

---

## Enabling the Debug Panel

### During Start Screen
1. Click the **"Debug Panel"** button
2. A compact panel will appear during gameplay
3. All parameters are live-adjustable

### What You'll See
```
┌─────────────────────┐
│ DEBUG PANEL         │
├─────────────────────┤
│ Chart Time    2.34s │
│ Audio Time    2.34s │
│ Spawned       45    │
│ Hit Notes     42    │
├─────────────────────┤
│ SETTINGS            │
│ BPM      [120    ]  │
│ Offset   [0      ]  │
│ Scroll   [400    ]  │
│ Spawn    [1.50   ]  │
├─────────────────────┤
│ ☑ Autoplay         │
│ [Restart Chart]     │
└─────────────────────┘
```

---

## Parameter Reference

### Timing Parameters

#### **Chart Time** (Read-Only)
- Current playback position in seconds
- Source: Either audio.currentTime OR internal chart timer
- Use to monitor progression

#### **Audio Time** (Read-Only)
- Current audio playback position (if audio loaded)
- Shows "N/A" if no audio
- Should roughly match Chart Time (within offset)

#### **Spawned Notes** (Read-Only)
- How many notes have entered the spawn window
- Increases as song progresses
- Should roughly equal current time / average note spacing

#### **Hit Notes** (Read-Only)
- How many notes have been judged (hit or missed)
- Indicates gameplay progress
- Should never exceed spawned notes

### Adjustable Parameters

#### **BPM** (30–300)
- **What it controls:** Information value only for now
- **When to adjust:** Rarely needed; chart already contains correct BPM
- **Typical value:** 120 (from VisiPiano.json header)
- **Example:** Change if you want to experiment with perceived timing

#### **Offset** (−500 to +500 ms)
- **What it controls:** Global time shift applied to all notes
- **When to adjust:** Audio-chart sync is off
- **How it works:**
  - Positive offset = notes appear later (chart time advances slower)
  - Negative offset = notes appear earlier (chart time advances faster)
- **Typical value:** 0 (perfect sync), range is usually ±50ms
- **Example:** If notes hit too early, increase offset to +30ms

#### **Scroll Speed** (100–800 px/sec)
- **What it controls:** How fast notes move down the screen
- **When to adjust:** Visual preference or readability
- **How it works:**
  - Higher = faster scrolling, less time to react (harder)
  - Lower = slower scrolling, more reaction time (easier)
- **Typical value:** 400 px/sec
- **Example:** Increase to 600 if notes feel sluggish

#### **Spawn Lead** (0.5–3.0 sec)
- **What it controls:** How far ahead notes appear before their hit time
- **When to adjust:** Notes appear too suddenly or too early
- **How it works:**
  - Higher = see notes sooner, more reaction time
  - Lower = surprise factor, less warning
- **Typical value:** 1.5 seconds
- **Example:** Increase to 2.0 if notes feel unexpected

#### **Autoplay** (Toggle On/Off)
- **What it controls:** Computer plays perfectly on all notes
- **When to use:** Testing, watching patterns, debugging gameplay logic
- **Effect:** Removes player input, computer hits every note perfectly
- **Note:** All notes get "Perfect" judgments automatically

---

## Synchronization Tuning Guide

### Scenario: Audio Doesn't Sync With Chart

**Symptom:** Notes appear to hit at the wrong moment relative to the audio

#### Quick Diagnosis (30 seconds)

1. **Play the game** with debug panel open
2. **Listen to a note** at around the 30-second mark
3. **Watch carefully:**
   - Does note hit **before** the sound? → Need positive offset
   - Does note hit **after** the sound? → Need negative offset
4. **Make note** of the difference in milliseconds

#### Tuning Steps

1. **Small adjustments first**
   - Start with ±10ms changes
   - Note the effect

2. **Test multiple sections**
   - Early section (0–30 sec)
   - Middle section (60–90 sec)  
   - Late section (120–160 sec)
   - Sync should be consistent throughout

3. **Fine-tune incrementally**
   ```
   If notes hit early:  Offset: +10 → Restart → +20 → Restart → +30
   If notes hit late:   Offset: -10 → Restart → -20 → Restart → -30
   ```

4. **When satisfied**
   - Note the final offset value
   - You can hardcode this value later if desired

#### Example: Real Scenario

```
Initial state:
- Offset: 0
- Chart Time: 2.30s
- Audio Time: 2.30s
- Note at 2.35s hits visually
- Heard sound at 2.32s
- → Conclusion: Notes hitting early

Solution:
- Increase offset to +30ms
- Restart chart
- Now Chart Time becomes: audio - 0.030
- Same note at audio 2.32s appears at chart time 2.29s
- Visual hit occurs at 2.35s
- → Much better sync!
```

### Scenario: Offset Drift (Sync Gets Worse Over Time)

**Symptom:** Early sections sync fine, but later sections drift

**Likely cause:** MIDI chart and audio have different tempos or timing sources

#### Investigation

1. **Enable autoplay** to focus on sync, not playing
2. **Test early sections** (0–30s) - note offset value
3. **Jump mentally to middle** (60–90s) - does offset still work?
4. **Test late sections** (120–160s) - does offset still work?
5. **If offset changes needed:** Chart may have tempo changes

#### Solution

Unfortunately, if the chart has internal tempo variations, a global offset won't fix all sections perfectly. However:
- Find the offset that works best for the **majority** of the song
- Accept small timing variations in fast tempo sections
- Consider requesting a re-exported chart from source

---

## Visual Tuning Guide

### Scenario: Notes Feel Sluggish

**Symptom:** Notes take too long to reach the hit line; feels slow

#### Solution: Increase Scroll Speed

```
Current setting: Scroll Speed 400 px/sec

Try:
1. Change to 450 px/sec → Restart → Feel better? Yes? Stop.
2. If no, try 500 px/sec → Restart
3. Keep increasing by 50 until comfortable
4. Typical: 400–600 px/sec for most people
```

### Scenario: Notes Feel Rushed

**Symptom:** Notes arrive too quickly; can't react in time

#### Solution: Decrease Scroll Speed

```
Current setting: Scroll Speed 400 px/sec

Try:
1. Change to 350 px/sec → Restart
2. If still rushed, try 300 px/sec
3. Typical: 300–400 px/sec if you prefer more time
```

### Scenario: Notes Appear Unexpectedly

**Symptom:** Notes pop in suddenly; can't see them coming

#### Solution: Increase Spawn Lead

```
Current setting: Spawn Lead 1.5 sec

Try:
1. Change to 2.0 sec → Restart → See notes sooner?
2. If yes, comfortable? Stop.
3. If no, try 2.5 sec
4. Typical: 1.5–2.0 sec balances visibility and challenge
```

### Scenario: Notes Appear Too Early

**Symptom:** Notes sit on screen for too long before hitting

#### Solution: Decrease Spawn Lead

```
Current setting: Spawn Lead 1.5 sec

Try:
1. Change to 1.2 sec → Restart
2. If feels better but still early, try 1.0 sec
3. Typical: 1.0–1.5 sec for standard gameplay
```

---

## Testing Workflows

### Workflow 1: Full Sync Calibration (5 minutes)

```
1. Open index.html → Start Game → Debug Panel enabled

2. Listen to first note (around 0:05–0:15 mark)
   ├─ Does it sync? Great! You're done.
   └─ If not, continue...

3. Adjust offset:
   ├─ Notes hit early? Increase offset (+10, +20, +30)
   └─ Notes hit late? Decrease offset (−10, −20, −30)

4. Click "Restart Chart" after each change

5. Repeat steps 2–4 until 3–5 sections sync perfectly

6. Note final offset value: ________

7. (Optional) Hardcode offset in code later for next time
```

### Workflow 2: Difficulty Tuning (3 minutes)

```
1. Start game, watch autoplay mode (enable autoplay in debug)
   ├─ All notes hit perfectly automatically
   └─ Focus on visual pacing, not playing

2. Adjust scroll speed for visual comfort:
   ├─ Too slow (300)? Increase
   └─ Too fast (600)? Decrease
   └─ Comfortable at ___? Keep it

3. Adjust spawn lead for reaction time:
   ├─ Surprised by notes? Increase spawn lead
   └─ Notes sit too long? Decrease spawn lead
   └─ Good at ___? Keep it

4. Note your preferred settings:
   ├─ Scroll Speed: ___
   ├─ Spawn Lead: ___
   └─ Offset: ___
```

### Workflow 3: Troubleshooting Gameplay (10 minutes)

```
Problem: "My hits aren't registering!"

Debug steps:
1. Enable debug panel
2. Check "Hit Notes" counter:
   ├─ Increases when you play? Input is working
   └─ Doesn't change? Input may not register

3. Enable autoplay:
   ├─ Works perfectly? Gameplay logic is OK
   └─ Still broken? Chart might have issues

4. Watch hit feedback text:
   ├─ Says "Perfect", "Great", etc.? Timing is working
   └─ Says "Miss" on every note? Offset is way off

5. Adjust offset and restart:
   ├─ Try different offset values
   └─ Watch if feedback changes

6. If still broken:
   └─ Check browser console (F12) for JavaScript errors
```

---

## Debug Panel Parameter Presets

### Preset 1: Default Settings (Good All-Rounder)
```
BPM:         120
Offset:      0
Scroll:      400 px/sec
Spawn Lead:  1.5 sec
Autoplay:    Off
```

### Preset 2: Easy (Lots of Warning, Slow)
```
BPM:         120
Offset:      0
Scroll:      300 px/sec
Spawn Lead:  2.0 sec
Autoplay:    Off
```

### Preset 3: Hard (Fast, Minimal Warning)
```
BPM:         120
Offset:      0
Scroll:      550 px/sec
Spawn Lead:  1.0 sec
Autoplay:    Off
```

### Preset 4: Testing/Watching (Perfect Auto-Play)
```
BPM:         120
Offset:      0
Scroll:      400 px/sec
Spawn Lead:  1.5 sec
Autoplay:    On ← Computer plays perfectly
```

### Preset 5: Sync Calibration
```
BPM:         120
Offset:      ±50 (adjust as needed)
Scroll:      400 px/sec
Spawn Lead:  1.5 sec
Autoplay:    Off
```

---

## Common Issues & Solutions

### Issue: "Offset Setting Doesn't Change Anything"

**Diagnosis:**
- Offset only affects chart timing interpretation
- If you're in silent mode (no audio), offset won't affect audio—it's already not playing
- Offset affects when notes should hit, not audio playback speed

**Solution:**
1. Check if you have audio loaded (status on start screen)
2. If silent mode: Offset won't help (audio doesn't exist to offset against)
3. If audio loaded: Offset should visibly change sync immediately after restart

---

### Issue: "I Changed Settings But Game Looks the Same"

**Diagnosis:**
- Changes only take effect on restart
- You must click "Restart Chart" to apply new settings

**Solution:**
1. Make adjustment (e.g., Scroll Speed: 400 → 500)
2. Click "Restart Chart" button
3. Notice the change (notes move faster)

---

### Issue: "All My Hits Say 'Miss' No Matter What"

**Diagnosis:**
- Offset is drastically wrong
- Notes timing is inverted relative to actual hit moment
- Hit windows might be too strict

**Solution:**
1. Open debug panel
2. Try extreme offset values:
   - If currently 0, try +100, then −100
   - Click Restart after each change
3. Watch where feedback appears
4. Once you see "Perfect" on any note, you've found the ballpark
5. Fine-tune from there

---

### Issue: "Game Runs But No Sound"

**Possible causes:**
1. Audio file missing from ./assets/audio/
2. Audio file has wrong name (not song.mp3, .wav, or .ogg)
3. Browser autoplay policy blocking audio
4. Audio file is corrupted

**Solutions:**
1. **Check file location:**
   ```
   ./assets/audio/song.mp3  ← Must exist
   ```
2. **Try different format:**
   ```
   Rename to .wav or .ogg and reload
   ```
3. **Check browser console (F12):**
   - Look for error messages
   - Copy error text for troubleshooting

4. **Verify game still works:**
   - Plays in silent mode? Good, audio issue only
   - Doesn't play? Chart issue, not audio

---

## Advanced Tuning

### Fine-Tuning Offset to Perfection

**Goal:** Get < 1ms sync error across entire song

#### Method 1: Audio Playback Analysis
```
1. Enable debug panel
2. Play normally, listen carefully
3. At first note, note if it hits early or late
4. Adjust offset by 5ms increments
5. Restart and repeat
6. Goal: Can't tell if early or late (within 1 frame = 16ms)
```

#### Method 2: Visual Confirmation
```
1. Watch the hit feedback text:
   "Perfect" consistently = Good sync
   Mix of "Perfect" and "Great" = Decent sync
   Mostly "Great" or "Good" = Offset needs adjustment
2. Adjust offset to maximize "Perfect" hits
```

#### Method 3: Latency Accounting
```
Some audio systems have inherent latency (5–50ms)
If offset = 25ms, that's normal and expected
(Computer audio pipeline delay)
```

---

## Reporting Issues to Developer

If you need to adjust offset every time you play, include:

1. **Your System:**
   - Browser: Chrome? Firefox? Safari?
   - OS: Windows? Mac? Linux?
   - Headphones/Speakers type

2. **Your Offset:**
   - Consistent value needed? (e.g., always +35ms)
   - Does it vary? (sometimes +30, sometimes +40)

3. **Your Settings:**
   ```
   BPM: 120
   Offset: +35 (for example)
   Scroll: 400
   Spawn Lead: 1.5
   ```

4. **Reproduction Steps:**
   - "Open game, play first note, hits 35ms early"
   - Include screenshots if relevant

This info helps improve sync detection.

---

## Summary

**Quick Reference:**

| Problem | Parameter | Typical Fix |
|---------|-----------|-------------|
| Notes hit too early | Offset | +20 to +50 ms |
| Notes hit too late | Offset | −20 to −50 ms |
| Notes feel slow | Scroll Speed | Increase 400→500 |
| Notes feel fast | Scroll Speed | Decrease 400→300 |
| Notes pop up suddenly | Spawn Lead | Increase 1.5→2.0 |
| Notes sit too long | Spawn Lead | Decrease 1.5→1.0 |
| Want to test | Autoplay | Enable checkbox |
| Ready to play | Autoplay | Disable checkbox |

**Golden Rule:** After any parameter change, click **"Restart Chart"** to see effect.

---

**Questions?** Check the full README.md or examine the code comments.
