# zemer-cast

Prebuilt [FCast](https://fcast.org) sender-SDK native libraries for
[Zemer](https://github.com/ZemerTeam/zemer-app)'s **on-demand cast download**.

Zemer does not bundle the ~5.3 MB `libfcast_sender_sdk.so` in its APK. When a user enables casting in
settings, the app downloads the matching-ABI library from this repo's GitHub **releases**, verifies a
pinned SHA-256, saves it to app-private storage, and `System.load()`s it before initialising the cast
SDK. Users who never cast never pay the download.

## How the libs are produced

[`.github/workflows/build-release.yml`](.github/workflows/build-release.yml) runs entirely in CI:

1. Downloads the official SDK aar from jitpack —
   `org.futo.gitlab.videostreaming.fcast-sdk-jitpack:sender-sdk-minimal:<version>` — the exact artifact
   the app would otherwise bundle.
2. Extracts `jni/<abi>/libfcast_sender_sdk.so` for the two ABIs Zemer ships: `arm64-v8a`,
   `armeabi-v7a`.
3. Computes SHA-256 for each and publishes a release tagged `sdk-<version>` with the libs plus a
   `SHA256SUMS` file.

Nothing is hand-uploaded — every asset is reproducible by re-running the workflow against the same SDK
version, and the `SHA256SUMS` in each release are what the app pins. That pinned checksum (verified
before the library is loaded as native code) is the trust anchor: a tampered download fails the check
and is rejected.

## Cutting a release

- **Manual:** Actions → *build-release* → *Run workflow* → enter the SDK version (default `0.4.0`).
- **By tag:** push a tag `sdk-<version>` (e.g. `sdk-0.4.0`).

## Releases

| Tag | SDK version | ABIs |
|-----|-------------|------|
| `sdk-0.4.0` | 0.4.0 | `arm64-v8a`, `armeabi-v7a` |
