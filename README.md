# MODU/TABLE iOS App — Codemagic Setup Guide

## What's in here
This is a minimal iOS WebView wrapper that loads your published Base44 app URL. Codemagic will build it on their cloud Mac and upload the IPA to App Store Connect.

## Project structure
```
modutable-ios/
├── codemagic.yaml          ← Codemagic build configuration
├── project.yml             ← XcodeGen project definition (generates the .xcodeproj)
├── MODUTABLE/
│   ├── AppDelegate.swift   ← App lifecycle
│   ├── SceneDelegate.swift ← Window setup
│   ├── ViewController.swift← WKWebView that loads your app URL
│   ├── Info.plist          ← App metadata & permissions
│   └── LaunchScreen.storyboard ← Launch screen with MODU/TABLE branding
```

## Step-by-Step Instructions

### 1. Push to GitHub
Create a new GitHub repo (public or private) and push these files to it.

### 2. Create a Codemagic Project
- Go to codemagic.io and sign in
- Click "Add application" → select your GitHub repo
- Choose "iOS App" as the project type

### 3. Set up Apple code signing in Codemagic
In your Codemagic app settings, go to "Code signing → iOS":
- Codemagic can handle automatic signing. You'll need to add your Apple Developer credentials.
- Alternatively, set up App Store Connect API key in Codemagic:
  - Go to App Store Connect → Users and Access → Keys
  - Create a new API key with "App Manager" or "Admin" access
  - Note your **Issuer ID**, **Key ID**, and download the **.p8 private key file**

### 4. Set environment variables in Codemagic
In your Codemagic app settings → Environment variables, add:
- `APP_STORE_ISSUER_ID` — your App Store Connect issuer ID
- `APP_STORE_KEY_ID` — your API key ID
- `APP_STORE_PRIVATE_KEY` — contents of your .p8 file (paste as secure variable)

### 5. Update the App URL
In `ViewController.swift`, the `appURL` is set to:
```
https://app.base44.com/apps/6a610d0885de92d90c0d169a/editor/preview
```
Before submitting to the App Store, change this to your app's **published domain URL** (not the editor preview URL). Apple requires a stable, non-editor URL. You can get this by publishing your app in Base44 and using the custom domain or the published Base44 URL.

### 6. Add an App Icon
You'll need to add an app icon before submitting:
- Create a 1024x1024 PNG icon
- In Codemagic, you can add it as part of the build, or add an `Assets.xcassets` folder with the icon

### 7. Run the Build
Click "Start new build" in Codemagic. The workflow will:
1. Install XcodeGen
2. Generate the Xcode project from project.yml
3. Build the IPA
4. Upload it to App Store Connect

### 8. Submit for Review
Once the IPA is uploaded to App Store Connect:
- Log in to App Store Connect
- Create your app listing (name, description, screenshots, etc.)
- Select the uploaded build
- Submit for Apple's review

## App Store Review Tips
- Apple requires a **privacy policy** page reachable from within the app
- Apple may reject "wrapper" apps if they don't provide native-feeling functionality — make sure your Base44 app looks polished and app-like
- Add a proper app icon and launch screen (already included)
- Make sure your app URL is a stable published URL, not an editor preview link

## Need to update the app URL?
Edit `ViewController.swift` line 10:
```swift
private let appURL = "YOUR_PUBLISHED_URL_HERE"
```
