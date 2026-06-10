[6/10/26 9:50 PM] Post: # 🎮 Glarium Mobile — Claude Code Prompt Plan

> این فایل رو نگه‌دار. هر prompt رو به‌ترتیب به Claude Code بده.
> بعد از هر مرحله صبر کن agent کارش تموم بشه، بعد برو مرحله بعد.

---

## ⚙️ پیش‌نیاز — قبل از شروع

npm install -g @anthropic-ai/claude-code
git clone https://github.com/verza22/glarium
cd glarium
claude
---

## 📋 مرحله ۱ — تحلیل و ممیزی پروژه

### Prompt 1.1 — بررسی کامل کدبیس

Please do a full audit of this project (Glarium - an Ikariam MMORTS clone).

I need you to:
1. Read and understand the entire folder structure (backend/, frontend/, shared/)
2. List all frontend pages/routes and their current state
3. List all backend API endpoints
4. Identify all outdated dependencies in both frontend and backend package.json
5. Identify any TypeScript errors or major code smells
6. Check if there's any existing mobile responsiveness

Output a detailed report as AUDIT.md in the root folder with sections:
- Project Structure Overview
- Frontend Pages & Components
- Backend API Endpoints
- Outdated Dependencies (with current vs latest versions)
- Issues Found
- Mobile Readiness Score (0-10)
### Prompt 1.2 — آپدیت dependencies

Based on the AUDIT.md you created:

1. Update ALL dependencies in frontend/package.json to their latest stable versions
2. Update ALL dependencies in backend/package.json to their latest stable versions
3. Fix any breaking changes caused by the updates
4. Make sure the project still builds successfully after updates

Run the build after each package update to catch errors early.
Do NOT upgrade React to a version higher than 19.
---

## 📋 مرحله ۲ — موبایل‌سازی Frontend

### Prompt 2.1 — نصب و تنظیم Capacitor

I want to turn the existing React 19 frontend into an Android APK using Capacitor.

Please:
1. Install Capacitor in the frontend/ directory:
   npm install @capacitor/core @capacitor/cli @capacitor/android
2. Initialize Capacitor with these settings:
   - App name: Glarium
   - App ID: com.glarium.game
   - Web dir: dist (or build, check what Vite uses)
3. Add the Android platform: npx cap add android
4. Create capacitor.config.ts with proper server URL pointing to the backend
5. Update frontend/package.json scripts to add:
   - "build:android": "vite build && npx cap sync android"
   - "open:android": "npx cap open android"
6. Make sure vite.config.ts is compatible with Capacitor

Document everything you did in CAPACITOR_SETUP.md
### Prompt 2.2 — سیستم طراحی موبایل

Create a mobile-first design system for the Glarium frontend.

In frontend/src/styles/ create:

1. mobile-variables.css — CSS variables for:
   - Touch target minimum size: 44px
   - Safe area insets (for notched phones): env(safe-area-inset-*)
   - Mobile font sizes (base 16px, scale up)
   - Mobile spacing scale (4, 8, 12, 16, 24, 32px)
   - Mobile color palette (keep existing game colors)

2. mobile-base.css — global mobile styles:
   - Remove hover-only interactions
   - Add touch-action: manipulation to all buttons
   - Prevent text selection on UI elements
   - Smooth scrolling
   - Prevent iOS bounce scroll where needed
   - Fix 100vh issue on mobile browsers (use dvh)

3. mobile-components.css — reusable mobile component styles:
   - .btn-touch (large touchable button)
   - .modal-mobile (full-screen modal for mobile)
   - .card-mobile (game card component)
   - .bottom-nav (bottom navigation bar)
   - .top-bar (mobile top status bar)

Import all these in the main index.css or App.tsx
### Prompt 2.3 — responsive کردن Layout اصلی

`
Make the main game layout fully responsive for mobile (375px to 768px screens).

Look at the current App.tsx and main layout components.

Changes needed:
1. Replace any fixed pixel widths with fluid/responsive values
2. Convert desktop sidebars to:
[6/10/26 9:50 PM] Post: - Collapsible drawer menu on mobile (hamburger icon)
   - Bottom navigation bar with 4-5 main game sections
3. Make all modals/popups full-screen on mobile
4. Fix the game map/world view to work with touch (pinch-zoom, drag)
5. Add viewport meta tag if missing: 
   <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
6. Add splash screen support for Capacitor

Test each breakpoint: 375px (iPhone SE), 390px (iPhone 14), 412px (Pixel 7), 768px (tablet)

### Prompt 2.4 — touch-friendly کردن تمام components

Go through EVERY component in frontend/src/components/ and make them touch-friendly.

For each component:
1. Replace hover states with active/focus states
2. Increase clickable areas to minimum 44x44px (use padding, not size)
3. Add touch feedback (CSS active state with slight scale or opacity change)
4. Convert any right-click menus to long-press menus
5. Fix any overflow issues that cause horizontal scroll
6. Replace desktop tooltips with tap-to-show info panels

Specific game UI elements:
- Building buttons: make them grid-based, large icons with labels below
- Resource counters: pin to top bar, always visible
- Army/troop selectors: use stepper buttons (+/-) instead of input fields
- Map navigation: add touch drag and pinch-to-zoom
- Modals: add swipe-down-to-close gesture

Create a MOBILE_COMPONENTS.md documenting what was changed.

### Prompt 2.5 — صفحه‌های اصلی بازی

Redesign these specific game screens for mobile (one by one):

1. City View (شهر)
- Buildings displayed in a scrollable grid (2-3 columns)
- Each building card shows: icon, name, level, upgrade button
- Bottom sheet for building details instead of modal
- Sticky resource bar at top

2. Island View (جزیره)
- Horizontal scrollable island map
- Player cities shown as pins
- Tap on city = show city info card from bottom

3. World Map (نقشه جهان)
- Zoomable/pannable canvas or SVG map
- Search bar at top to find islands
- Floating action button for "go to my island"

4. Research Screen (تحقیقات)
- Vertical scrollable tech tree
- Each research item as a card
- Progress bar + time remaining clearly visible

5. Messages Screen (پیام‌ها)
- Standard mobile messaging UI
- Conversation list → conversation detail
- Compose button (FAB) at bottom right

For each screen, keep the existing React component logic but completely redo the JSX/CSS for mobile.

---

## 📋 مرحله ۳ — بهبود Performance و Backend

### Prompt 3.1 — بهبود API و WebSocket

Optimize the backend for mobile clients (slower connections, battery constraints).

1. API Optimization:
   - Add response compression (install compression middleware)
   - Add proper Cache-Control headers to static/infrequently-changing endpoints
   - Add rate limiting (install express-rate-limit) 
   - Paginate any endpoint returning lists > 20 items
   - Add a /api/health endpoint

2. WebSocket Optimization (real-time events):
   - Review current WebSocket/Socket.io implementation
   - Add reconnection logic with exponential backoff
   - Add heartbeat/ping-pong to detect disconnections
   - Queue events when client is briefly disconnected, resend on reconnect
   - Minimize payload size (send only changed data, not full objects)

3. New Mobile-Specific Endpoint:
   - GET /api/mobile/dashboard
   - Returns in ONE request: player info, resources, active buildings, active troops, notifications count
   - This reduces the number of API calls on app startup

Document all changes in BACKEND_OPTIMIZATIONS.md

### Prompt 3.2 — سیستم Offline و Cache

Add offline support and smart caching to the frontend.

1. Service Worker (via Vite PWA plugin):
   Install: npm install vite-plugin-pwa
   Configure to cache:
   - All game assets (images, icons, fonts)
   - Static game data (building definitions, research tree)
   - Last known game state
[6/10/26 9:50 PM] Post: 2. React Query / TanStack Query for API caching:
   Install: npm install @tanstack/react-query
   - Wrap all API calls with useQuery
   - Set staleTime: 30000 (30 seconds) for game state
   - Set staleTime: 300000 (5 minutes) for static data
   - Show cached data while refetching (no loading spinners on every navigation)

3. Offline UI:
   - Detect network status with navigator.onLine + online/offline events
   - Show a banner when offline: "You're offline — showing last saved data"
   - Disable action buttons (build, attack, etc.) when offline
   - Queue actions that fail and retry when back online

4. Local Storage for session:
   - Cache player token and basic profile
   - Remember last viewed city/island
   - Save UI preferences (map zoom level, etc.)

### Prompt 3.3 — Push Notifications

Add push notifications for mobile using Capacitor.

Install: 
npm install @capacitor/push-notifications @capacitor/local-notifications

Implement notifications for these game events:
1. Building construction complete
2. Research complete  
3. Troops finished training
4. You're being attacked (URGENT - immediate)
5. Resources full (storage capacity reached)
6. New message received
7. Colony ship arrived

Steps:
1. Create frontend/src/services/NotificationService.ts
   - requestPermission() — ask user on first launch
   - scheduleLocal(event) — for timed events (building completes in X minutes)
   - handlePush(payload) — for server-sent urgent notifications

2. In backend, add a simple notification queue:
   - POST /api/notifications/register — save device token
   - Trigger notifications from existing game event handlers

3. Add notification settings screen in the app (allow user to toggle each type)

Make sure notifications work when app is in background (Capacitor handles this).

---

## 📋 مرحله ۴ — Telegram Mini App

### Prompt 4.1 — تنظیم Telegram WebApp

Adapt Glarium to work as a Telegram Mini App.

1. Install Telegram WebApp SDK:
   npm install @twa-dev/sdk
   or use the CDN: <script src="https://telegram.org/js/telegram-web-app.js">

2. Create frontend/src/telegram/ directory with:

   TelegramProvider.tsx — context provider that:
   - Detects if running inside Telegram (window.Telegram?.WebApp exists)
   - Initializes WebApp: Telegram.WebApp.ready()
   - Expands to full screen: Telegram.WebApp.expand()
   - Provides theme colors from Telegram.WebApp.themeParams
   - Provides user data from Telegram.WebApp.initDataUnsafe.user

   useTelegram.ts — hook exposing:
   - isTelegram: boolean
   - tgUser: {id, username, firstName}
   - hapticFeedback(type) — vibration feedback
   - showBackButton() / hideBackButton()
   - showMainButton(text, onClick)
   - closeApp()

3. Modify App.tsx:
   - Wrap with TelegramProvider
   - If isTelegram: use Telegram theme colors instead of custom colors
   - If isTelegram: use Telegram BackButton instead of custom back navigation
   - Hide custom header if inside Telegram (Telegram provides its own)

4. Telegram-specific login:
   - If running in Telegram, skip normal login screen
   - Auto-login using Telegram user ID + initData validation
   - Backend: POST /api/auth/telegram with initData, validate using bot token, return JWT

5. Create a separate build script:
   "build:telegram": "VITE_PLATFORM=telegram vite build --outDir dist-telegram"

Document bot setup instructions in TELEGRAM_SETUP.md

### Prompt 4.2 — Telegram UX Optimizations

Optimize the Telegram Mini App experience specifically.

1. Use Telegram's native components where possible:
   - Telegram.WebApp.showPopup() for simple confirm dialogs
   - Telegram.WebApp.showAlert() for notifications
   - Telegram.WebApp.showConfirm() for destructive actions (attack, demolish)
   - Telegram.WebApp.HapticFeedback for button taps

2. Main Button (bottom Telegram button):
   - On city view: show "Build" button when a building is selected
   - On attack screen: show "Launch Attack" button
   - On research: show "Start Research" button
   - Hide when no primary action is available
[6/10/26 9:50 PM] Post: 3. Back Button:
   - Use Telegram's back button for all navigation
   - Island View → Back → World Map
   - Building Detail → Back → City View
   - etc.

4. Optimize for Telegram's webview constraints:
   - Max height is limited, use scrollable containers
   - Avoid fixed position elements that conflict with Telegram's UI
   - Test keyboard behavior (Telegram adjusts viewport on keyboard open)

5. Share functionality:
   - Add "Share my city" button that uses Telegram.WebApp.switchInlineQuery()
   - Players can share their city stats to Telegram chats

---

## 📋 مرحله ۵ — ساخت APK

### Prompt 5.1 — تنظیمات Android

Prepare the Android build for Glarium.

1. Update android/app/src/main/AndroidManifest.xml:
   Add permissions:
   - INTERNET
   - VIBRATE (for haptic feedback)
   - RECEIVE_BOOT_COMPLETED (for scheduled notifications)
   - POST_NOTIFICATIONS (Android 13+)
   Add: android:usesCleartextTraffic="true" for development

2. Update app icons:
   Place game icon in these sizes:
   - android/app/src/main/res/mipmap-mdpi/ (48x48)
   - android/app/src/main/res/mipmap-hdpi/ (72x72)
   - android/app/src/main/res/mipmap-xhdpi/ (96x96)
   - android/app/src/main/res/mipmap-xxhdpi/ (144x144)
   - android/app/src/main/res/mipmap-xxxhdpi/ (192x192)
   Create a placeholder icon with "G" letter for now.

3. Splash screen:
   Install: npm install @capacitor/splash-screen
   Configure a game-themed splash screen in capacitor.config.ts:
   - backgroundColor: "#1a1a2e" (dark game theme)
   - showDuration: 2000

4. Update capacitor.config.ts for production:
   - server.url should point to production backend URL
   - Add android.buildOptions

5. Create build instructions file BUILD_ANDROID.md:
   Step by step how to:
   - Build the web assets: npm run build
   - Sync to Android: npx cap sync
   - Open in Android Studio: npx cap open android
   - Build debug APK
   - Build release APK (signing instructions)

### Prompt 5.2 — تست و دیباگ

Set up testing and debugging for the mobile build.

1. Add error boundary for mobile:
   Create frontend/src/components/MobileErrorBoundary.tsx
   - Catches React errors
   - Shows user-friendly error screen (not white screen)
   - "Report bug" button that sends error to backend
   - "Restart game" button

2. Add mobile debugging:
   In development mode only:
   - Install eruda: npm install eruda
   - Show floating debug console on mobile (activated by 5 quick taps on logo)
   - Log all API calls with timing
   - Show current FPS (for map/animation performance)

3. Performance monitoring:
   - Log slow API calls (> 2 seconds) to console
   - Log component render time for main screens
   - Add a simple performance report: /debug/performance in dev mode

4. Create test checklist TEST_CHECKLIST.md:
   - [ ] Login/Register works on mobile
   - [ ] City view loads and scrolls smoothly
   - [ ] Buildings can be tapped and upgraded
   - [ ] Map zooms and pans with touch
   - [ ] Notifications received when app is closed
   - [ ] Works offline (shows cached data)
   - [ ] Telegram login works
   - [ ] App doesn't crash on orientation change
   - [ ] Back button works correctly on Android
   - [ ] No horizontal scroll anywhere

---

## 📋 مرحله ۶ — پولیش نهایی

### Prompt 6.1 — انیمیشن و UX

Add smooth animations and polish the mobile UX.

Install: npm install framer-motion

1. Page transitions:
   Wrap routes with AnimatePresence
   - City → Island: slide left
   - Island → World: zoom out
   - World → Island: zoom in  
   - Any modal: slide up from bottom

2. Game feedback animations:
   - Resource collection: floating "+50 wood" text animation
   - Building upgrade: brief glow/pulse on building card
   - Attack launched: shake animation on attack button
   - Research complete: sparkle/star burst animation

3. Loading states:
   Replace all blank loading states with:
   - Skeleton screens (gray placeholder shapes)
   - Keep the game's dark theme in skeletons
[6/10/26 9:50 PM] Post: 4. Micro-interactions:
   - Button press: scale(0.95) with haptic feedback
   - Swipe to dismiss notifications
   - Pull-to-refresh on city view and messages
   - Long press on building for quick info popup

5. Sound (optional but nice):
   Add subtle game sounds using Howler.js:
   npm install howler
   - Button tap sound
   - Building complete sound
   - Attack sound
   Make all sounds opt-in (off by default)

### Prompt 6.2 — نهایی‌سازی و مستندات

Final cleanup and documentation.

1. Code cleanup:
   - Remove all console.log statements from production code
   - Remove unused imports and dead code
   - Make sure all TypeScript types are correct (no any unless necessary)
   - Consistent naming conventions throughout

2. Environment configuration:
   Create proper .env files:
   - frontend/.env.development (local backend)
   - frontend/.env.production (production backend URL)
   - frontend/.env.telegram (Telegram-specific config)

3. Update README.md with:
   - Updated project description
   - Full setup instructions for all three platforms:
     * Web browser
     * Android APK
     * Telegram Mini App
   - Screenshots of mobile UI
   - How to contribute

4. Create ROADMAP.md with future features:
   - iOS support (add Capacitor iOS platform)
   - Alliance/guild system
   - Global leaderboard
   - In-app chat
   - Push notifications via FCM
   - Apple/Google sign-in

5. Final build test:
   Run these and fix any errors:
   - cd frontend && npm run build (web)
   - cd frontend && npm run build:android (Android)
   - cd frontend && npm run build:telegram (Telegram)
   - cd backend && npm run build
`

---

## 🚀 ترتیب اجرا

| # | Prompt | زمان تخمینی |
|---|--------|-------------|
| 1.1 | Audit پروژه | 10 دقیقه |
| 1.2 | آپدیت dependencies | 15 دقیقه |
| 2.1 | نصب Capacitor | 10 دقیقه |
| 2.2 | سیستم طراحی موبایل | 20 دقیقه |
| 2.3 | Layout اصلی | 30 دقیقه |
| 2.4 | Touch-friendly components | 45 دقیقه |
| 2.5 | صفحه‌های اصلی | 60 دقیقه |
| 3.1 | بهبود API | 20 دقیقه |
| 3.2 | Offline و Cache | 25 دقیقه |
| 3.3 | Push Notifications | 20 دقیقه |
| 4.1 | Telegram WebApp | 30 دقیقه |
| 4.2 | Telegram UX | 20 دقیقه |
| 5.1 | تنظیمات Android | 15 دقیقه |
| 5.2 | تست و دیباگ | 20 دقیقه |
| 6.1 | انیمیشن و UX | 30 دقیقه |
| 6.2 | نهایی‌سازی | 15 دقیقه |

مجموع تخمینی: ۶-۸ ساعت کار agent

---

## 💡 نکات مهم

- بعد از هر prompt بررسی کن که build هنوز کار می‌کنه
- commit بزن بعد از هر مرحله: `git commit -m "feat: mobile layout"`
- اگر agent گیر کرد یا خطا داد، بگو: "Fix the error and continue"
- برای Telegram، نیاز به ساخت bot داری از [@BotFather](https://t.me/botfather)
- برای APK نهایی، نیاز به Android Studio داری روی سیستمت

---

*ساخته شده با Claude — نقشه راه Glarium Mobile*
[6/10/26 11:16 PM] Post: 
[6/10/26 11:16 PM] Post: <!-- Header -->
<div align="center">

██╗  ██╗ ██████╗ ██████╗ ██████╗ ███████╗██╗  ██╗██╗██╗     ██╗     ███████╗██████╗
██║  ██║██╔═══██╗██╔══██╗██╔══██╗██╔════╝██║ ██╔╝██║██║     ██║     ██╔════╝██╔══██╗
███████║██║   ██║██████╔╝██║  ██║█████╗  █████╔╝ ██║██║     ██║     █████╗  ██████╔╝
██╔══██║██║   ██║██╔══██╗██║  ██║██╔══╝  ██╔═██╗ ██║██║     ██║     ██╔══╝  ██╔══██╗
██║  ██║╚██████╔╝██║  ██║██████╔╝███████╗██║  ██╗██║███████╗███████╗███████╗██║  ██║
╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚═════╝ ╚══════╝╚═╝  ╚═╝╚═╝╚══════╝╚══════╝╚══════╝╚═╝  ╚═╝
**Builder · Breaker · Fixer**

*از تهران می‌سازم، برای همه جای دنیا*

[![Website](https://img.shields.io/badge/🌐_catus.ir-000000?style=for-the-badge)](https://catus.ir)
[![GitHub](https://img.shields.io/badge/GitHub-Hordekiller-181717?style=for-the-badge&logo=github)](https://github.com/hordekiller)

</div>

---

## 👾 کی‌ام؟

یه توسعه‌دهنده ایرانی که دوست داره همه چیز رو بشکنه، بفهمه چطور کار می‌کنه، بعد بهتر بسازتش.  
از game server گرفته تا WordPress plugin — اگه جالب باشه، می‌سازمش.

while (alive) {
    eat();
    code();
    break_things();
    fix_things_better();
    sleep(); // optional
}
---

## 🛠️ با چی کار می‌کنم

<div align="center">

| حوزه | ابزارها |
|---|---|
|Webeb** | PHP · WordPress · HTML · CSS |
|Androidid** | Java · Android SDK |
|Game Serverer** | C++ · MaNGOS · WoW Emulation |
|Toolsls** | Git · Linux · MySQL |

</div>

---

## 🚀 پروژه‌های اصلی

### 🔒 [Atlas Backup & Migration](https://github.com/Hordekiller/atlas-backup-migration)
> پلاگین وردپرس برای بکاپ کامل، مایگریشن سایت و export هوشمند  
> پشتیبانی از WooCommerce · Elementor · Site-to-Site transfer

### 📡 [DNS To Go](https://github.com/Hordekiller/DNS-To-Go-master)
> اپ اندروید برای بهینه‌سازی DNS — ساخته‌شده برای گیمرها  
> کاهش لیتنسی · دور زدن محدودیت‌های منطقه‌ای · رابط کاربری مدرن

### ⚔️ [WoW Private Server](https://github.com/Hordekiller/world-of-warcraft)
> سرور شخصی World of Warcraft — چون گاهی باید دنیای خودت رو بسازی

---

## 📊 آمار

<div align="center">

![GitHub Stats](https://github-readme-stats.vercel.app/api?username=hordekiller&show_icons=true&theme=tokyonight&hide_border=true&bg_color=0d1117&title_color=58a6ff&icon_color=58a6ff)

![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=hordekiller&layout=compact&theme=tokyonight&hide_border=true&bg_color=0d1117&title_color=58a6ff)

</div>

---

## 🎮 یه چیز درباره من

- 🕹️ از بچگی WoW بازی می‌کردم، الان سرورش رو می‌سازم
- 🇮🇷 ساخته‌شده در ایران
- ☕ با قهوه کد می‌زنم، بدون قهوه باگ می‌زنم
- 🔥 شعارم: *اگه کار نمی‌کنه، یعنی هنوز نفهمیدم چرا کار می‌کنه*

---

<div align="center">

*"The best code is the code that solves a real problem.[catus.ir](https://catus.ir)r)** · Iran · Building stuff that matters

</div>
