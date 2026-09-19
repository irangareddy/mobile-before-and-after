---
name: mobile-before-and-after
description: Capture and compare reproducible before-and-after screenshots or recordings for native iOS and Android apps. Use when a mobile change needs device or simulator evidence, cross-platform parity proof, or media for a pull request. Do not use for browser-only captures.
---

# Mobile before-and-after evidence

Prove the same user scenario before and after a change on every affected mobile platform. Treat screenshots as runtime evidence, not decoration: a visually plausible screen is insufficient when its labels, actions, or entities come from the wrong data.

## Define the proof

Before capturing, identify:

- the behavior or semantic claim being proved;
- the affected platforms: iOS, Android, or both;
- the exact route and interaction sequence;
- the account, fixture, permissions, and network state;
- the build revision and configuration used for each side.

For cross-platform parity, compare meaning before styling. Check the source and presentation of user-visible fields such as organizer or host, venue versus street address, date and timezone, admission or RSVP state, availability, permissions, loading, empty, and error states. Use the product-defined source of truth; do not infer field meaning from visual proximity.

If Web is the reference, inspect its data contract and mapping only to establish expected semantics. The mobile app must still be run to prove its own result.

## Capture a trustworthy baseline

- Prefer the original app build or revision for Before and the changed build or revision for After.
- A user-provided screenshot may serve as Before when the original build is unavailable. Record that provenance.
- Do not recreate Before by adding a debug toggle to the changed code. If a reconstruction is unavoidable, label it simulated and do not present it as historical runtime proof.
- Capture the screen and, when available, the accessibility or UI hierarchy. Use logs or a network trace when the visible result alone cannot prove the data mapping.
- Never include credentials, tokens, private notifications, personal data, or authenticated URLs in media or accompanying files.

## Match the two sides

Within each platform, keep these stable between Before and After:

- device model, OS/API version, orientation, and screen dimensions;
- appearance, text size or font scale, locale, timezone, and accessibility settings;
- account and server fixture;
- permissions, connectivity, navigation path, scroll position, keyboard state, and modal state.

Wait for loading and animation to settle unless motion itself is the subject. Capture the same full screen or the same tightly scoped region. Do not crop one side differently to hide unrelated regressions. iOS and Android do not need identical pixel dimensions to each other; each platform's own Before and After pair must match.

## iOS capture

Use the available device-interaction or iOS runtime workflow when present. Otherwise use the project's normal Xcode build/install path and `xcrun simctl` for Simulator media.

Record the commit SHA, scheme/configuration, bundle identifier, simulator UDID or physical-device model, and OS version. For Simulator screenshots, a typical command is:

```bash
xcrun simctl io <udid> screenshot captures/ios-after.png
```

Use a real signed-in device when the claim depends on Keychain state, push delivery, device-only entitlements, TestFlight/App Store signing, or another environment the Simulator cannot reproduce. State the boundary explicitly; do not claim that a locally signed Simulator build proves TestFlight or production behavior.

## Android capture

Use the project's normal Gradle build/install path and the available emulator/device interaction workflow. Record the commit SHA, build variant, application ID, device serial and model, API level, and active activity.

Typical fallback commands are:

```bash
adb -s <serial> exec-out screencap -p > captures/android-after.png
adb -s <serial> shell uiautomator dump /sdcard/window.xml
adb -s <serial> pull /sdcard/window.xml captures/android-after.xml
```

Use a physical or distribution-signed build when the claim depends on Play signing, Play Integrity, device-only services, production push, or retained secure storage. Do not treat a debug emulator result as proof of an internal-track or production build.

## Name and validate artifacts

Store media inside the relevant repository with paths that contain no whitespace:

```text
captures/ios-before.png
captures/ios-after.png
captures/android-before.png
captures/android-after.png
```

Use `.mp4` or `.mov` for interaction or motion evidence. Before publishing, verify that each pair:

- depicts the same scenario and stable fixture;
- has matching dimensions and orientation within its platform;
- visibly proves the stated change;
- contains no transient overlays or sensitive information;
- is labeled with honest provenance when either side was supplied or reconstructed.

Do not substitute mocked screenshots, design files, or static previews for an actual app run unless the user requested mockups.

## Report the result

Lead with the claim and its proof status. For multiple platforms, use a compact matrix:

| Platform | Before build | After build | Runtime result | Remaining boundary |
|---|---|---|---|---|
| iOS | revision/config | revision/config | what changed | device or release caveat |
| Android | revision/variant | revision/variant | what changed | device or release caveat |

Separate these states when relevant: implementation complete, local build/test passed, simulator/emulator verified, physical-device verified, distribution build verified, and production verified.

When the media should be attached to a GitHub pull request and the `before-and-after` skill is available, load and follow it after capture. Give it one labeled pair per platform; it owns deterministic formatting, upload, marker replacement, and rendered-PR verification. If it is unavailable, keep the captures inside the repository and return a labeled Markdown table for the user to publish. Do not edit, publish, or create a PR unless the user's request includes that action.
