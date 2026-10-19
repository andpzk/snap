# Snap

Vanish from Google Meet with a snap of your fingers. Snap is a Chrome extension that replaces your outgoing camera image with a captured still of your empty room, with an optional reversible dust effect.

Everything runs on your device. No build step, API keys, account, or hosted backend is required.

## Features

- Capture your empty room and switch between it and your live camera.
- Toggle with a button, **Alt+Shift+V**, a finger snap, or a V-sign gesture.
- Optional person-only dust effect with reverse reappearance.
- Bundled local hand tracking and person segmentation.

## Install

1. Get the source files or extract the extension ZIP.
2. Open `chrome://extensions` in Chrome 116 or newer.
3. Enable **Developer mode**, then choose **Load unpacked**.
4. Select **`meet-vanish-extension`**, the folder containing `manifest.json`.
5. Open or reload Google Meet and allow normal camera/microphone access.
6. In the floating Snap panel, choose **Capture empty room** and step out during the countdown.
7. Return and use **Vanish / Appear** or **Alt+Shift+V**. Enable finger-snap detection, V-sign detection, or the dust effect as wanted.

When updating, replace the complete extension folder, reload the extension, and reload Meet between calls. Loading from a different folder can create a separate Chrome extension installation with its own settings; capture the room again if needed.

See the [usage guide](meet-vanish-extension/README.md) for controls, privacy, and troubleshooting.

## Compatibility

Snap targets **Google Meet in Chrome**. Zoom, Teams, desktop applications, and system virtual cameras are not supported.

Keep the camera and lighting stable. Local segmentation can miss hair or hands and include room edges. During dust, rendering is capped at 1024 x 576 and scaled to the outgoing resolution. Background-tab throttling and device performance can affect smoothness.

Automated tests use mocked browser APIs. Check the result with another participant before relying on it in a call.

## Development

Edit the extension files directly, reload the extension, then reload Meet. Models and runtime files are bundled.

Run the dependency-free regression suite with Node.js 22 or newer:

```sh
npm test
```

In Windows PowerShell, use `npm.cmd test` if the local execution policy blocks `npm.ps1`.

Package the extension with Python 3.9 or newer:

```sh
python3 scripts/package-release.py
```

On Windows, use `py` or `python` if that is your Python command. The ZIP and SHA-256 sidecar are written to `dist/`. The packager checks every extension file against `checksums/extension-v1.7.2.sha256` and refuses missing, changed, or extra files. Review and update the checksum list after deliberate extension changes; increment the version for a new release.

GitHub Actions runs tests, checks JavaScript syntax and file integrity, and produces an extension artifact.

## Privacy

No analytics or remote inference are included. Captured room images and settings stay in local extension storage. Camera samples and silhouettes used for inference are transient. Optional snap detection may request a separate microphone stream if a suitable call track is unavailable.

The page bridge uses same-window messaging and is not a security boundary against Meet-page scripts. Saved Chrome settings and captured room images are not part of this repository.

## Licensing

Bundled dependencies retain their [third-party notices](meet-vanish-extension/THIRD-PARTY-NOTICES.md) and [Apache 2.0 license](meet-vanish-extension/vendor/LICENSE.txt).

No license has been selected for the extension's own code. The bundled dependency license does not license the rest of this project.
