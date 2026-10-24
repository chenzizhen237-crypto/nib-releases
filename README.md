# Nib releases

Downloads for the **Nib** desktop app (macOS, Apple Silicon). Get the `.dmg` from the
[latest release](https://github.com/chenzizhen237-crypto/nib-releases/releases/latest).

The app checks this repository for updates on launch and every 4 hours (Settings ▸ Updates). It
reads `latest-mac.yml` from the latest release and downloads the `.zip` that file names. Source
lives in a separate private repository; this one holds release files and the workflow that builds
them (`.github/workflows/release.yml`), which runs here because Actions minutes are free on public
repositories.
