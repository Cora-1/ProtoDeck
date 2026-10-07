# ProtoDeck // Telemetry

A live motorsport-style telemetry console for drone & RC drivetrain testing, plus the
PhishLens forensic scam deconstructor built earlier in the same workspace.

**Live:** https://protodec.web.app

![status](https://img.shields.io/badge/status-live-E6FF00?style=flat-square)
![hosting](https://img.shields.io/badge/hosting-firebase-111111?style=flat-square)

---

## Projects in this repository

| | App | Entry point | Notes |
|---|---|---|---|
| 1 | **ProtoDeck** | [`protodeck.html`](protodeck.html) | The deployed app. Single self-contained file — zero external requests. |
| 2 | **PhishLens** | [`index.html`](index.html) | Forensic scam deconstructor. Tailwind compiled to `assets/styles.css`. |

Both are static, run entirely in the browser, and need no backend.

---

## 1. ProtoDeck

A single HTML file with inline CSS and JS. No bundler, no framework, no CDN — every
byte it needs is in the file, so it also works offline by double-clicking it.

### Panels

| # | Panel | What it does |
|---|---|---|
| 01 | **Chassis Orientation** | CSS-3D gyro rig (rings, horizon, reticle) driven by pitch / yaw / roll, plus tilt and G-force readouts. |
| 02 | **Motor / Thrust** | Four tachometer bars (Front-L, Front-R, Rear-L, Rear-R) with a 14-segment LED strip that turns red at the top of the range, and total thrust in newtons. |
| 03 | **Power / Vitals** | Cell voltage in display type, pack total, current draw, ESC temperature, consumed capacity, sag delta, live current sparkline and a voltage-sag alarm. |
| 04 | **Live Command Terminal** | Streaming colour-coded log with simulated CAN frames, IMU and failsafe events. |
| KPI | **Strip** | Throttle, steering, bank, RSSI (with sparkline), ESC temp and latency, each with a driven meter bar. |

### The tilt link (phone as controller)

On a phone you can **fly the chassis by physically moving the device**: tilt drives
attitude, which drives the motor mix, current draw, G-load and the logs.

1. Tap **`TILT // SIM`** in the Chassis Orientation panel header. On iOS 13+ this
   triggers the motion & orientation permission prompt — it must be a user tap.
2. Move the phone. The first sample becomes "level", so you can hold it however is
   comfortable; tap **`RECENTER`** at any time to re-zero the current pose.
3. Tap the chip again to release and return to the simulated flight model.

Details worth knowing:

- **HTTPS is required** for the sensor APIs — so this works on
  https://protodec.web.app but not on a plain `file://` open.
- If the device has no sensor (any desktop browser), the chip reports
  `TILT // NO SENSOR` and the simulation keeps running. No error, no dead UI.
- If the sensor feed goes silent (screen lock, backgrounded tab) the drive reverts
  to the flight model after ~1.5 s and recovers automatically when data returns.
- Acceleration is read too: shake the phone and you get impact spikes in the log
  (`⚠ 3.4m/s²`) and in the G-force readout.
- Axis feel inverted on your handset? Flip `PITCH_SIGN`, `ROLL_SIGN` or `YAW_SIGN`
  near the top of the `MOTION LINK` block — one character each.

### Running locally

```bash
npm run serve          # http://127.0.0.1:5178/protodeck.html
```

Or just open `protodeck.html` in a browser — it has no build step.

---

## 2. PhishLens

An offline forensic workbench that deconstructs a suspicious message into its
manipulation primitives: sender spoofing, micro-transaction traps, urgency exploits
and redirect risk, with SVG callout lines, an X-ray highlight toggle and a threat
gauge per preset.

```bash
npm run build:css      # compile Tailwind -> assets/styles.css
npm run serve          # http://127.0.0.1:5178/
```

Presets: Package Delivery Scam, Crypto Giveaway Scam, Urgent Bank Fraud. Pasting your
own text runs a local heuristic pass — nothing ever leaves the browser.

---

## Deployment

Hosted on **Firebase Hosting**, project id `protodec` (display name *ProtoDeck*).

```
firebase.json     hosting config: public dir, SPA rewrite, no-cache headers
.firebaserc       binds this directory to the protodec project
scripts/build-public.js   copies protodeck.html -> public/index.html
```

`public/` is a **generated** deploy root and is gitignored — the source of truth is
`protodeck.html`. Always deploy through the npm script so the two can never drift:

```bash
npm run deploy
# == node scripts/build-public.js && firebase deploy --only hosting
```

First time on a new machine:

```bash
npm install -g firebase-tools
firebase login
npm run deploy
```

The build step refuses to run if the source grows an external host reference, so a
deploy can never silently introduce a network dependency.

---

## Repository hygiene

- **No credentials are tracked.** Authentication is handled by the Firebase CLI's own
  browser session on your machine, not by a key file in the repo. `.firebaserc` holds
  only the public project id.
- `node_modules/`, `public/` and the `.firebase/` deploy cache are gitignored.

## License

MIT
