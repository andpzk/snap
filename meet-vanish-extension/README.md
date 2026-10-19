# Snap usage guide

Snap replaces your outgoing Google Meet camera image with a saved image of your empty room. Finger-snap detection, a V-sign gesture, and an optional dust effect run locally.

## Install and update

1. Open `chrome://extensions` in Chrome 116 or newer.
2. Enable **Developer mode** and choose **Load unpacked**.
3. Select `meet-vanish-extension`, the folder containing `manifest.json`.
4. Reload Google Meet and grant normal camera/microphone access.

For updates, replace the complete extension folder, including `vendor/` and `models/`, reload the extension, then reload Meet. Update between calls because reloading can interrupt a call. Loading from a new folder may create a separate installation with fresh settings.

## Controls

1. Choose **Capture empty room** in the floating Snap panel.
2. Step out of frame during the three-second countdown, then return. The captured JPEG stays in local Chrome extension storage.
3. Choose **Vanish / Appear** or press **Alt+Shift+V** to switch your outgoing video. Use it again to return to the live camera.
4. Enable **Snap detection** to toggle with a finger snap. Grant microphone access if requested; adjust sensitivity if typing or claps trigger it.
5. Enable **Dust effect** for a roughly 2.2-second person-only particle transition. Reappearing reverses the particle paths; reversing mid-animation reuses the active effect.
6. Enable **V-sign detection**, wait for the local model, and hold your index and middle fingers apart with ring and pinky folded for about 0.7 seconds. Lower your hand for at least half a second before showing V again. A 2.3-second cooldown limits repeated triggers.

Drag the panel to reposition it, collapse it to its round button, and use **Retake** to replace the room image. The toolbar popup enables or disables Snap and links to Google Meet.

## Tips and limitations

- Capture a genuinely empty room and keep camera framing and lighting stable.
- Test your microphone sensitivity in the Meet preview. Other sharp sounds can resemble a finger snap.
- Keep Meet visible and your whole hand in frame for V-sign detection. Either hand works; face your palm generally toward the camera.
- Dust uses local person segmentation, which may miss hair or hands or include room edges. Without a valid mask, the live image stays visible. Without a working GPU renderer, the camera switches instantly.
- During dust, rendering is capped at 1024 x 576 and scaled to the outgoing video size. Browser throttling and device performance affect smoothness; uninterrupted 30 fps is not guaranteed.
- Snap supports Google Meet in Chrome. It is not a system virtual camera and does not support Zoom, Teams, or desktop apps.

## Privacy and camera behavior

- No analytics, remote inference, or runtime downloads. Hand tracking and person segmentation use bundled MediaPipe JavaScript, WASM, and model files. See [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).
- The captured room and preferences remain in local extension storage. Inference samples are transient and are not saved or uploaded by Snap.
- The same-window page bridge is observable by Meet-page scripts and is not a security boundary against the page.
- The outgoing canvas stream keeps the call's original audio tracks. Optional snap detection first tries a suitable existing call audio track; otherwise it requests a separate microphone stream. Disabling detection releases that separate stream.
- V-sign detection and person segmentation sample the raw camera, including while the outgoing image shows the empty room. They do not request an additional camera or microphone.
- Mask sampling pauses in hidden tabs. The shared model document closes when no tab owns detection or the dust effect.
- When all tracked output video tracks and clones stop, Snap releases its raw camera and drawing timers. If camera compositing setup fails, the original camera stream is returned.
- Disabling Snap hides the panel and returns the processed image to live video. Reconnect the camera or rejoin Meet to restore a direct camera stream in an existing call.

## Troubleshooting

- **No panel:** check that the URL is on `meet.google.com`, enable Snap, and reload Meet after installation.
- **No room image:** allow camera access and capture again from the pre-join preview.
- **Frozen or black camera:** toggle Meet's camera off and on or rejoin. Look for `[Snap]` warnings in the page console. For verbose logs, set `DEBUG` to `true` in `main-world.js` and reload the extension and Meet.
- **Shortcut conflict:** change the assignment at `chrome://extensions/shortcuts`.
- **False snap triggers:** lower sensitivity or disable snap detection and use the button or shortcut.

## Development and validation

The main files are `main-world.js` for camera processing and the floating panel, `bridge.js` for storage and page messaging, `background.js` for the shortcut and offscreen lifecycle, and `popup.*` for toolbar controls. `dust-effect.js` and `grain-transport.js` handle particles; `gesture-rules.js`, `gesture-engine.js`, and `gesture-offscreen.html` handle local inference. Bundled dependencies live in `vendor/` and `models/`.

Run `npm test` from the repository root. The mocked tests cover camera lifecycle and clones, unchanged call audio, gesture rules and timing, shared model ownership, dust timing, and safe GPU dispatch. They do not establish physical gesture accuracy, actual WebGL output, production Meet compatibility, or remote participant output. Verify those in a real call.
