<div align="center">
  
<br>

# H A V E N

**Emergency flood response, rebooted.**

<br>

*When flash floods hit, official alert systems push information one way — slowly. Haven flips that model. It turns every person with a phone into both a sensor and a responder, creating a live, breathing picture of what's actually happening on the ground.*

<br>

`React` · `Node.js` · `Express` · `MongoDB Atlas` · `Gemini AI` · `OpenStreetMap` · `UK Environment Agency API`

---

</div>

<br>

## The problem we're solving

People die in UK flash floods every year. Not because help doesn't exist, but because information doesn't flow fast enough. Roads flood in minutes. Official warnings arrive in hours. Residents have no way to tell each other which streets are passable, which shelters have space, or where someone needs help pulling sandbags.

The emergency broadcast system was designed for a different era. **We rebooted it.**

<br>

## What Haven does

Haven is a live community map for flood emergencies. Open the app and you see what's happening around you — reported floods, blocked roads, power outages — all posted by real people in real time.

**Report what you see.** Drop a pin when you spot flooding, a road closure, or a power outage. Your report instantly appears on everyone's map, tagged with GPS coordinates, a category, and a timestamp.

**Ask for help. Offer help.** Post an "I need help" or "I can help" pin. Suddenly your neighbourhood isn't a collection of strangers — it's a response network.

**Find a safe way out.** Hit the Safe Route button and Haven pulls multiple driving routes from Google Directions, cross-references them against live Environment Agency flood data and community reports, then asks Gemini AI to pick and explain the safest path to the nearest shelter.

**Know where to go.** Browse nearby emergency shelters with capacity information, so you don't arrive at a full building.

<br>

## How it's built

The frontend is a **React** app bundled with **Vite**, rendering an interactive **Leaflet** map on **OpenStreetMap** tiles. Users interact through a report modal, floating action buttons, and a route panel — all designed around the principle that in an emergency, every tap should count.

The backend is **Node.js** with **Express**, connected to **MongoDB Atlas** with geospatial 2dsphere indexing. This means incident queries like "show me everything within 5km" resolve in milliseconds. The shelters endpoint serves capacity data from a curated dataset.

Safe routing is where it gets interesting. Rather than building a complex scoring algorithm from scratch, we take a smarter approach: fetch multiple route alternatives from **Google Directions**, pull real-time flood warnings from the **UK Environment Agency**, gather community-reported incidents from our own database, and hand the whole package to **Gemini AI** with a single question — *which route is safest, and why?* Gemini returns a chosen route and a plain-English explanation.

<br>

## Architecture

```
                        ┌─────────────────────────────┐
                        │       React Frontend         │
                        │   Leaflet · Report Flow · UI │
                        └──────────────┬──────────────┘
                                       │
                                  REST / HTTP
                                       │
                        ┌──────────────▼──────────────┐
                        │      Express API Server      │
                        │                              │
                        │   /api/incidents             │
                        │   /api/shelters              │
                        │   /api/routes/safe           │
                        └───┬──────────┬──────────┬───┘
                            │          │          │
                     ┌──────▼───┐ ┌────▼────┐ ┌───▼──────────┐
                     │ MongoDB  │ │ Gemini  │ │ External     │
                     │  Atlas   │ │   AI    │ │ Google Dirs  │
                     │          │ │         │ │ EA Flood API │
                     └──────────┘ └─────────┘ └──────────────┘
```

<br>

## API at a glance

**Incidents** — the core of community reporting.

```
POST  /api/incidents          Create a report (incident, need_help, or can_help)
GET   /api/incidents          Fetch reports — filter by location, radius, time, type, category
```

Post body expects `type`, `category`, `description`, `lat`, and `lon`. The GET endpoint supports query params for `lat`, `lon`, `radius` (in metres), `sinceMinutes`, `type`, and `category`.

**Shelters** — where to go.

```
GET   /api/shelters           Returns all shelters with location and capacity
```

**Safe routing** — the AI-guided escape route.

```
POST  /api/routes/safe        Send your location, get back ranked routes with an AI explanation
```

Send `{ origin: { lat, lon } }` and receive `{ routes, chosenRouteId, explanation }`.

<br>

## Project structure

```
Haven/
│
├── client/
│   └── src/
│       ├── components/       MapView, ReportModal, SafeRoutePanel, IncidentList
│       ├── hooks/            useIncidents, useUserLocation
│       ├── api/              incidentsApi, routesApi
│       └── styles/           main.css
│
├── server/
│   └── src/
│       ├── models/           Incident.js — Mongoose schema with 2dsphere index
│       ├── routes/           incidents.js, shelters.js, routesSafe.js
│       ├── data/             shelters.json, flood_zones.geojson
│       └── server.js
│
└── docs/                     Architecture notes and team references
```

<br>

## Running it locally

**Server**

```bash
cd server
cp .env.example .env
# Add your MONGODB_URI, GEMINI_API_KEY, and GOOGLE_MAPS_API_KEY
npm install
npm run dev
```

**Client**

```bash
cd client
npm install
npm run dev
```

Frontend runs on `localhost:5173`, API on `localhost:5000`.

<br>

## The team

Three people built this in 21 hours at LeedsHack 2026:

One handled the **Express API and MongoDB** — database design, geospatial queries, endpoint architecture, and integration testing. One owned the **React frontend** — the map interface, report flow, and user experience. One built the **AI routing pipeline** — connecting Gemini, Google Directions, and Environment Agency flood data into a single decision engine.

<br>

## Where this goes next

The 21-hour version is a foundation. The full vision includes flood prediction using historical data and ML, an authority dashboard with live user heatmaps, multi-language support through Gemini translation, push notifications for area-specific flood alerts, and a mobile-native build for offline-first emergency access.

<br>

---

<div align="center">

<br>

**Built at LeedsHack 2026**

Theme: *Systems Rebooted*

*Because when the water rises, information should flow faster.*

<br>

</div>


