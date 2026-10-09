# OID4VCgo-wallet-kotlin

`OID4VCWallet`: the Kotlin library of [OID4VCgo](https://github.com/IDFoundry/OID4VCgo)'s
mobile wallet, for Android 8 (API 26) and later. It receives credentials
over OpenID4VCI 1.0 and presents them over OpenID4VP 1.0, under HAIP 1.0,
with SD-JWT VC and ISO mdoc credentials, from links and, through
Credential Manager, from Chrome's Digital Credentials API. OID4VCgo is
OpenID Certified for those roles (over links). It also presents in
person over Bluetooth (ISO/IEC 18013-5), as the holder or as the
reader.

The protocols run in OID4VCgo's Go code, compiled with gomobile into the
library. Your app owns the keys, the storage and the UI:

- **Keys:** `AndroidKeystoreKeyStore` keeps them in StrongBox where the
  device has it, else the TEE. Holder keys ask for the holder's
  fingerprint or screen lock to present (`BiometricPromptAuthenticator`),
  from Android 11 (API 30); below it, the app authenticates the holder
  itself. [What each API level guarantees](https://github.com/IDFoundry/OID4VCgo/blob/main/mobile/README.md#what-each-platform-supports).
- **Storage:** `FileCredentialStore` encrypts each record under a key
  that works only while the device is unlocked, out of backups.
- **Wallet Provider:** you implement `WalletProvider`, which attests the
  wallet.

## Install

Download `oid4vcwallet-0.8.2.aar` from [the release](https://github.com/IDFoundry/OID4VCgo-wallet-kotlin/releases/tag/0.8.2)
(and `oid4vcwallet-0.8.2-sources.jar`, to browse its sources in
Android Studio) into your app module's `libs/`, and add it with the
libraries it uses:

```kotlin
// app/build.gradle.kts
dependencies {
    implementation(files("libs/oid4vcwallet-0.8.2.aar"))
    implementation("androidx.annotation:annotation:1.7.0")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.11.0")
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.11.0")
}
```

Check the download against the release's `SHA256SUMS`
(`sha256sum -c SHA256SUMS`), and its provenance with the GitHub CLI:
`gh attestation verify oid4vcwallet-0.8.2.aar --repo IDFoundry/OID4VCgo`.

In-person presentation needs Bluetooth permissions, which the
library's manifest adds to your app's: request
`ProximityPermissions.holder` or `.reader` at runtime before starting
a session.

The AAR goes in an app module: an Android library can't depend on a
local AAR. It holds the Go library for arm64 and x86_64 (the emulator),
and an app can hold only one gomobile library. It's compiled for Kotlin
2.2 and later.

## Documentation

- [Guides](docs/GettingStarted.md): getting started, receiving
  credentials, presenting from a link, to Chrome and in person,
  handling errors, and going to production.
- [The API reference](https://idfoundry.github.io/OID4VCgo-wallet-kotlin/0.8.2/), for this
  release.
- [The Android library's sources](https://github.com/IDFoundry/OID4VCgo/tree/main/mobile/android/OID4VCWallet),
  mirrored in `sources/`.
- [OID4VCgo's `mobile/`](https://github.com/IDFoundry/OID4VCgo/tree/main/mobile):
  what each platform supports, in-person presentation, the Go side and
  its [ABI](https://github.com/IDFoundry/OID4VCgo/blob/main/mobile/ABI.md).
- [MOBILE.md](https://github.com/IDFoundry/OID4VCgo/blob/main/mobile/MOBILE.md): the design.
- [The demo wallet app](https://github.com/IDFoundry/OID4VCgo/tree/main/mobile/android/DemoWallet):
  a complete app on this library.

## Contributing

This repository is generated: OID4VCgo's `wallet-kotlin-release`
workflow publishes every release here from
[`mobile/android`](https://github.com/IDFoundry/OID4VCgo/tree/main/mobile/android).
Issues and pull requests belong in [OID4VCgo](https://github.com/IDFoundry/OID4VCgo).

## License

MIT. See [LICENSE](LICENSE).
