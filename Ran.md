# MapScan - Google Maps Scraper Setup Guide

cd ~/Desktop/mapscan-server/api
npx nest start --watch



এটি একটি পূর্ণাঙ্গ Google Maps, Yandex Maps ও 2GIS স্ক্র্যাপিং টুল। এখানে Chrome এক্সটেনশন ও ব্যাকএন্ড API সার্ভার উভয়ই আছে।

---

## 📁 প্রজেক্ট স্ট্রাকচার

```
~/Desktop/
├── mapscan-extension/     # Chrome এক্সটেনশন (ইতিমধ্যে বিল্ড করা)
└── mapscan-server/        # ব্যাকএন্ড API সার্ভার
    └── api/               # NestJS API সার্ভার
```

---

## 🚀 কিভাবে চালাবেন

### ১. Chrome এক্সটেনশন লোড করুন

```bash
# এক্সটেনশন ফোল্ডারে যান
cd ~/Desktop/mapscan-extension

# অথবা নতুন করে এক্সট্র্যাক্ট করুন
unzip /home/hcker/Downloads/geoleadscraper-main/dist.zip -d ~/Desktop/mapscan-extension
```

**Chrome-এ লোড করুন:**
1. `chrome://extensions` ওপেন করুন
2. **"Developer mode"** চালু করুন
3. **"Load unpacked"** → `~/Desktop/mapscan-extension` ফোল্ডার সিলেক্ট করুন

---

### ২. API সার্ভার চালু করুন

```bash
# সার্ভার ফোল্ডারে যান
cd ~/Desktop/mapscan-server/api

# ডিপেন্ডেন্সি ইনস্টল করুন (প্রথমবার)
npm install

# NestJS CLI ইনস্টল করুন (যদি না থাকে)
npm install -g @nestjs/cli

# সার্ভার চালু করুন (ডেভেলপমেন্ট মোডে)
npx nest start --watch
```

**সফল হলে দেখবেন:**
```
🚀 Server listening on http://0.0.0.0:5050 (development)
```

---

### ৩. এক্সটেনশনে Backend URL সেট করুন

1. Chrome টুলবারে এক্সটেনশন আইকনে ক্লিক করুন
2. **Settings** বা **Configuration** অপশনে যান
3. **Backend URL** দিন: `http://localhost:5050`
4. Save করুন

---

### ৪. স্ক্র্যাপিং শুরু করুন

1. Google Maps-এ যান: `https://www.google.com/maps/search/restaurants+in+Dhaka`
2. এক্সটেনশন আইকনে ক্লিক করুন
3. **"Start Scraping"** বাটনে ক্লিক করুন (অথবা auto-collect চালু করুন)

---

### ৫. API চেক করুন

```bash
# হেলথ চেক
curl http://localhost:5050/v1

# নতুন জব তৈরি করুন
curl -X POST http://localhost:5050/v1/jobs \
  -H "Content-Type: application/json" \
  -d '{"platform":"google_maps","query":"coffee shops in Dhaka","limit":5}'

# সব জব দেখুন
curl http://localhost:5050/v1/jobs
```

---

## ⚠️ সাধারণ সমস্যা ও সমাধান

### ১. `pnpm: command not found`
```bash
# সমাধান:
sudo corepack enable
sudo corepack prepare pnpm@latest --activate
```

### ২. `EADDRINUSE: address already in use :::5050`
```bash
# পোর্ট 5050 ব্যবহার করছে এমন প্রক্রিয়া খুঁজুন
sudo lsof -i :5050

# PID ব্যবহার করে বন্ধ করুন (যেমন: 16250)
sudo kill -9 16250

# আবার চালু করুন
npx nest start --watch
```

### ৩. `Manifest file is missing or unreadable`
- **কারণ:** আপনি API সার্ভারের `dist` ফোল্ডার Chrome-এ লোড করার চেষ্টা করছেন
- **সমাধান:** এক্সটেনশনের `dist` ফোল্ডার লোড করুন, যেখানে `manifest.json` আছে

### ৪. TypeScript Error: `rateLimit is not assignable`
```bash
# সমাধান: src/main.ts-এ এই লাইন পরিবর্তন করুন
# আগে: await app.register(rateLimit, {
# পরে: await app.register(rateLimit as any, {
```

### ৫. `start:dev` স্ক্রিপ্ট না থাকলে
```bash
# NestJS CLI ইনস্টল করুন
npm install -g @nestjs/cli

# সরাসরি চালান
npx nest start --watch
```

### ৬. `packages field missing or empty` এরর
- **কারণ:** আপনি monorepo-র বাইরে `pnpm` চালাচ্ছেন
- **সমাধান:** `npm install` ব্যবহার করুন অথবা রুট ডিরেক্টরি থেকে চালান

### ৭. Permission denied (corepack)
```bash
sudo corepack enable
sudo corepack prepare pnpm@latest --activate
```

---

## 🛠️ দ্রুত কমান্ড রিফ্রেশ

| কাজ | কমান্ড |
|-----|--------|
| সার্ভার চালু | `cd ~/Desktop/mapscan-server/api && npx nest start --watch` |
| সার্ভার বন্ধ | `Ctrl+C` অথবা `sudo kill -9 $(sudo lsof -t -i:5050)` |
| পোর্ট চেক | `sudo lsof -i :5050` |
| এক্সটেনশন রিলোড | `chrome://extensions` → রিলোড আইকন |
| API চেক | `curl http://localhost:5050/v1` |

---

## 📌 গুরুত্বপূর্ণ টিপস

1. **সার্ভার চালু রাখুন:** যতক্ষণ স্ক্র্যাপ করবেন, টার্মিনাল খোলা রাখুন
2. **এক্সটেনশন সক্রিয় রাখুন:** Chrome-এ এক্সটেনশন ইন্সটল করা থাকতে হবে
3. **সঠিক পেজে থাকুন:** Google Maps-এর সার্চ রেজাল্ট পেজে থাকতে হবে
4. **ব্যাকএন্ড URL চেক করুন:** এক্সটেনশনে সঠিক URL সেট করা আছে কিনা
5. **পোর্ট কনফ্লিক্ট:** অন্য কোনো সার্ভার যদি 5050 ব্যবহার করে, তাহলে `.env`-এ পোর্ট পরিবর্তন করুন

---

## 🧹 পরিষ্কার করার কমান্ড

```bash
# সব Node.js প্রক্রিয়া বন্ধ করুন (সাবধান!)
pkill -f node

# পোর্ট 5050 ফোর্স রিলিজ
sudo fuser -k 5050/tcp

# সব প্রক্রিয়া চেক করুন
ps aux | grep node
```

---

## 📞 সহায়তা

যদি কোনো সমস্যা হয়, তাহলে এই ধাপগুলো ফলো করুন:
1. `sudo lsof -i :5050` → পোর্ট চেক করুন
2. `sudo kill -9 <PID>` → পুরনো সার্ভার বন্ধ করুন
3. `cd ~/Desktop/mapscan-server/api && npx nest start --watch` → আবার চালু করুন
4. `curl http://localhost:5050/v1` → চেক করুন

---

## ✅ সফল সেটআপের লক্ষণ

- [x] Chrome এক্সটেনশন লোড হয়েছে
- [x] `curl http://localhost:5050/v1` → `{"status":"ok","service":"geoleadscraper-api"}`
- [x] এক্সটেনশনে Backend URL সেট করা আছে
- [x] Google Maps-এ স্ক্র্যাপিং কাজ করছে

---

**সবকিছু সেটআপ হয়ে গেলে আপনি প্রস্তুত!** 🚀