# YouTube Extended+

Ad-free YouTube for LG webOS TVs, with SponsorBlock, DeArrow and a pile of fixes.

YouTube Extended+ is a fork of [NicholasBly/youtube-webos](https://github.com/NicholasBly/youtube-webos)
(itself a fork of [webosbrew/youtube-webos](https://github.com/webosbrew/youtube-webos)) with these additions on top:

- **[DeArrow](https://dearrow.ajay.app)** - crowdsourced titles and thumbnails instead of clickbait. Its own tab in the settings with the extension's options: replace titles, title formatting (Title Case, Sentence case, ...), remove emojis, replace thumbnails, and what to show without a crowdsourced thumbnail (random frame, YouTube's auto-generated start/middle/end frame, original or blank).
- **SponsorBlock keeps working after switching apps** - coming back to the app used to silently turn SponsorBlock off until the next video.
- **No more ~20 s frozen video after a video screensaver** (e.g. [Aerial](https://github.com/aabytt/webos-custom-screensaver-aerial)) - playback recovers in a couple of seconds.
- Red settings panel and the YouTube Extended+ name.

## Features

Everything from the upstream fork:

- Full support for webOS 3, 4, 5, 6, 22, 23, 24, and 25 (2016 and newer LG TVs) (webOS 1 and 2 currently not supported)
- Ad blocking with a schema-based filtering engine (cleaner Home, Search, and Shorts)
- Filter out QR code + Shop button overlays during video playback
- [SponsorBlock](https://sponsor.ajay.app/) with all segment types, segment colours, a segment list, highlight jumping (🟦 BLUE button), per-segment auto skip / manual skip / progress bar / off, and skip-once
- [DeArrow](https://dearrow.ajay.app) crowdsourced titles and thumbnails
- Return YouTube Dislike in the video description
- Auto Login - bypasses the account selection screen
- Force Max Quality, Force Video Codec, Max Thumbnail Quality
- Hide Endcards, remove Shorts / Live videos / shelves
- Shortcuts - programmable 0-9 key shortcuts during video playback
- Guest Mode: hides "Sign in" prompts
- OLED care: toggle the display off with the 🟥 RED button, true-black UI
- Display Time in UI, multiline titles, video shelf opacity and other UI fixes
- [Autostart](#autostart)

![Segment Skipped](screenshots/2_sm_new.png)

## Pre-requisites

- Official YouTube app needs to be uninstalled before installation.

## Installation

- Use [webOS Homebrew Channel](https://github.com/webosbrew/webos-homebrew-channel) - add this repository in its settings:
  `https://raw.githubusercontent.com/magicsk/youtube-webos/main/repo.json`
  (remove other YouTube AdFree repositories first - they install the same app).
  "YouTube Extended+ (webOS 22+)" is a lighter build for 2022+ TVs (webOS 22-25).
- Use [Device Manager app](https://github.com/webosbrew/dev-manager-desktop) - see [Releases](https://github.com/magicsk/youtube-webos/releases) for prebuilt `.ipk` files (`..._all.ipk` for every TV, `..._all_webOS22+.ipk` for 2022+ TVs).
- Use [webOS TV CLI tools](https://webostv.developer.lge.com/develop/tools/cli-installation) -
  `ares-install youtube...ipk` (For more information on configuring the webOS CLI tools, see [below](#development-tv-setup))

Installing over an existing YouTube AdFree install keeps your settings (it is the same app id).

## Configuration

Configuration screen can be opened by pressing 🟩 GREEN button on the remote.
Black screen / OLED mode can be toggled by pressing 🟥 RED button on the remote.

### Autostart

In order to autostart an application the following command needs to be executed
via SSH or Telnet:

```sh
luna-send-pub -n 1 'luna://com.webos.service.eim/addDevice' '{"appId":"youtube.leanback.v4","pigImage":"","mvpdIcon":""}'
```

This will make "YouTube Extended+" display as an eligible input application (next
to HDMI/Live TV, etc...), and, if it was the last selected input, it will be
automatically launched when turning on the TV.

This will also greatly increase startup performance, since it will be runnning
constantly in the background, at the cost of increased idle memory usage.
(so far, relatively unnoticable in normal usage)

In order to disable autostart run this:

```sh
luna-send-pub -n 1 'luna://com.webos.service.eim/deleteDevice' '{"appId":"youtube.leanback.v4"}'
```

## Building

- Clone the repository

```sh
git clone https://github.com/magicsk/youtube-webos.git
```

- Enter the folder and build the App, this will generate a `*.ipk` file.

```sh
cd youtube-webos

# Install dependencies (need to do this only when updating local repository / package.json is changed)
npm install

npm run build && npm run package
```

## Development TV setup

These instructions use the [webOS CLI tools](https://github.com/webos-tools/cli).
See <https://webostv.developer.lge.com/develop/tools/cli-introduction> for more information.

### Configuring webOS CLI tools with Developer Mode App

This is partially based on <https://webostv.developer.lge.com/develop/getting-started/developer-mode-app>.

- Install Developer Mode app from Content Store
- Enable Developer Mode
- Enable key server and download TV's private key: `http://TV_IP:9991/webos_rsa`  
  The key must be saved under `~/.ssh` (or `%USERPROFILE%\.ssh` on Windows)
- Configure the device using `ares-setup-device` (`-a` may need to be replaced with `-m` if device named `webos` is already configured)
  - `PASSPHRASE` is the 6-character passphrase printed on screen in developer mode app
  - `privatekey` path is relative to `${HOME}/.ssh` (Windows: `%USERPROFILE%\.ssh`)

```sh
ares-setup-device -a webos -i "username=prisoner" -i "privatekey=webos_rsa" -i "passphrase=PASSPHRASE" -i "host=TV_IP" -i "port=9922"
```

### Configuring webOS CLI tools with Homebrew Channel / root

- Enable SSH in Homebrew Channel app
- Generate SSH key on developer machine (`ssh-keygen -t rsa`)
- Copy the private key (`id_rsa`) to the `~/.ssh` directory (or `%USERPROFILE%\.ssh` on Windows) on the local computer
- Append the public key (`id_rsa.pub`) to the `/home/root/.ssh/authorized_keys` file on the TV
- Configure the device using `ares-setup-device` (`-a` may need to be replaced with `-m` if device named `webos` is already configured)
  - `privatekey` path is relative to `${HOME}/.ssh` (Windows: `%USERPROFILE%\.ssh`)

```sh
ares-setup-device -a webos -i "username=root" -i "privatekey=id_rsa" -i "passphrase=SSH_KEY_PASSPHRASE" -i "host=TV_IP" -i "port=22"
```

## Installation

```sh
npm run deploy
```

## Launching

- The app will be available in the TV's app list. You can also launch it using the webOS CLI tools.

```sh
npm run launch
```

To jump immediately into some specific video use:

```sh
npm run launch -- -p '{"contentTarget":"v=F8PGWLvn1mQ"}'
```
