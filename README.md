# Redline Milk — putting it online

This folder is the whole app. It is plain static files, so it runs from any web host with no server code.

## 1 · Make it a website (free, on GitHub Pages)

1. On github.com open the **redline-milk** repo → **Add file → Upload files**, drag in everything in this folder, then **Commit changes**.
2. **Settings → Pages → Build and deployment:** Source *Deploy from a branch*, Branch *main*, folder */ (root)*, **Save**.
3. About a minute later it's live at **https://f0under4.github.io/redline-milk/**. Anyone can open that link.

To update it, upload the new `index.html` the same way. The service worker always fetches the newest version when someone is online, so nobody gets stuck on an old copy.

**Also free:** Netlify Drop (drag the folder onto app.netlify.com/drop), Cloudflare Pages or Vercel. Any of them works. They serve the files over HTTPS, which the app needs for tab sharing, the microphone, MIDI and install-as-an-app. A custom domain (e.g. redlinemilk.com) costs about $10–15 a year and connects in the host's settings.

## 2 · Make it an app

Because it is a Progressive Web App (manifest + service worker included), people can install it straight from the site:

- **Computers (Chrome/Edge):** the install icon in the address bar. It opens full screen and works offline.
- **Android:** Chrome menu → *Install app*.
- **iPhone/iPad:** Safari → Share → *Add to Home Screen*. (Safari can't capture another app's audio; songs, the demo and the mic work.)

To put it in the **app stores**:
- **Google Play and Microsoft Store:** paste your site's link into **PWABuilder** (pwabuilder.com), which packages the PWA for each store. You need a developer account: Google $25 once, Microsoft about $19 once (free for some individuals).
- **Apple App Store (iPhone/iPad/Mac):** wrap the site with **Capacitor** (capacitorjs.com) in Xcode. You need an Apple Developer account ($99 a year) and a Mac.

## 3 · Apple TV

tvOS has no web browser and no web views, so this web app can't run on Apple TV as-is. There are three routes:

1. **Now, with no store listing:** AirPlay a Mac or iPhone screen to the Apple TV, or cast a Chrome tab to any Chromecast/Google TV.
2. **A real tvOS app:** rebuild the renderer natively. **projectM** is the open-source native MilkDrop engine (LGPL) and runs these same `.milk` presets. A Swift/Metal app would use projectM for the visuals and this project's listening logic (kick/snare/hats/bass/vocal, beat clock, drops), ported from `11_listen.js`. It is a real project: a developer for a few weeks, plus the $99/year Apple account.
3. **Android TV / Google TV / Fire TV** run web apps, so the PWA packaged with PWABuilder, or a Trusted Web Activity, gets you onto most smart TVs sooner.

## How it listens (and why there's no delay)

- **Songs you load** are analysed in full in the first second or two, before the visuals need them: every kick, snare and hi-hat, a steady beat grid, the downbeats, breakdowns, the build into each drop, and the drop itself. The visuals are then scheduled against the song's own clock, so each hit lands when the sound reaches you, and a build rises *into* its drop instead of reacting after it.
- **Live sound** (a shared tab such as Spotify, a line-in or a mic) goes to a drum detector on the audio thread (an AudioWorklet). It runs every 2.7 ms whatever the screen's frame rate, and stamps each hit with its exact time. A beat clock locks onto the kicks and shows on-the-beat kicks at the predicted moment. Hits off the grid arrive 10–35 ms after the sound, below what most people notice.
- **Tight sync for a shared tab (Spotify, YouTube…):** live sound can't be heard ahead of time, so Redline makes its own head start. In Chrome or Edge it mutes the shared tab and plays the sound itself about 200 ms later. In that gap it has already heard every kick, snare and hat, so each one is shown exactly when the sound reaches you. Change the hold-back or turn it off in Settings → TIGHT SYNC. A microphone or line-in can't be held back (the sound is already in the room), so those still rely on the beat clock.
- **Telling drums apart:** each hit is judged by how it behaves, not only by its frequency range. A kick's beater click dies in a few milliseconds while a snare or hi-hat keeps ringing. A kick whose pitch sweeps down through the mid range has to prove it's a snare by still ringing 32 ms later. A hat on top of a snare has to fade differently from the snare's own noise.
- **Sync test** (Settings → SYNC TEST): a click plus a flash that is scheduled exactly like the visuals. Line them up by eye to cover your TV's picture delay, or tap along to measure your speakers (Bluetooth adds 150–250 ms). The setting is saved on each device.
- **More than drums:** Redline also hears *chord hits* (several notes struck together), *pads* (chords held underneath), a *synth lead* (a played melody with a steady pitch) and a *vocal* (a sung line, told apart by the wobble a voice puts on a held note), plus every *chord change*.
- **You choose what each instrument does:** FX panel → for every instrument pick up to three effects (punch in, bright flash, colour split, shockwave, sparkle, liquid flow, glow, colour drift, new palette, warm tint, colour pop, or feed the MilkDrop preset's own low/mid/high) and how strong each is. Saved on each device; RESET puts Redline's own mix back.
- **Everything is timed to the sound, not just the drums:** songs play 220 ms after Redline hears them, and every smooth reading (bass, melody, pads, chords, the waveform) is shown at the moment its sound reaches you, with each reader's own reaction time taken off.

## Credits and licences

- MilkDrop presets by the MilkDrop community, rendered by **Butterchurn** (MIT licence). Presets come from `butterchurn-presets` (MIT).
- Lyrics from **LRCLIB** (lrclib.net), a free and open lyrics database.
- Fonts: Michroma and Lexend Zetta (SIL Open Font Licence).

If you charge for it later, keep these notices in the About section. If you add ads, check LRCLIB's terms.
