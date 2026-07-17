# DangerRail

Community map for reporting dangerous or poorly maintained guardrails. Anyone can click the map, drop a marker at a guardrail's location, and attach a photo. Reports appear for all users in real time.

**Stack:** single `index.html` — Google Maps JavaScript API + Firebase Firestore (free tier). Photos are compressed in the browser and stored inside Firestore documents, so no paid storage plan is required.

## Quick start

1. Follow [SETUP.md](SETUP.md) to create a free Firebase project and paste its config into `index.html` (~5 minutes).
2. Open `index.html` in a browser to test locally.
3. Deploy free with Firebase Hosting (steps in SETUP.md).

## Usage

Click **+ Report a guardrail** → click the map at the guardrail's location → add a photo → submit. Markers are shared with all users live; click any marker to see its photo and report date.
