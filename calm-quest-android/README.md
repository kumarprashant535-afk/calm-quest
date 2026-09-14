# Calm Quest — Android project

This is a complete, ready-to-build Android project for **Calm Quest**, the 999-level
calming matching game. It wraps the game (plain HTML/CSS/JS, no external services) in a
thin native Android shell using [Capacitor](https://capacitorjs.com), with the app icon,
splash screen, name, and package ID already configured.

**Important:** the actual `.apk`/`.aab` file is *not* included here — Android apps are
compiled by Google's own build tools, which this project doesn't bundle (that's normal;
no one ships those). Pick one of the three build paths below and you'll have a real,
installable file in a few minutes.

---

## Option A — GitHub Actions (recommended, no installs)

1. Create a new **public or private** GitHub repo and push this whole folder to it.
2. Go to the repo's **Actions** tab. A workflow called **"Build debug APK"** runs
   automatically on your first push (or run it manually from that tab).
3. When it finishes (~3–5 minutes), open the run and download the **`calm-quest-debug-apk`**
   artifact. Unzip it — that's `app-debug.apk`, ready to install.
4. Send that file to any Android phone (Drive, email, etc.) and open it. The phone will
   ask to allow installing from that source once — that's expected for an app installed
   outside the Play Store.

This gives you a real, shareable APK today. It's a **debug build**, which is perfect for
sharing with friends, family, or coworkers directly — but Play Store submission requires
a *signed release* build, which is Option A's sibling workflow:

### Getting a signed release for the Play Store

The Play Store needs an `.aab` file signed with your own private key (Google will reject
an unsigned or debug-signed one). To do this once:

1. **Create a keystore** (needs a JDK installed locally, e.g. via `brew install openjdk`
   or Android Studio, which bundles one):
   ```
   keytool -genkeypair -v -keystore release.keystore -alias calmquest \
     -keyalg RSA -keysize 2048 -validity 10000
   ```
   Keep the resulting `release.keystore` file and the passwords you set somewhere safe —
   if you lose them you can never update the app again under the same listing.
2. In your GitHub repo, go to **Settings → Secrets and variables → Actions** and add four
   repository secrets:
   - `ANDROID_KEYSTORE_BASE64` — run `base64 -i release.keystore | pbcopy` (Mac) or
     `base64 -w0 release.keystore` (Linux) and paste the output
   - `ANDROID_KEYSTORE_PASSWORD`
   - `ANDROID_KEY_ALIAS` (e.g. `calmquest`)
   - `ANDROID_KEY_PASSWORD`
3. Go to **Actions → "Build signed release AAB (Play Store)" → Run workflow**.
4. Download the `calm-quest-release-aab` artifact — that `app-release.aab` is what you
   upload to Play Console.

## Option B — Android Studio (local, visual)

1. Install [Android Studio](https://developer.android.com/studio) (free).
2. `npm install` in this folder, then `npx cap sync android`.
3. Open the `android/` folder as a project in Android Studio.
4. Use **Build → Generate Signed App Bundle / APK** and follow its prompts (it can create
   a keystore for you the first time).

## Option C — PWABuilder (no code at all)

The `www/` folder is a self-contained, installable web app (it has a manifest and icons
already). If you'd rather not touch Android tooling:

1. Host `www/` somewhere with a public URL — the fastest way is enabling **GitHub Pages**
   on this same repo (Settings → Pages → deploy from the `www` folder). You'll get a
   `https://yourname.github.io/yourrepo/` link in about a minute — which is *also* the
   fastest way to just share the game as a link, playable in any browser, no app needed.
2. Go to [pwabuilder.com](https://www.pwabuilder.com), paste that URL, and use its
   Android package option. It generates a signed APK/AAB for you in the browser.

---

## What's already set up for you

- **App name:** Calm Quest
- **Package ID:** `com.calmquest.app`
- **Version:** 1.0.0 (versionCode 1)
- **Icon:** a soft leaf mark on the game's teal-to-sage brand gradient, generated as a
  proper Android adaptive icon (`android/app/src/main/res/mipmap-*`) plus legacy
  square/round fallbacks for older devices
- **Splash screen:** the same mark, centered on the brand gradient, at all required
  portrait/landscape densities
- **Offline by design:** the packaged game has no external font or network calls and
  needs no `INTERNET` permission — everything runs from bundled assets. Progress saves
  locally on the device.
- **`store-assets/`** — a 512×512 Play Store listing icon and a 1024×500 feature graphic,
  ready to upload in Play Console.

## Play Store submission checklist

A few things Google will ask for that only you can provide:

- **Screenshots** — at least 2 phone screenshots (take them from the running app; the
  home/world-map screen and a level in progress both make good ones).
- **Privacy policy URL** — required even for apps that collect nothing. Since Calm Quest
  stores everything only on-device and calls no servers, a one-paragraph policy saying so
  is sufficient; you'll need to host it somewhere (a GitHub Pages page or a Google Doc
  link both work) and link it in Play Console.
- **Content rating questionnaire** — answer honestly in Play Console; a game like this
  with no ads, violence, or user-generated content should rate very low/all-ages.
- **Data safety form** — you can truthfully declare "no data collected," since there are
  no accounts, no analytics, and no network calls in this build.

## Project layout

```
calm-quest/
├── www/                        the game itself (HTML/CSS/JS + manifest.json + icons)
├── android/                    the native Android project (open this in Android Studio)
├── store-assets/               512×512 icon + 1024×500 feature graphic for Play Console
├── .github/workflows/          the two auto-build pipelines described above
├── capacitor.config.ts         app name / ID / web folder config
└── package.json
```

## Making changes later

If you edit the game, change the file at `www/index.html`, then run:

```
npx cap sync android
```

to copy your changes into the Android project before rebuilding.
