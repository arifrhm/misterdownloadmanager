# Running Mister Download Manager on macOS

Mister Download Manager compiles natively on macOS for both Apple Silicon (`arm64`, M1/M2/M3/M4) and Intel (`x86_64`) Macs.

---

## 1. Downloading Pre-Built Releases (.dmg)

Download the `.dmg` corresponding to your Mac architecture:
- **Apple Silicon:** `MisterDownloadManager-<version>-macos-arm64-experimental.dmg`
- **Intel Mac:** `MisterDownloadManager-<version>-macos-x86_64-experimental.dmg`

Open the `.dmg` and drag **Mister Download Manager** into your `/Applications` folder.

---

## 2. Bypassing macOS Gatekeeper (Unsigned / Ad-hoc Build)

Because community open-source builds are ad-hoc signed without an Apple Developer ID, macOS Gatekeeper will show a warning on first launch (*"Apple could not verify..."* or *"cannot be opened because it is from an unidentified developer"* — see Issue #585).

To open the application:
1. Open **Finder** and navigate to **Applications**.
2. **Right-click (or Control-click)** on **Mister Download Manager** and select **Open**.
3. In the security popup, click **Open** (or go to **System Settings → Privacy & Security** and click **Open Anyway** under the *Security* section).

Alternatively, from the Terminal:
```bash
xattr -cr "/Applications/Mister Download Manager.app"
```

---

## 3. Building From Source on macOS

### Prerequisites
- macOS 11.0 (Big Sur) or newer
- Xcode Command Line Tools:
  ```bash
  xcode-select --install
  ```
- Rust toolchain (`rustup`):
  ```bash
  curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
  ```
- PowerShell (`pwsh`) for extension packing:
  ```bash
  brew install --cask powershell
  ```

### Build Steps

Clone the repository:
```bash
git clone https://github.com/arifrhm/misterdownloadmanager.git
cd misterdownloadmanager
```

#### Run Directly via Cargo (Development Mode):
```bash
cargo run -p mdm-app
```

#### Build Self-Contained `.app` and `.dmg`:
To build for your current architecture:
```bash
# Apple Silicon
bash tools/installer/build-macos.sh --arch arm64

# Intel Mac
bash tools/installer/build-macos.sh --arch x86_64

# Universal (both architectures)
bash tools/installer/build-macos.sh --arch all
```
The resulting `.dmg` and `.dSYM` symbols will be generated in `target/dist/`.

---

## 4. Browser Integration (Safari & Chromium/Firefox)

- **Safari:** The `.app` bundle embeds the native Safari Web Extension (`tools/safari`). To enable unsigned extensions in Safari during development:
  1. Open Safari → **Settings → Advanced** → check **Show features for web developers**.
  2. In the menu bar, open **Develop → Allow Unsigned Extensions**.
  3. Go to Safari **Settings → Extensions** and enable **Mister Download Manager Extension**.
- **Chrome / Brave / Edge:** Load the unpacked extension from `/Applications/Mister Download Manager.app/Contents/Resources/extension-chromium`.
- **Firefox:** Load `/Applications/Mister Download Manager.app/Contents/Resources/mdm-firefox.xpi`.

---

## 5. File & URL Scheme Associations

The macOS `.app` bundle automatically registers:
- **URL Scheme:** `magnet:` (BitTorrent Magnet links)
- **Document Types:** `.torrent` (BitTorrent files)
- **CLI IPC:** Any link passed to `/Applications/Mister\ Download\ Manager.app/Contents/MacOS/misterdownloadmanager "<URL>"` immediately forwards to the running instance via local IPC.
