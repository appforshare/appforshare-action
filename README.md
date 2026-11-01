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

Needs a Pro or Team plan. Create the token in your AppForShare dashboard under Settings, API tokens, and store it
as a repository secret. The runner needs Node 18 or newer (GitHub hosted runners have it).

An .aab is turned into one universal APK that testers can install, signed with your workspace test key or with
your own keystore if you added one under Settings, Android .aab uploads.

| Input | Required | |
|---|---|---|
| `file` | yes | Path to the .ipa, .apk or .aab |
| `token` | yes | AppForShare API token |
| `release-notes` | no | Shown to testers on the install page |
| `channel` | no | Put the build into a channel (`beta`, `live`), created on first use. Its link always opens the newest build in it. |
| `fail-on` | no | `blocked`, or `review` (also fails on blocked): fail the step on the store readiness check. The build stays uploaded and its link works. |
| `api-url` | no | API origin, default `https://appforshare.io` |

| Output | |
|---|---|
| `url` | Install link |
| `slug` | Share code of the build |
| `readiness` | Store readiness status: `ready`, `review`, `blocked` or `unknown` |
| `channel-url` | Stable link of the channel, when `channel` is set |

The store readiness check is guidance about current App Store and Google Play rules, not a promise of approval.
More examples: https://appforshare.io/docs/ci

MIT license.
