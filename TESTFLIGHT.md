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
3. Choose **TestFlight & App Store**
4. Use automatic signing
5. Click **Upload** and wait for it to finish

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
2. **Product > Archive** > **Distribute App** > Upload
3. New build appears in App Store Connect automatically
4. Internal testers get it immediately
5. External testers get it after a brief review (usually instant after the first one)

---

## Notes

- Builds expire after **90 days** — testers lose access and need a new build
- You don't need to fill out App Store metadata just for TestFlight
- TestFlight is free with your Apple Developer account
