# Install Anaglyphohol 4.0.0 from GitHub

Anaglyphohol 4.0.0 is on the [Chrome Web Store](https://chromewebstore.google.com/detail/anaglyphohol/fjbffnhfchidmfcbecccnmdedjahankc) (reinstated October 14, 2026), and it is also published on GitHub. These are the same files that were submitted to the store. Use this page for Edge, Brave, Opera, Vivaldi, Firefox, or if you prefer to install from a folder.

**Download:** [Anaglyphohol 4.0.0 release](https://github.com/LostBeard/AnaglyphoholV2/releases/tag/v4.0.0)

- `Anaglyphohol-4.0.0-chrome.zip`: Chrome, Edge, Brave, Opera, Vivaldi and other Chromium browsers
- `Anaglyphohol-4.0.0-firefox.zip`: Firefox 141 or newer

You need a browser and a graphics card with WebGPU support. The download is about 130 MB because the two depth AI models ship inside the extension.

## Microsoft Edge (recommended)

1. Download `Anaglyphohol-4.0.0-chrome.zip`.
2. Extract it to a folder you will keep, for example `Documents\Anaglyphohol`. The browser loads the extension from this folder every time it starts, so do not delete or move it.
3. Open `edge://extensions`.
4. Turn on **Developer mode** (left side of the page).
5. Click **Load unpacked** and select the folder you extracted (the one that contains `manifest.json`).
6. Anaglyphohol opens its welcome page and prepares its 3D programs for your graphics card. Put on your 3D glasses and open a video.

## Google Chrome, Brave, Opera, Vivaldi

The same steps as Edge, from the extensions page:

1. Download `Anaglyphohol-4.0.0-chrome.zip` and extract it to a folder you will keep.
2. Open `chrome://extensions` (Brave: `brave://extensions`, Opera: `opera://extensions`, Vivaldi: `vivaldi://extensions`).
3. Turn on **Developer mode** (top right in Chrome).
4. Click **Load unpacked** and select the extracted folder (the one that contains `manifest.json`).

If you already have Anaglyphohol from the Chrome Web Store, remove it first so the two copies do not both run on the same pages.

## Firefox

Firefox only installs signed extensions permanently, so there are two ways:

**Try it (until Firefox restarts):**
1. Download `Anaglyphohol-4.0.0-firefox.zip`.
2. Open `about:debugging#/runtime/this-firefox`.
3. Click **Load Temporary Add-on...** and select the zip file.

**Keep it installed (Firefox Developer Edition, Nightly or ESR only):**
1. Open `about:config` and set `xpinstall.signatures.required` to `false`.
2. Rename `Anaglyphohol-4.0.0-firefox.zip` to `Anaglyphohol-4.0.0-firefox.xpi`.
3. Open `about:addons`, click the gear icon, choose **Install Add-on From File...** and select the `.xpi` file.

Regular Firefox ignores `xpinstall.signatures.required`, so use the temporary method there.

## Updates

An extension loaded this way does not update by itself. Watch the [AnaglyphoholV2 releases](https://github.com/LostBeard/AnaglyphoholV2/releases) (on GitHub: **Watch > Custom > Releases**). To update, download the new zip, extract it over the same folder, and click the reload icon on Anaglyphohol's card on the extensions page.

## Build it yourself

The full source is at [LostBeard/AnaglyphoholV2](https://github.com/LostBeard/AnaglyphoholV2). See its [Building](https://github.com/LostBeard/AnaglyphoholV2#building) section.

## Permissions, so you know what you are installing

- `storage`: saves your settings (3D mode, level, focus, which sites have 3D on, toolbar state). Yes, it is used.
- `offscreen` (Chromium browsers): runs the optional Shared 3D converter. Yes, it is used.
- Access to all sites: Anaglyphohol converts the videos and images on whatever page you visit, so it has to run there and read their pixels. Nothing is sent anywhere.

## Support the developer

Anaglyphohol is free and will stay free. If you enjoy it, you can support its development:

[![GitHub Sponsor](https://img.shields.io/github/sponsors/LostBeard?label=Sponsor&logo=GitHub&color=%23fe8e86)](https://github.com/sponsors/LostBeard)
[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=2F6VANCK2EMEY)
