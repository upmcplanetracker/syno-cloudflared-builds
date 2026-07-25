# Cloudflared SPK Builder for Synology DS224+

Automated GitHub Actions workflow that builds [Cloudflared](https://github.com/cloudflare/cloudflared) SPK packages for **Synology NAS devices with the `geminilake` architecture** (including the DS224+).

## What It Does

- Checks for new upstream Cloudflared releases **daily at 03:00 UTC**
- Builds a Synology-compatible `.spk` using the [SynoCommunity/spksrc](https://github.com/SynoCommunity/spksrc) framework
- Publishes a GitHub Release with SHA256 checksums
- Skips builds automatically if the upstream version already matches the latest release in this repo

## Supported Devices

| Architecture | Example Devices |
|--------------|-----------------|
| `geminilake` | DS224+, DS423+, DS723+, DVA1622 |

&gt; **Note:** This workflow is specifically tuned for `geminilake`. If you need a different architecture, fork this repo and change the `make arch-geminilake` step.

## Installation

1. Go to the [**Releases**](https://github.com/YOUR_USERNAME/YOUR_REPO/releases) page of this repository.
2. Download the latest `.spk` file.
3. On your Synology NAS, open **Package Center** → **Manual Install**.
4. Select the downloaded `.spk` and complete the installation.
5. Configure Cloudflared via SSH or the package's UI as needed.

## Manual Build

You can trigger a build at any time:

1. Go to the **Actions** tab in this repo.
2. Select **Build Cloudflared SPK (DS224+)**.
3. Click **Run workflow**.

## How It Works

| Step | Description |
|------|-------------|
| `Get latest cloudflared version` | Queries the upstream Cloudflare repo for the newest release tag |
| `Get latest release in THIS fork` | Checks if this repo already released that version |
| `Check if update is needed` | Compares upstream vs. current; skips if matched |
| `Pull SynoCommunity spksrc` | Clones the official build framework |
| `Prepare build environment` | Runs `make setup` inside the container |
| `Cache toolchain` | Caches the `geminilake` toolchain between runs |
| `Update versions in Makefiles` | Patches `PKG_VERS` and `SPK_VERS` to the new release |
| `Update digests` | Regenerates source tarball checksums |
| `Build SPK for geminilake` | Compiles the package |
| `Calculate SHA256 Checksums` | Generates hashes for the built artifacts |
| `Create GitHub Release` | Publishes the `.spk` and checksums |

## Requirements

- A fork of this repository (GitHub Actions must be enabled)
- `contents: write` permission for the default `GITHUB_TOKEN` (already set in the workflow)

## Troubleshooting

- **No release created?** Check the Actions log for the *Check if update is needed* step — it skips if upstream hasn't changed.
- **Build failures?** The `spksrc` container is large; ensure the runner has enough disk space. You can also try re-running failed jobs.
- **Wrong architecture?** This workflow hardcodes `geminilake`. Do not install this SPK on a non-geminilake device.

## License

This workflow configuration is provided as-is. Cloudflared is licensed by Cloudflare, Inc. The SPK build framework is by [SynoCommunity](https://synocommunity.com/).
