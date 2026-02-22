# QuoteTrend

A Chrome extension that delivers positive AI-style quotes at three times daily—morning, afternoon, and evening—with beautiful, time-specific UI and auto-dismissing popups.

## Single Purpose

QuoteTrend solves one problem: **delivering daily motivation through curated quotes at optimal times**, with visual variety and minimal user interruption. It fetches fresh quotes from a free API, caches them intelligently, and presents them via both popup windows and notifications.

## Description

QuoteTrend cycles through a 7-day motivational pattern, showing three daily quotes (8 AM, 1 PM, 7 PM). Each time period has:
- A dedicated color scheme (golden morning, blue afternoon, purple evening)
- Unique background gradients and typography
- A live digital clock display
- Auto-fetched quotes from Quotable.io API with smart caching
- Notification badges + auto-popup windows

The extension automatically:
1. Tracks installation date to cycle days 1–7
2. Schedules alarms for three time periods daily
3. Shows popup windows that auto-close after 5 seconds
4. Displays system notifications for quick glance
5. Caches quotes per day/time (24-hour expiry) to minimize API calls

## Permissions Justification

### `alarms`
**Purpose**: Schedule three daily alarms (8 AM, 1 PM, 7 PM) to trigger quote delivery.  
**Why Needed**: Chrome extensions require the `alarms` API to create persistent, scheduled events that survive browser restarts. This ensures quotes fire at consistent times without requiring the extension to run continuously.

### `notifications`
**Purpose**: Display system notifications when quotes are triggered.  
**Why Needed**: Notifications provide non-intrusive awareness—users see badges in the system tray even if they're not actively browsing. Paired with popups for full engagement.

### `storage`
**Purpose**: Persist installation date, cached quotes, and storage metadata.  
**Why Needed**: Installation date determines which day (1–7) the user is on for quote cycling. Cached quotes reduce API load and improve performance. Without storage, the extension would lose this state on restart.

### `tabs`
**Purpose**: Enumerate open browser tabs (future use for advanced features).  
**Why Needed**: Allows the extension to interact with or query tab states if needed. Currently optional but included for extensibility (e.g., detecting if a tab is active before showing popups).

### `windows`
**Purpose**: Create new popup windows for quote display.  
**Why Needed**: The `chrome.windows.create()` API requires explicit permission. This allows the extension to spawn independent popup windows that auto-close after 5 seconds, providing a separate visual context for the quote without interrupting the current tab.

### `host_permissions` (`<all_urls>`)
**Purpose**: Fetch quotes from Quotable.io API (https://api.quotable.io/random).  
**Why Needed**: Cross-origin HTTP requests are blocked by default. Host permissions allow the service worker to reach external APIs. Scoped to `<all_urls>` for maximum compatibility; could be narrowed to `https://api.quotable.io/*` if desired.

## Architecture

- **manifest.json**: Defines extension metadata, permissions, and service worker entry point.
- **background.js**: Service worker that manages alarms, notifications, and popup windows.
- **quotes.js**: Core logic for quote fetching, caching, day cycling, and time-of-day detection.
- **popup.html**: UI template with clock, title, subtitle, quote display, and footer.
- **popup.css**: Time-aware styling (morning/afternoon/evening themes).
- **popup.js**: Popup lifecycle—renders quotes, applies styles, updates clock every second, auto-closes if triggered by alarm.

## How to Use

1. **Load the extension**: Open `chrome://extensions`, enable Developer mode, click "Load unpacked", select this folder.
2. **Wait for scheduled times**: The extension triggers at 8 AM, 1 PM, and 7 PM (local time).
3. **View popup & notification**: A popup window appears and auto-closes after 5 seconds; a notification persists in the system tray.
4. **Manual access**: Click the extension icon anytime to open the popup manually (no auto-close).

## Customization

- **Alarm times**: Edit `DAILY_ALARMS` in `background.js` (hours in 24-hour format).
- **Auto-close delay**: Modify `CLEAR_DELAY_MINUTES` in `background.js` for notifications or `setTimeout(5000)` in `popup.js` for popups.
- **Fallback quotes**: Edit `FALLBACK_QUOTES` in `quotes.js` if API is unavailable.
- **Styles**: Customize gradients and colors in `popup.css` under `.morning`, `.afternoon`, `.evening` classes.

## API

**Quotable.io** (https://api.quotable.io/random)  
- Free, no API key required
- Returns random quotes with author attribution
- 24-hour cache per day/time to minimize requests
