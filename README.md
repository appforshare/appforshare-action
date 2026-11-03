# AppForShare upload action

Upload an iOS (.ipa) or Android (.apk or .aab) build to [AppForShare](https://appforshare.io) and get an install link and
QR code page for your testers.

```yaml
- uses: appforshare/appforshare-action@v1
  id: afs
  with:
    file: app/build/outputs/apk/release/app-release.apk
    token: ${{ secrets.APPFORSHARE_TOKEN }}
    release-notes: ${{ github.event.head_commit.message }}
    fail-on: blocked
- run: echo "Install: ${{ steps.afs.outputs.url }}"
```

Flutter or React Native app with both platforms? Run `npx appforshare init` once in the repo and commit the
`appforshare.config.json` it writes (where your iOS and Android builds land, and a channel). Then leave `file` out:
one step sends the newest build of each platform.

```yaml
- uses: appforshare/appforshare-action@v1
  id: afs
  with:
    token: ${{ secrets.APPFORSHARE_TOKEN }}
    ios-release-notes: New checkout screen
    android-release-notes: New checkout screen, Android 15 fix
- run: echo "iOS ${{ steps.afs.outputs.ios-url }} Android ${{ steps.afs.outputs.android-url }}"
```

Needs a Pro or Team plan. Create the token in your AppForShare dashboard under Settings, API tokens, and store it
as a repository secret. The runner needs Node 18 or newer (GitHub hosted runners have it).

An .aab is turned into one universal APK that testers can install, signed with your workspace test key or with
your own keystore if you added one under Settings, Android .aab uploads.

| Input | Required | |
|---|---|---|
| `file` | no | Path to the .ipa, .apk or .aab, or a folder (its newest build goes). Empty: the folders in `appforshare.config.json`. |
| `token` | yes | AppForShare API token |
| `release-notes` | no | Shown to testers on the install page |
| `ios-release-notes` | no | Release notes for the iOS build only |
| `android-release-notes` | no | Release notes for the Android build only |
| `channel` | no | Put the build into a channel (`beta`, `live`), created on first use. Its link always opens the newest build in it. |
| `groups` | no | Share the build with these tester groups when it is ready, comma separated (Team). |
| `testers` | no | Share the build with these tester emails when it is ready, comma separated (Team). |
| `email` | no | Email the install link to up to 5 addresses when it is ready, comma separated (Pro and Team). |
| `password` | no | Protect the install link with a password (4 to 128 characters). Pass it from a secret. |
| `fail-on` | no | `blocked`, or `review` (also fails on blocked): fail the step on the store readiness check. The build stays uploaded and its link works. |
| `api-url` | no | API origin, default `https://appforshare.io` |

| Output | |
|---|---|
| `url` | Install link |
| `slug` | Share code of the build |
| `readiness` | Store readiness status: `ready`, `review`, `blocked` or `unknown` |
| `channel-url` | Stable link of the channel, when `channel` is set |
| `ios-url` | Install link of the iOS build, when one run sent both platforms |
| `android-url` | Install link of the Android build, when one run sent both platforms |

The store readiness check is guidance about current App Store and Google Play rules, not a promise of approval.
More examples: https://appforshare.io/docs/ci

MIT license.
