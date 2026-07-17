# DangerRail — Setup Guide

A community map where anyone can drop a marker + photo of a dangerous guardrail.
Stack: one HTML file, Google Maps JavaScript API, Firebase Firestore (free tier).
Photos are compressed in the browser and stored inside Firestore, so **no paid plan is needed**.

## 1. Secure your Google Maps API key (do this first)

Your key is already in `index.html`, but it was shared in plain text, so lock it down:

1. Go to https://console.cloud.google.com/apis/credentials
2. Click your key → **Application restrictions** → *Websites* → add:
   - `http://localhost:*` (for testing)
   - your future hosting URL, e.g. `https://YOUR-PROJECT.web.app/*`
3. **API restrictions** → *Restrict key* → select only **Maps JavaScript API**.
4. Save. (Consider regenerating the key since it was pasted in chat.)

## 2. Create a Firebase project (~5 min)

1. Go to https://console.firebase.google.com → **Add project** → name it (e.g. `dangerrail`). Google Analytics: optional, off is fine.
2. In the project, click the **web icon `</>`** to register a web app (name: `dangerrail`, no hosting checkbox needed yet).
3. Firebase shows a `firebaseConfig` object. Copy it and paste it over the placeholder near the top of `index.html`.

## 3. Enable Firestore

1. In the Firebase console: **Build → Firestore Database → Create database**.
2. Choose a location near you → **Start in production mode**.
3. Go to the **Rules** tab and replace the rules with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /guardrails/{report} {
      allow read: if true;
      allow create: if request.resource.data.keys().hasOnly(['lat','lng','photo','createdAt'])
                    && request.resource.data.lat is number
                    && request.resource.data.lng is number
                    && request.resource.data.photo is string
                    && request.resource.data.photo.size() < 1000000;
      allow update, delete: if false;
    }
  }
}
```

4. **Publish**. This lets anyone read and submit reports, but nobody can edit or delete them from the client (you can delete spam from the Firebase console).

## 4. Test locally

Open `index.html` in a browser (or run `npx serve` in the folder). Click **+ Report a guardrail**, click the map, add a photo, submit. The marker should appear — and on any other open browser too, in real time.

## 5. Deploy (Firebase Hosting, free)

```bash
npm install -g firebase-tools
firebase login
cd <this folder>
firebase init hosting   # select your project, public dir: . , single-page app: No
firebase deploy
```

Your app goes live at `https://YOUR-PROJECT.web.app`. Add that URL to the Maps key restrictions from step 1.

## Notes & limits

- Free Firestore tier: 1 GiB storage, 50k reads/20k writes per day — plenty to start. Each photo report is ~0.3–0.6 MB, so roughly 2,000–3,000 reports fit in the free tier. If it grows, move photos to Firebase Storage (requires Blaze pay-as-you-go plan).
- Reports are anonymous by design. If spam becomes a problem, add Firebase Anonymous Auth + App Check.
- To change the default map view, edit `DEFAULT_CENTER` / `DEFAULT_ZOOM` in `index.html`.
