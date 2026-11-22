# Changelog

All notable changes to 8ras Time Tracker are documented here.

## [2.6.0] - 2025-11-22

### Added
- **Real-time timeline for the weekly dashboard**: the day bars now show a detailed timeline, not just contiguous in/out fills.  
  - **Green segments**: time worked inside the workday (e.g., 8:00-18:00), positioned by real time.  
  - **Orange segments**: time worked outside the workday (extra hours), positioned by real time.  
  - **Grey segments**: workday hours not worked (idle time), positioned by real time.  
  - Example: if the workday is 8:00-18:00 and you worked 8:05-10:05, 14:35-16:21, and 17:00-20:00, the bar shows:  
    - 8:00-8:05: grey (workday, not worked)  
    - 8:05-10:05: green (worked inside workday)  
    - 10:05-14:35: grey (workday, not worked)  
    - 14:35-16:21: green (worked inside workday)  
    - 16:21-17:00: grey (workday, not worked)  
    - 17:00-18:00: green (worked inside workday)  
    - 18:00-20:00: orange (worked outside workday)  
  - This gives a clear visual of your workday rhythm, including idle time.
- `dayTimelineSegments()`: calculates all segments (in/out/idle) for a day, positioned by real time.
- `barTimelineHtml()`: renders the segments as spans positioned by real time (24h scale).

### Changed
- **Scale**: the timeline now uses a 24h scale (86400s), not 12h. This allows showing the full day, including extra hours after the workday.
- **Visual hierarchy**: the grey segments (idle workday) are now visible, giving context to the green (worked) and orange (extra) segments.

## [2.5.6] - 2025-11-22

### Fixed
- **Grey workday band now left-aligned**: reverted to left-aligned (left:0) with width proportional to the workday duration (e.g., 8h/12h = 66.67%). Previously (v2.5.5) it was positioned by real time, which caused it to appear centered.
- **Green bars restored**: reverted to the simpler `barFillsHtml()` logic (contiguous in/out bars, left-aligned). The complex `barFragmentsHtml()` from v2.5.5 was removed.
- **Visual hierarchy**: the grey band (workday window) is now a left-aligned background, and the green/orange bars (activity) are left-aligned on top of it. The green bar shows time inside the workday, and the orange bar shows time outside the workday (or weekends).

## [2.5.5] - 2025-11-22

### Changed
- **Bars positioned by real time (24h scale)**: the green/orange activity bars are now positioned by real time on the 24h track, not left-aligned. This means:  
  - The grey workday band (e.g., 10:00-18:00) is positioned at 41.67%-75.00% of the track.
  - Green bars (inside workday) appear where the activity overlaps with the workday window.
  - Orange bars (outside workday, or weekends) appear where the activity is outside the workday window.
  - This makes it visually clear which hours are "extra" (orange) and which are within the configured workday (green).

### Added
- `dayFragmentParts()`: splits raw activity fragments into in/out parts, each with real-time positioning.
- `barFragmentsHtml()`: renders the in/out parts as spans positioned by real time (24h scale).

## [2.5.4] - 2025-11-22

### Changed
- **Track scale reduced to 12h**: the dark track (day background) now represents 12h (43200s) instead of 24h (86400s). This makes the bars more prominent: a full workday (8h) now spans 66.67% of the track instead of 33.33%. The grey workday band and the green/orange activity bars are also scaled to the 12h track.

## [2.5.3] - 2025-11-22

### Changed
- **Bars now scaled to the full 24h track**: the dark track (day background) is always 100% wide. The grey workday band and the green/orange activity bars are scaled proportionally to 24h (86400s), so they are always shorter than the track (unless the workday or activity spans the full 24h). This makes the visual hierarchy clear: the track represents the full day, and the bars represent the subset of time actually worked or configured.

## [2.5.2] - 2025-11-22

### Fixed
- **Grey workday band now left-aligned and scaled**: the band is now left-aligned (default `left:0`) and scaled proportionally to the busiest day, matching the green/orange activity bars. Previously it was positioned by real time (41.67%-75.00% for 10:00-18:00), which created a visual mismatch with the activity bars.

## [2.5.1] - 2025-11-22

### Added
- **Grey workday band**: weekdays (Mo-Fr) now show a grey band (`#565656`) representing the configured work hours (e.g. 10:00-18:00), positioned by real time on the 24h track. Weekends show no band.
- **Tooltips on bars**: hovering over green/orange bar segments shows the exact time (e.g. "1h 05m").
- **Enhanced header**: "Total this week -- {total} ({green} / {orange})" with the total in white, and the in/out split in parentheses.

### Changed
- Bars are now left-aligned (default `left:0`), not centered.
- Workday band uses real-time positioning (10:00-18:00 = 41.67%-75.00% of the track), not proportional to activity.

## [2.5.0] - 2025-11-22

### Added
- **Dashboard colour scheme**: green `#2ecc71` (inside workday), orange `#e09145` (outside), track `#272732`, day label `#8e8e9e`, current day `#4a80f0` bold, right-aligned monospace counter `#b0b0c0` -- all matching the reference design.
- **Dashboard times in hours + minutes only** (no seconds): "1h 05m", "4m", "0m". The live timer still shows full "Hh MMm SSs".

### Changed
- Bar track height increased from 7px to 10px, gap from 6px to 10px.
- Day label width from 17px to 22px.
- Right counter fixed at 68px width.
- Test suite expanded to 87 assertions; the "deterministic GREEN" scenario now uses a weekday date to avoid weekend-always-orange interference.

## [2.4.1] - 2025-11-17

### Fixed
- **Daily bar fills restored to the reference look**: each day's bar is again one contiguous fill scaled proportionally to that day's tracked time (the busiest day spans the full track), with the green (inside-workday) portion followed by the orange (outside) portion -- instead of thin ticks positioned at their time of day, which read as broken.
- **Reliable resume after idle**: the panel now also listens to Photoshop's own `userIdle` events (core API, minversion 23.3). The first event marks the idle start; when the user makes any input again a second event arrives with `{idleEnd:true}` and the session resumes immediately, without waiting for a new history step. This also removes the spurious `toolModalStateChanged` "Unsupported tool id" console errors.

### Changed
- Dashboard caption now reads "bars scaled to the busiest day ..." to describe the proportional scale.
- Test suite expanded from 77 to 84 assertions: core `userIdle` registration on boot and restart, idle-start ignored / `idleEnd` resumes, proportional full-track fill.

## [2.4.0] - 2025-11-17

### Added
- Working hours window in Settings (start and end time, default 10:00-18:00), persisted alongside the idle threshold in `.tracker-token.json`.
- Colour-coded daily bars in the weekly dashboard: green segments fall inside the workday window, orange segments fall outside it; Saturday and Sunday always render orange. The bar axis starts one hour before the workday and extends four hours after it, so overtime at the end of the day has room to show.
- "Total this week -- X / Y" header splitting the weekly total into time inside and outside the workday, each value shown in its colour.

### Changed
- The big timer now shows today's cumulative tracked time for the active file (committed sessions plus the in-flight one) instead of only the live session, so it no longer resets to zero on idle pauses or 60-second checkpoints.
- The "This week" tab now toggles the entire weekly summary on and off.
- Daily bar fills are positioned by time of day (where the work happened) rather than a single width proportional to the total.
- Test suite expanded from 65 to 77 assertions: interval in/out splitting, working-hours persistence, weekend-always-outside rule, timer accumulation, and dashboard toggle.

## [2.3.0] - 2025-11-17

### Fixed
- **Timezone bug**: date keys now use the local calendar date (year-month-day from the system clock) instead of UTC (`toISOString`). Previously, a Tuesday session in Madrid (UTC+1) could land on the Wednesday row when worked between 01:00-06:00 CET. All three test timezones (UTC, Europe/Madrid, America/New_York) now pass.
- **Idle resume after tool change** (v2.2.0 gap): tracking now resumes automatically when the user continues working with the same tool (e.g. brush strokes). The fix polls `app.activeDocument.historyStates.length` every second -- any increase in history stack depth triggers `touchDoc()`, reopening the current session without requiring a document or tool switch.

### Changed
- Weekday labels changed from single letters (M T W T F S S) to two-letter abbreviations (Mo Tu We Th Fr Sa Su).
- Header branding reverted to plain text ("8ras time tracker v2.3.0") -- no logo images.
- Test suite expanded from 46 to 65 assertions, covering timezone correctness and activity-probe resume.

## [2.2.1] - 2025-11-17

### Added
- Activity probe: `historyStates.length` polling in `tick()` to detect undoable steps (including strokes of the currently active tool), enabling automatic resume after idle without switching documents or tools.

## [2.2.0] - 2025-11-17

### Added
- Week starts on Monday (Mo through Su), not Sunday.
- LOG toggle moved inside Settings card (not header).
- "Change folder..." button in Settings to reconfigure the data folder location while preserving existing metrics.json.
- Explicit time units everywhere: "1h 05m 09s", "4m 12s", "38s" (never bare "hh:mm:ss").
- Progress bars scale to fixed 24-hour maximum (86400 seconds), not relative to max day.
- Default view shows timer only; "This week" tab must be pressed to reveal the dashboard.
- Official plugin name set to "8ras time tracker" (all lowercase) across manifest, HTML, and UI.

### Fixed
- Inline event handlers removed entirely: all click/change handlers use `data-action` / `data-change` attributes bound via `addEventListener` after innerHTML injection (UXP forbids inline handlers).
- `uxp.storage.local` fallback added for KV persistence (some PS builds do not expose it); `window.localStorage` used as primary store.

## [2.1.0] - 2025-11-17

### Added
- Complete rewrite abandoning React/Vite. Vanilla JS, separated into `main.js` (logic) and `style.css` (theme).
- Manifest v5 with new ID `com.8ras.time-tracker.v2`.
- Dark theme matching Photoshop's native panels.
- Headless smoke test suite (initially 46 assertions, growing with each release).

### Removed
- All React/Vite build tooling (Vite was incompatible with UXP's Chromium V8 engine).
