# Android APK Build Guide — মজা নাও (bild -MJ MORSALIN)

এই প্রজেক্টটি **Capacitor Android** দিয়ে সম্পূর্ণভাবে প্রস্তুত করা হয়েছে, যাতে বর্তমান React + TypeScript + Three.js 3D গেমটি সরাসরি Android APK হিসেবে বিল্ড ও ইনস্টল করা যায়।

## পূর্বশর্ত (Prerequisites)
- **কম্পিউটার ছাড়া (GitHub Actions Cloud Build):** শুধুমাত্র একটি GitHub অ্যাকাউন্ট থাকলেই Android ফোন থেকেই `.github/workflows/android-apk.yml` ব্যবহার করে সরাসরি APK বিল্ড ও ডাউনলোড করা যাবে।
- **কম্পিউটারে লোকাল বিল্ডের জন্য:** Node.js (v22+), Java Development Kit (JDK 21), ও Android Studio (Android SDK 36 সহ)।

## ০. কম্পিউটার ছাড়াই GitHub Actions দিয়ে APK তৈরি করা
১. প্রজেক্টটি আপনার GitHub রিপোজিটরিতে (`main` অথবা `master` ব্রাঞ্চে) আপলোড করুন।
২. GitHub-এ **Actions** ট্যাবে যান এবং **Build Android Debug APK** ওয়ার্কফ্লো সিলেক্ট করুন।
৩. **Run workflow** বাটনে চাপ দিন (অথবা `main` ব্রাঞ্চে কোড পুশ করলেই বিল্ড অটোমেটিক শুরু হবে)।
৪. বিল্ড সবুজ টিক (✔) হয়ে শেষ হলে নিচে **Artifacts** সেকশন থেকে **`moja-nao-android-debug-apk`** ডাউনলোড করে ZIP আনজিপ করুন এবং `moja-nao-debug.apk` ফোনে ইনস্টল করুন।


## ১. ওয়েব বিল্ড ও Android প্রজেক্ট সিঙ্ক করা
প্রজেক্টে কোনো পরিবর্তন করলে প্রথমে ওয়েব অ্যাসেট বিল্ড করে Android প্রজেক্টে সিঙ্ক করুন:

```bash
npm install
npm run android:sync
```

## ২. Android Studio দিয়ে APK তৈরি করা (সবচেয়ে সহজ পদ্ধতি)
১. Android Studio-তে প্রজেক্ট ওপেন করুন:
```bash
npm run android:open
```
২. Android Studio-তে Gradle Sync সম্পন্ন হলে মেনু থেকে নির্বাচন করুন:
   - **Build → Build Bundle(s) / APK(s) → Build APK(s)** (Debug APK-এর জন্য)
   - অথবা **Build → Generate Signed Bundle / APK...** (Play Store বা Release APK-এর জন্য)

## ৩. কমান্ড লাইন (Gradle) দিয়ে সরাসরি APK তৈরি করা

### Debug APK (ফোনে সরাসরি টেস্ট করার জন্য)
```bash
npm run android:apk
```
তৈরি হওয়া APK ফাইলটি পাওয়া যাবে:
```
android/app/build/outputs/apk/debug/app-debug.apk
```

### Release APK (ডিস্ট্রিবিউশনের জন্য)
```bash
npm run android:apk:release
```
তৈরি হওয়া Unsigned Release APK পাওয়া যাবে:
```
android/app/build/outputs/apk/release/app-release-unsigned.apk
```
*(Release APK ফোনে ইনস্টল করতে Android Studio-এর `Generate Signed Bundle / APK` বা `apksigner` ব্যবহার করে Keystore দিয়ে সাইন করে নিন।)*

## ৪. Android-এর জন্য যুক্ত করা সেটিংস
- **App ID:** `com.mjmorsalin.mojanao`
- **App Name:** `মজা নাও`
- **Orientation:** Landscape (`sensorLandscape`) — 3D ড্রাইভিং গেমের জন্য অপ্টিমাইজড।
- **Fullscreen Mode:** Capacitor StatusBar প্লাগইন দিয়ে স্ট্যাটাস বার হাইড করা আছে এবং ডিসপ্লে কাটআউট সাপোর্ট যুক্ত করা হয়েছে।
- **Hardware Acceleration:** WebGL / Three.js 3D পারফরম্যান্সের জন্য চালু রাখা হয়েছে।
- **Hardware Back Button:** Android-এর ব্যাক বাটন চাপলে গেম পজ/রিজিউম (Escape) হিসেবে কাজ করবে।
- **Offline Audio / Voice:** সার্ভার ছাড়া অফলাইনে APK চালালে ভয়েস সিস্টেম স্বয়ংক্রিয়ভাবে Android ডিভাইসের বিল্ট-ইন Speech Synthesis ব্যবহার করবে। চাইলে `.env`-এ `VITE_API_BASE_URL` সেট করে রিমোট TTS সার্ভার ব্যবহার করা যাবে।
