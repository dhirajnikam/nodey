# 🚀 Final Release Steps

I have configured **GoReleaser** and verified the build locally. Everything is ready.
Since I don't have access to your GitHub account permissions, you just need to run these final commands.

### 1. Create the Tap Repository
Go to GitHub and create a **new public repository** named:
`homebrew-tap`
*(Leave it empty).*

### 2. Push & Tag
Run these commands in your terminal:
```bash
# Push your latest code
git push origin main

# Tag the release
git tag -a v1.0.0 -m "Initial Release"
git push origin v1.0.0
```

### 3. Release!
You need a GitHub Token (Classic) with `repo` scope.
```bash
export GITHUB_TOKEN="YOUR_GITHUB_TOKEN_HERE"
goreleaser release --clean
```

---

That's it! 
Your users can now install Nodey with:
```bash
brew tap dhirajnikam/tap
brew install nodey
```
