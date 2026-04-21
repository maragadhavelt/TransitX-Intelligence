## 1. Problem Statement

Agricultural drone use is growing rapidly in Tamil Nadu for spraying, surveying, and monitoring. However, these flights usually happen in an uncoordinated way:

- Multiple drone operators may fly in the same village or cluster of fields at the same time.
- There is **no shared, real-time view** of low-altitude airspace (0–120 m) for these operators.
- Existing UTM (Unmanned Traffic Management) solutions are too complex, centralized, and connectivity-dependent for small rural operators.
- Operators typically rely only on line-of-sight and phone calls/WhatsApp to avoid each other.
- No simple mechanism exists to **mark and respect no-fly zones** (near schools, temples, transmission lines, etc.) in a hyper-local context.

As a result:

- There is a **high risk of mid-air collisions**, especially during peak times (e.g., fertilizer/pesticide spraying seasons).
- New operators have no easy way to understand who else is flying nearby.
- Safety guidelines are hard to follow in practice without tools that fit local connectivity and literacy constraints.

The root cause: there is **no enforced or incentivized low-altitude airspace management framework** that is simple enough for farmers, small operators, and service providers, and that works reliably in **low-connectivity rural environments**.

---

## 2. Solution Overview – KisanDrone Live Map

**KisanDrone Live Map** is a simple mobile + web app that creates a **hyper-local shared “airspace room”** for agricultural drone operators within a 3–5 km radius.

Core concept:

- Each drone operator runs the app on a smartphone (either:
  - the primary controller phone, or  
  - a phone physically attached to the drone).
- The app continuously shares the device’s **GPS + altitude** with other nearby users.
- Nearby users automatically join a **local “airspace room”** based on proximity.
- A **live map** shows:
  - real-time positions and trajectories of all active drones in a 3–5 km radius
  - **predicted future paths** for basic collision avoidance
  - **no-fly zones** (geo-fenced areas) with alerts when approached or entered.
- The system is designed to work even in **low-connectivity areas**:
  - If internet is weak or absent, one phone acts as a **local hotspot host**, and others connect via Wi‑Fi to share data peer-to-peer or via a local server mode.

For the hackathon prototype:

- Drone positions will be:
  - simulated (via test “virtual drones”), and/or
  - captured using the phone’s own GPS (phone carried or attached to a bike/drone).
- The app will provide:
  - live tracking on the map
  - simple visual + basic collision alerts (e.g., two drones on intersecting paths).

---

## 3. Target Users

### 3.1 Primary Users

1. **Agricultural Drone Operators (Pilots)**
   - Needs:
     - See all other drones flying near their current field (3–5 km).
     - Quickly understand where it is safe to fly.
     - Avoid mid-air collisions without complex systems or high data usage.
     - Use in **Tamil / local language** with minimal text where possible.

2. **Drone Service Providers / Agri-Input Companies**
   - Needs:
     - Coordinate multiple drones working in the same village / cluster.
     - Plan spraying schedules safely across fields.
     - Maintain a log of flights and potential conflicts for accountability.

3. **Farmer Producer Organizations (FPOs) & Cooperatives**
   - Needs:
     - Ensure safe usage of drones across member farms.
     - Ensure no-fly around sensitive local zones (e.g., schools, temples, village ponds, power lines).
     - Build trust with farmers that drone operations are controlled and safe.

### 3.2 Secondary Users (Future)

- Local government agri officers, safety regulators:
  - View heat maps of drone activity.
  - Define / approve **no-fly zones** for specific events or seasons.

---

## 4. Key Features

These features must be implemented and referenced throughout the plan:

1. **Real-time Drone Position Sharing**
   - Each device shares its GPS position, altitude, heading, and speed in near real-time.
   - Operates over:
     - internet (if available), or
     - a **local hotspot network** (Wi‑Fi) when internet is weak.
   - Data is lightweight JSON messages broadcasted to other participants in the airspace room.

2. **Live Map Visualization (3–5 km Radius)**
   - Shows **all active drones** around the user within 3–5 km:
     - as icons with colored trails for recent paths.
   - Supports:
     - pan/zoom
     - basic labels (operator name / drone ID)
     - terrain/satellite background if connectivity allows, else cached/offline map tiles.

3. **No-Fly Zone Alerts**
   - Predefined static and configurable geo-fenced zones (polygons/circles):
     - e.g., schools, hospitals, temples, power lines, village tanks.
   - Overlays on the live map along with drone icons.
   - Triggers alerts when:
     - a drone is **approaching** (e.g., within 100–200 m)
     - a drone **enters** a no-fly zone.
   - Alerts as:
     - color changes on map (red zone overlap)
     - screen flash / popup
     - optional vibration + sound.

4. **Hyper-local Airspace Room**
   - Automatic grouping of nearby operators into a **local “airspace room”**:
     - based on geolocation clustering (e.g., all users within 5 km radius).
   - One user can act as **room host** (especially when offline), using:
     - local hotspot + local room server
   - Other users auto-discover and join the room via:
     - scanning QR code from host
     - or automatic discovery on the same Wi‑Fi subnet.
   - Ensures that **only nearby** drones share data instead of global clutter.

5. **Drone Trajectory Prediction**
   - For each drone:
     - uses current position, speed, heading, and recent history to **predict short-term future path** (e.g., 10–30 sec).
   - Draws a faint projected line ahead of each drone.
   - Computes **potential conflicts**:
     - when two predicted paths intersect within a small time window and distance threshold.
   - Issues **basic collision alerts** to both operators involved.

---

## 5. User Flow

Describe both high-level and detailed steps for the primary flows.

### 5.1 First-Time Setup (Operator)

1. User installs **KisanDrone Live Map** mobile app (Android-focus for hackathon prototype).
2. On first open:
   - Choose language (Tamil / English).
   - Simple onboarding screens explaining:
     - live map
     - no-fly zones
     - “airspace room” concept.
3. User creates a basic profile:
   - Name
   - Drone name/ID (or selects “Simulation Mode” if no real drone)
   - Organization (optional)
4. App requests permissions:
   - GPS location (high accuracy)
   - Background location (if needed)
   - Wi‑Fi / hotspot access (or at least Wi‑Fi state).
5. User chooses usage mode:
   - **Internet mode** (normal: uses server backend)
   - **Local room mode (low connectivity)**:
     - Option A: “Create hotspot room” (be host)
     - Option B: “Join nearby room” (be client)

### 5.2 Joining / Creating a Hyper-local Airspace Room

**Case 1: Good/Moderate Internet (Cloud Room)**

1. User opens app and taps “Start Flight”.
2. App:
   - gets current GPS location
   - queries backend to find matching **airspace room** (clustered by location; e.g., 3–5 km radius).
3. If existing room near the user:
   - user is auto-joined to that room.
4. If no room exists:
   - server creates a new room for that location.
5. User sees:
   - current map centered on their location.
   - list of currently active drones in that room (if any).

**Case 2: Poor/No Internet (Local Hotspot Room)**

**Host:**
1. Host user taps “Create local airspace”.
2. App:
   - turns on mobile hotspot (or instructs user how to do it manually).
   - starts a **local room server**:
     - e.g., using an in-app HTTP/WebSocket server library.
3. App displays:
   - hotspot name / password
   - QR code encoding Wi‑Fi details + local IP.

**Clients:**
1. Second user taps “Join local airspace”.
2. Options:
   - Scan QR code from host’s screen (preferred).
   - Manually connect to given SSID/password in OS settings, then return to app.
3. App auto-discovers local room server via:
   - mDNS / fixed IP / QR data, connects via WebSocket/HTTP.
4. Once connected:
   - both host and clients see each other’s drone positions on the **live map**.

### 5.3 Starting a Flight (Real-time Drone Position Sharing)

1. Once in an airspace room, user taps “Start Flight”.
2. App begins to:
   - Continuously read **GPS + altitude**:
     - Using device GPS and barometer if available.
   - Send position updates at a fixed interval (e.g., every 1–2 seconds) to:
     - Server (cloud) if online, or
     - Local room host if offline.
3. The backend (or host) broadcasts:
   - Each user’s position to all others in that airspace room.
4. All connected apps continuously update:
   - Display of drone markers on the **live map**.

### 5.4 Viewing the Live Map & No-Fly Zones

1. On main map screen, user sees:
   - A map centered on their drone/phone.
   - All other active drones within **3–5 km radius** represented as icons.
   - **Trails** showing the last ~30–60 seconds of movement.
   - **No-fly zones** overlaid as shaded polygons (e.g., red) with labels.
2. User can:
   - pan/zoom the map
   - tap on drone icons to:
     - view operator name
     - distance and relative altitude.
3. If user approaches edge of 5 km radius:
   - app indicates boundary (e.g., faded markers beyond 5 km).

### 5.5 No-Fly Zone Alerts Flow

1. As the drone moves, app continuously:
   - compares current GPS position against no-fly zone polygons.
   - calculates distance to nearest zone boundary.
2. If drone is within a “warning distance” (e.g., 200 m):
   - map shows zone in amber
   - banner: “Approaching no-fly zone: [Name]”
   - optional beep/vibrate.
3. If drone intersects a no-fly polygon:
   - zone flashes red
   - prominent alert: “Inside no-fly zone: [Name] – Adjust course”
   - optional louder sound and vibration.
4. Alerts are logged locally for potential export (future feature).

### 5.6 Drone Trajectory Prediction & Collision Alert Flow

1. For each drone, app/back-end:
   - Maintains a buffer of last N positions with timestamps.
   - Computes:
     - velocity vector (speed + direction).
2. Predicts **future positions** over a short time horizon (e.g., 10–30 seconds) assuming constant velocity or simple smoothing.
3. On the **live map**:
   - draws a faint dashed line ahead of each drone representing **predicted trajectory**.
4. Collision detection:
   - For each pair of drones in a room, algorithm checks if:
     - their predicted paths come within a small distance threshold (e.g., < 15–20 m horizontal and < 10 m vertical)
     - within the same time window (e.g., within 10 seconds).
5. If potential collision detected:
   - both operators see a warning:
     - highlight both drones in red
     - show a “Possible collision in X seconds” banner
     - vibrate/beep.
   - Optional suggestion: “Climb / Descend / Turn Right/Left” (simple text; not autopilot).

### 5.7 Ending a Flight

1. After operation, user taps “End Flight”.
2. App:
   - stops sending GPS updates.
   - removes drone from live map for other users.
   - optionally saves a local log of:
     - track
     - alerts (no-fly / collision warnings).

---

## 6. Technical Approach

Design for a **hackathon-viable prototype** that can be extended later.

### 6.1 Overall Architecture

- **Client Apps:**
  - Mobile: Android (primary; Kotlin/Java or React Native/Flutter).
  - Simple responsive web frontend (React or Next.js) for view-only map/demo.
- **Backend (Cloud mode):**
  - Lightweight real-time server:
    - Node.js + Express + Socket.IO (or NestJS + WebSockets).
  - Data store:
    - In-memory (Map/Redis) for active sessions and rooms.
    - Simple PostgreSQL/SQLite for logging if needed.
- **Local Offline Mode:**
  - On host device:
    - Embedded HTTP + WebSocket server library (e.g., Ktor for Kotlin, Node.js embedded, or a local P2P library).
  - Clients connect via local Wi‑Fi LAN (hotspot).

### 6.2 Data Model (Simplified)

**User**
- `id`
- `name`
- `phone` (optional)
- `organization` (optional)
- `preferredLanguage` (e.g., `ta`, `en`)

**DroneSession**
- `sessionId`
- `userId`
- `roomId`
- `status` (`active`, `ended`)
- `startTime`
- `endTime` (nullable)
- `droneLabel` (e.g., “Sprayer A”)

**Room (Airspace Room)**
- `roomId`
- `centerLat`
- `centerLng`
- `radiusKm` (3–5)
- `mode` (`cloud`, `local`)
- `hostUserId` (for local)
- `createdAt`

**PositionUpdate**
- (Not necessarily stored permanently; can be ephemeral)
- `sessionId`
- `lat`
- `lng`
- `altitudeMeters`
- `timestamp`
- `speed` (m/s) – optional
- `headingDegrees` – optional

**NoFlyZone**
- `id`
- `name`
- `type` (`circle`, `polygon`)
- `centerLat`, `centerLng`, `radiusMeters` (for circle)
- `polygonPoints` (array of lat/lng pairs for polygon)
- `category` (e.g., `school`, `temple`, `powerline`)
- `active` (bool)

### 6.3 APIs / Real-time Channels

**REST APIs (Cloud)**

- `POST /api/register` – create user (simple, no heavy auth for hackathon).
- `POST /api/room/join` – input: `lat`, `lng`, `userId` → output: `roomId`.
- `POST /api/session/start` – input: `userId`, `roomId`, `droneLabel` → output: `sessionId`.
- `POST /api/session/end` – input: `sessionId`.
- `GET /api/noflyzones?lat=&lng=&radius=` – return list for given area.

**WebSocket / Socket.IO Events**

- `join_room` – { `roomId`, `sessionId` }
- `position_update` – { `sessionId`, `lat`, `lng`, `altitude`, `timestamp` }
- `room_state` (broadcast) – { list of live positions + predictive data }
- `collision_alert` – { `sessionId1`, `sessionId2`, `etaSeconds` }
- `nofly_alert` – { `sessionId`, `zoneId`, `type`: `approach`/`enter` }

Local mode can re-use same event shapes; only endpoint changes from remote URL to local host IP.

### 6.4 Real-time Drone Position Sharing Implementation

- On app:
  - Use OS location services with:
    - update interval: 1–2 seconds
    - smallest displacement: e.g., 3–5 m
  - On each update:
    - send `position_update` event via WebSocket.
- On server/local host:
  - Upon receiving `position_update`:
    - store/update last-known position in memory keyed by `sessionId`.
    - broadcast updated positions to all clients in same room (throttled if needed).

### 6.5 Live Map Visualization Implementation

- Use **Map library**:
  - Mobile: Google Maps SDK (Android) or Mapbox (easier offline tiles support).
  - Web: Leaflet.js with OSM tiles (with caching).
- Render:
  - Own drone:
    - distinct icon (e.g., green arrow).
  - Other drones:
    - different colored icons and labels.
  - Maintain short history per drone:
    - array of past coordinates for last N seconds (e.g., 20–30 points).
    - draw line/polyline behind drone.
- Restrict view:
  - Show a circular boundary around user with radius 3–5 km.
  - Optionally fade out drones beyond radius.

### 6.6 No-Fly Zone Alerts Implementation

- Store static no-fly zones server-side (JSON), and sync to clients on room join.
- On client:
  - draw polygons/circles for relevant no-fly zones on map.
  - For each location update:
    - calculate:
      - if inside any zone polygon/circle.
      - nearest distance to zone boundary.
    - If within threshold:
      - trigger local UI alerts as described.
- For offline/local: bundle a local JSON of typical zones or allow manual creation of a few example zones for demo.

### 6.7 Hyper-local Airspace Room Implementation

**Cloud Rooms:**

- When `/room/join` is called:
  - Back-end searches existing rooms:
    - find one with `center` within some max distance (e.g., 2.5 km) of user’s location.
  - If found:
    - return that `roomId`.
  - Else:
    - create new room with `center` = user’s location, set `radius` = 3–5 km.

**Local Rooms:**

- Use an embedded server running on host device:
  - Example for Kotlin: Ktor server bound to `0.0.0.0` on port e.g., 8080.
  - Serve:
    - minimal REST APIs
    - WebSocket endpoint for `position_update` events.
- Client devices:
  - Connect to host’s hotspot (SSID/PW from QR).
  - Use host IP: typically `192.168.43.1` or IP from QR.
  - Same real-time room logic but local.

### 6.8 Drone Trajectory Prediction Implementation

- On server/local host:
  - For each `sessionId`, maintain last N `PositionUpdate` entries (e.g., last 5–10).
  - Compute:
    - Average speed and heading using last few updates.
  - Predict positions at T+2s, T+5s, T+10s:
    - `lat_future = lat + (speed * cos(heading) * t / EarthRadius)`
    - `lng_future = lng + (speed * sin(heading) * t / (EarthRadius * cos(lat)))`
  - Build a small list of predicted coordinates.
- Send to clients:
  - Periodic `room_state` broadcasts include predicted paths per drone.
- On client:
  - Render predicted path as faint dashed polyline ahead of each drone.

**Collision Detection (Server-side for simplicity):**

- For each pair of active sessions in a room:
  - For each prediction time slice (e.g., every 2 seconds up to 10 seconds):
    - compute predicted position for both sessions.
    - compute distance between predicted points.
  - If minimum predicted distance < threshold:
    - raise `collision_alert` event to both sessions.
- Keep complexity low: For hackathon, limit to small number of drones (e.g., < 20).

### 6.9 Low-connectivity & Offline Considerations

- App should:
  - cache map tiles for the surrounding 5 km area when internet is available.
  - gracefully degrade to a simple **grid background** or offline tiles when map tiles unavailable.
- Position sharing:
  - Use only WebSocket or short JSON; no heavy media.
- When connectivity drops:
  - show a banner: “Internet weak – switching to local-only map” (if local room is possible).
  - If no local room:
    - still show **self-position** and last-known positions of others (frozen).

---

## 7. Success Metrics

### 7.1 Functional Metrics (Hackathon Prototype)

1. **Real-time Drone Position Sharing Performance**
   - Position updates visible to other users within:
     - < 2 seconds latency on same local network.
   - Can handle:
     - at least 5–10 concurrent drones in single airspace room without noticeable lag.

2. **Live Map Accuracy**
   - Drones are displayed within:
     - ±10 m of actual GPS location (given consumer device limits).
   - Map correctly shows all active drones within 3–5 km radius.

3. **No-Fly Zone Alerts Reliability**
   - For test routes that intentionally cross known zones:
     - ≥ 90% of entries/exits trigger correct alert.
   - Warning alert appears at least:
     - 3–5 seconds before entering zone (at typical drone speeds).

4. **Hyper-local Airspace Room Robustness**
   - New user within 3–5 km **automatically joins** correct room (cloud mode).
   - In local mode:
     - host + at least 2–3 clients can join same hotspot-based room and see each other’s positions.

5. **Drone Trajectory Prediction & Collision Alerts**
   - In controlled simulations with intersecting paths:
     - potential collision is detected and alerted **≥ 5 seconds before** closest approach.
   - False positive alerts are kept reasonably low in simple, straight-line test flights.

### 7.2 User Experience Metrics (Field Testing / Demo)

- **Time to First Use:**
  - New user can:
    - install app
    - join/create an airspace room
    - start flight
    - see another drone’s position  
    within **5 minutes** without external help.

- **Perceived Ease of Use:**
  - In quick surveys with student testers or local pilots:
    - ≥ 80% say the interface is “easy” or “very easy” to understand.

- **Safety Perception:**
  - After demo:
    - ≥ 80% of operators state that the app would help them avoid other drones and no-fly zones.

### 7.3 Future Impact Metrics (Beyond Hackathon)

- Reduction in:
  - reported near-miss incidents in areas where app is adopted.
- Increase in:
  - number of concurrent drones safely operating in a given village/cluster.
- Adoption:
  - number of active rooms per day in Tamil Nadu villages.
  - number of FPOs and service providers using the system.

---

This plan provides enough detail (feature breakdown, flows, data models, and tech suggestions) for an AI prototyping tool (Lovable, Bolt, etc.) or a small student team to build a working **KisanDrone Live Map** prototype implementing:

- **Real-time Drone Position Sharing**
- **Live Map Visualization (3–5 km radius)**
- **No-Fly Zone Alerts**
- **Hyper-local Airspace Room**
- **Drone Trajectory Prediction & basic collision alerts**