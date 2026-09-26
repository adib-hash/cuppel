# TestFlight Distribution Guide

Quick reference for distributing a Capacitor iOS app via TestFlight.

---

## Prerequisites

- Paid Apple Developer account
- App built and running on a physical device via Xcode
- App icon with **no alpha channel** (App Store Connect rejects transparent icons)

---

## 1. Archive the App

1. In Xcode, set the build target to **Any iOS Device** (not a simulator)
2. Set **Version** and **Build** number in General tab (e.g., 1.0.0 / 1)
3. **Product > Archive**
4. Wait for the Organizer window to open

## 2. Upload to App Store Connect

1. In the Organizer, select your archive
2. Click **Distribute App**
3. Choose **App Store Connect** (Xcode 15+ wording; older Xcode called this
   "TestFlight & App Store"). The other options are wrong for this:
   - *TestFlight Internal Only* — permanently caps the build at internal
     testers; it can never be promoted to external or the App Store
   - *Release Testing* — the old Ad Hoc export; signs for registered device
     UDIDs and uploads nothing
4. Choose **Upload**, not Export
5. Use automatic signing — Xcode re-signs the archive with the distribution
   certificate here, even if it was archived with a development cert
6. Wait for the upload to finish

## 3. Create the App in App Store Connect (first time only)

1. Go to [appstoreconnect.apple.com](https://appstoreconnect.apple.com)
2. **My Apps > + > New App**
3. Fill in: Platform (iOS), Name, Bundle ID, SKU (any unique string)
4. Save — your uploaded build will appear under the **TestFlight** tab

## 4. Add Testers

### Internal Testers (instant access, up to 25)
- **Users and Access** > add people with an App Store Connect role
- They can install immediately — no Apple review needed

### External Testers (up to 10,000)
- **TestFlight** tab > create a group (e.g., "friends&family")
- Add testers by email
- First build requires Apple's automated beta review (~24-48 hours)
- Subsequent builds are usually approved instantly

## 5. Testers Install

1. Tester receives an email invite from TestFlight
2. They install the **TestFlight** app from the App Store (if they don't have it)
3. Open the invite link > tap Install

---

## Updating the App

1. Bump the **Build** number in Xcode (Version can stay the same)
2. **Product > Archive** > **Distribute App** > **App Store Connect** > Upload
3. New build appears in App Store Connect automatically
4. **Answer the export compliance question** on the build in App Store Connect
   — HTTPS-only apps are exempt. The build can't be distributed until you do
5. Internal testers get it immediately
6. External testers get it after a brief review (usually instant after the first one)

---

## Notes

- Builds expire after **90 days** — testers lose access and need a new build.
  There is no way to extend or un-expire a build; the only fix is uploading a
  new one with a higher build number. Existing testers don't need re-inviting
- Claude can produce the signed archive headlessly, no Xcode needed:
  `npm run build && npx cap sync ios`, then `xcodebuild -project
  ios/App/App.xcodeproj -scheme App -configuration Release -destination
  "generic/platform=iOS" -archivePath ~/Library/Developer/Xcode/Archives/$(date
  +%F)/"Cuppel <ver> (<build>).xcarchive" -allowProvisioningUpdates archive`
- Internal testers skip beta review on every build — worth moving regular
  testers to internal (Users and Access) rather than an external group
- You don't need to fill out App Store metadata just for TestFlight
- TestFlight is free with your Apple Developer account
