# Command Central

A single-page personal dashboard with a live clock, weather, Google Calendar integration, countdowns, todos, moon phase, and a live news ticker. Built as a single self-contained `index.html` file — no build tools, no dependencies, no server required. Deployed via GitHub Pages.

**Live:** [dashboard.mgriffioen.com](https://dashboard.mgriffioen.com)

---

## Features

### 🕐 Clock
Live time display with hours, minutes, seconds, full date, and day of week. Updates every second. Format adapts per theme (e.g. numeric date in Akira mode, roman-style in Bass mode).

### 🌤 Weather
Current conditions plus a 7-day forecast strip. Pulls from the free [Open-Meteo API](https://open-meteo.com/) — no API key required. City name resolved via [Nominatim](https://nominatim.openstreetmap.org/). Location detected automatically via browser geolocation, with a manual lat/lon fallback. Updates every 10 minutes. Coordinates cached in `localStorage`.

### 🌙 Moon Phase
Calculated mathematically — no API needed. Displays a canvas-rendered moon disc showing the correct lit/dark hemisphere for today's phase, phase name (e.g. Waxing Gibbous), and illumination percentage. Moon colors adapt to each theme.

### 📅 Google Calendar
Displays a monthly calendar grid with event dots, plus an upcoming events panel to the right. Connects via OAuth 2.0 using the [Google Identity Services](https://developers.google.com/identity/oauth2/web) library. Requires a Google Cloud OAuth Client ID (see setup below). Shows up to 20 upcoming events. Token is session-only and does not persist across page refreshes.

### ⏱ Countdowns
Add named countdown timers to any future date/time. Each shows days, hours, minutes, and seconds ticking live. Countdowns persist in `localStorage`.

### ✅ Mission Objectives (Todo List)
Simple task list with check-off and delete. Persists in `localStorage`. Shows remaining item count and a "clear completed" action.

### 📰 News Ticker
Scrolling headline bar along the bottom of the screen pulling from **AP News** and **Reuters** RSS feeds via [rss2json.com](https://rss2json.com) (free tier, no key needed). Source label precedes each headline. Pauses on hover. Refreshes every 30 minutes. Speed is configurable (see Customization below).

---

## Themes

The dashboard has four themes, cycling with the toggle button in the top-right corner.

| Theme | Toggle Label | Vibe |
|---|---|---|
| **Retro** | `◈ AKIRA MODE` | Navy & amber, space-age, twinkling starfield, orbit rings |
| **Akira / Cyber** | `◈ TIKI MODE` | Black & orange-red, Neo-Tokyo, falling embers, scanlines, city skyline |
| **Tiki** | `◈ BASS MODE` | Deep mahogany & torchlight orange, fireflies, palm silhouettes, torch flicker |
| **Saul Bass** | `◈ RETRO MODE` | Cream & black, flat print aesthetic, geometric shapes, hard drop shadows |

Theme preference is saved in `localStorage` and restored on next visit.

### Theme Details

**Retro** — the default. Navy background, amber accents, Bebas Neue / Josefin Sans / DM Serif Display typefaces, twinkling starfield, rotating orbit rings.

**Akira / Cyber** — inspired by Neo-Tokyo anime aesthetics. Black background, red-orange accents, Orbitron font throughout, falling ember particles, CRT scanlines overlay, city skyline silhouette, glitch animations on the toggle button.

**Tiki** — warm mid-century tiki bar atmosphere. Deep mahogany background, torchlight orange accents, Pacifico script clock, Josefin Slab body text, Special Elite typewriter mono font, drifting firefly particles with radial glow, palm frond silhouette with tiki torches along the bottom. News ticker relabeled **🌴 COCONUT TELEGRAPH**.

**Saul Bass** — inspired by the graphic title card work of Saul Bass (Vertigo, Psycho, Anatomy of a Murder). Cream/off-white background (the only light theme), bold black and red accents, League Spartan heavy sans-serif, Courier Prime typewriter mono, flat cards with hard 4px drop shadows and sharp corners, bold color-block card headers alternating black/red/gold, geometric SVG shapes in the background. No particles, no glow. News ticker labeled **BULLETIN**. Footer reads: `A COMMAND CENTRAL PRODUCTION · MCMLXI · ALL RIGHTS RESERVED`.

---

## Setup

### 1. Deploy to GitHub Pages

1. Fork or clone this repo
2. Go to **Settings → Pages**
3. Set source to `main` branch, `/ (root)` folder
4. Your dashboard will be live at `https://yourusername.github.io/dashboard`

### 2. Custom Domain (optional)

To use a custom subdomain like `dashboard.yourdomain.com`:

1. In your domain registrar's DNS settings, add a **CNAME record**:
   - Name: `dashboard`
   - Value: `yourusername.github.io`
2. In GitHub Pages settings, set your custom domain
3. Enable **Enforce HTTPS**

### 3. Google Calendar Integration

Google Calendar requires an OAuth 2.0 Client ID. This is a one-time setup.

#### Create the OAuth Client

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (or use an existing one)
3. Navigate to **APIs & Services → Library** and enable the **Google Calendar API**
4. Go to **APIs & Services → OAuth consent screen**
   - Choose **External**
   - Fill in app name, support email, developer email
   - Add the scope: `https://www.googleapis.com/auth/calendar.readonly`
   - Under **Test users**, add your Google account email
5. Go to **APIs & Services → Credentials → Create Credentials → OAuth 2.0 Client ID**
   - Application type: **Web application**
   - Under **Authorized JavaScript origins**, add both:
     - `https://yourusername.github.io`
     - `https://dashboard.yourdomain.com` (if using a custom domain)
   - Click **Create** and copy the Client ID

#### Add the Client ID to the Dashboard

Open `index.html` and find this line near the top of the `<script>` section:

```javascript
const GCAL_CLIENT_ID = 'YOUR_GOOGLE_CLIENT_ID_HERE';
```

Replace the placeholder with your actual Client ID.

#### Using Calendar

1. Open your dashboard
2. Click **⊕ CONNECT GOOGLE CALENDAR** in the Schedule card
3. A Google sign-in popup appears — approve the calendar read permission
4. Events populate automatically

> **Note:** The OAuth token is session-only. You'll need to reconnect after each page refresh until the app is published (moves out of Google's "Testing" mode).

---

## Customization

### News Ticker Speed

Find this line in the `buildTicker` function:

```javascript
const duration = Math.max(60, halfW / 80); // ~80px per second
```

Lower the number to slow it down, raise it to speed it up:
- `80` → default
- `50` → comfortable reading pace  
- `35` → leisurely scroll

### News Sources

The ticker pulls from two RSS feeds defined at the top of the news section:

```javascript
const RSS_FEEDS = [
  { label:'AP',      url:'https://...' },
  { label:'REUTERS', url:'https://...' },
];
```

You can swap in any RSS feed URL. The rss2json proxy handles CORS, so any standard RSS feed will work.

### Weather Units

The dashboard uses **Fahrenheit** and **mph**. To switch to metric, find the `fetchWeather` function and change:
- `temperature_unit=fahrenheit` → `temperature_unit=celsius`
- `wind_speed_unit=mph` → `wind_speed_unit=kmh`

### Adding a Countdown

Click into the **Mission Countdowns** card, type a name, pick a date/time, and press **⊕ ADD COUNTDOWN**. Countdowns survive page refreshes and are stored in `localStorage`.

---

## APIs & Dependencies

Everything is either calculated locally or uses free APIs — no paid services, no API keys required except for Google Calendar.

| Service | Purpose | Key Required |
|---|---|---|
| [Open-Meteo](https://open-meteo.com/) | Weather & forecast | No |
| [Nominatim](https://nominatim.openstreetmap.org/) | Reverse geocoding (city name) | No |
| [Google Calendar API](https://developers.google.com/calendar) | Calendar events | Yes (OAuth Client ID) |
| [Google Identity Services](https://developers.google.com/identity) | OAuth 2.0 auth | Yes (same Client ID) |
| [rss2json.com](https://rss2json.com) | RSS → JSON proxy for news feeds | No |
| Moon phase | Calculated mathematically from known new moon epoch | — |
| [Google Fonts](https://fonts.google.com/) | Bebas Neue, Josefin Sans, DM Serif Display, Orbitron, Pacifico, Special Elite, Josefin Slab, League Spartan, Courier Prime | No |

---

## localStorage Keys

The dashboard stores the following keys in the browser's `localStorage`:

| Key | Contents |
|---|---|
| `dashboard-theme` | Active theme name (`retro`, `cyber`, `tiki`, `bass`) |
| `wx_lat` | Cached weather latitude |
| `wx_lon` | Cached weather longitude |
| `todos` | JSON array of todo items |
| `cd_events` | JSON array of countdown events |

Clearing site data in your browser will reset all preferences and saved items.

---

## File Structure

```
dashboard/
└── index.html    # The entire dashboard — HTML, CSS, and JS in one file
└── README.md
```

There are no build steps, no `node_modules`, no bundler. The file can be opened directly in a browser locally, or served from any static host.

---

## Browser Support

Works in all modern browsers (Chrome, Firefox, Safari, Edge). Requires JavaScript enabled. Google Calendar OAuth requires the page to be served over HTTPS — it will not work when opened as a local `file://` URL.
