# Release & Distribution Strategy

This guide covers how to:
1.  **Host the Website** on GitHub Pages.
2.  **Release the CLI** for macOS (Homebrew), Windows (Scoop), and Linux (Snap/APT).

---

## 1. Hosting the Website on GitHub Pages

Since your website is static (HTML/CSS), GitHub Pages is the perfect free host.

### Option A: The `/docs` Folder Method (Simplest)
1.  Move your website files (`index.html`, `assets/`) into a new folder named `docs` in the root of your repo:
    ```bash
    mkdir docs
    mv web/* docs/
    rm -r web
    ```
2.  Push these changes to GitHub:
    ```bash
    git add .
    git commit -m "Setup GitHub Pages in docs/"
    git push origin main
    ```
3.  Go to your Repository on GitHub -> **Settings** -> **Pages**.
4.  Under **Build and deployment** > **Source**, select **Deploy from a branch**.
5.  Select **main** branch and **`/docs`** folder. Click **Save**.
6.  Your site will be live at `https://dhirajnikam.github.io/nodey/`.

---

## 2. Releasing the CLI Tool (Multi-Platform)

To distribute binaries, we will use **GoReleaser**. It automates building for Mac, Linux, and Windows and creating the Homebrew formula.

### Step 1: Install GoReleaser
```bash
brew install goreleaser/tap/goreleaser
```

### Step 2: Initialize
Run this in your project root:
```bash
goreleaser init
```
This creates a `.goreleaser.yaml` file.

### Step 3: Configure `.goreleaser.yaml`
Replace the contents of `.goreleaser.yaml` with this configuration:

```yaml
project_name: nodey
before:
  hooks:
    - go mod tidy

changes:
  sort: asc
  filters:
    exclude:
      - '^docs:'
      - '^test:'

builds:
  - env:
      - CGO_ENABLED=0
    goos:
      - linux
      - windows
      - darwin
    goarch:
      - amd64
      - arm64

archives:
  - replacements:
      darwin: Darwin
      linux: Linux
      windows: Windows
      386: i386
      amd64: x86_64

checksum:
  name_template: 'checksums.txt'

snapshot:
  name_template: "{{ .Tag }}-next"

changelog:
  sort: asc
  filters:
    exclude:
      - '^docs:'
      - '^test:'

# Homebrew (MacOS)
brews:
  - repository:
      owner: dhirajnikam
      name: homebrew-tap # You need to create this repo!
    
    # The formula name
    name: nodey
    
    homepage: "https://dhirajnikam.github.io/nodey"
    description: "AI-Powered Flowchart Architect for your Terminal"
    license: "MIT"
    
    install: |
      bin.install "nodey"

# Scoop (Windows)
scoops:
  - repository:
      owner: dhirajnikam
      name: scoop-bucket # You need to create this repo!
    homepage: "https://dhirajnikam.github.io/nodey"
    description: "AI-Powered Flowchart Architect for your Terminal"
    license: "MIT"

```

### Step 4: Create Distribution Repositories
You need two new *empty* public repositories on GitHub for the package managers to read:
1.  **`homebrew-tap`**: For MacOS users.
2.  **`scoop-bucket`**: For Windows users.

### Step 5: Push a Tag to Release
GoReleaser runs when you push a new git tag.

1.  Commit your code:
    ```bash
    git add .
    git commit -m "Ready for v1.0.0 release"
    ```
2.  Create a tag:
    ```bash
    git tag -a v1.0.0 -m "First major release"
    ```
3.  Push the tag:
    ```bash
    git push origin v1.0.0
    ```
4.  **Run GoReleaser**:
    You need a `GITHUB_TOKEN` with `repo` permissions.
    ```bash
    export GITHUB_TOKEN="YOUR_GITHUB_TOKEN"
    goreleaser release --clean
    ```

---

## 3. How Users Will Install It

**MacOS (Homebrew):**
```bash
brew tap dhirajnikam/tap
brew install nodey
```

**Windows (Scoop):**
```bash
scoop bucket add dhirajnikam https://github.com/dhirajnikam/scoop-bucket
scoop install nodey
```

**Linux (Curl/Binary):**
Users can download the `.tar.gz` from your GitHub Releases page, or you can supply a simple install script.

```bash
# Linux Install Script Example
curl -L https://github.com/dhirajnikam/nodey/releases/download/v1.0.0/nodey_Linux_x86_64.tar.gz | tar xz
sudo mv nodey /usr/local/bin/
```
