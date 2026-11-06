# گیر انداز

**گیر انداز** یک بازی مسابقه‌ای کارتونی و خانوادگی برای iOS و Android است. بازیکن با یکی از
قهرمان‌های اورجینال بازی در یک مسیر رنگارنگ می‌دود، از مانع‌ها می‌پرد، ستاره جمع می‌کند و
می‌تواند آفلاین با ربات یا آنلاین با یک دوست مسابقه بدهد.

> نسخه‌ی فعلی یک MVP قابل اجراست. بازی روی وب، PWA و پروژه‌های بومی Capacitor اجرا می‌شود؛
> اما برای انتشار در App Store و Google Play به حساب توسعه‌دهنده، امضای باینری و
> کلیدهای واقعی AdMob/RevenueCat نیاز دارید.

---

## فهرست

- [ویژگی‌ها](#ویژگی‌ها)
- [اجرا و ساخت](#اجرا-و-ساخت)
- [نسخه‌های بومی](#نسخه‌های-بومی)
- [پیکربندی](#پیکربندی)
- [ساختار پروژه](#ساختار-پروژه)
- [اقتصاد بازی](#اقتصاد-بازی)
- [تبلیغات و خرید](#تبلیغات-و-خرید)
- [حریم خصوصی و کودکان](#حریم-خصوصی-و-کودکان)
- [تست و کیفیت](#تست-و-کیفیت)
- [محدودیت‌های شناخته‌شده](#محدودیت‌های-شناخته‌شده)
- [مجوزها](#مجوزها)

---

## ویژگی‌ها

- **دو حالت بازی:** مسابقه‌ی آفلاین با ربات (آسان/متوسط/سخت) و مسابقه‌ی آنلاین با دوست.
- **۱۰ قهرمان اورجینال:** آرش، تارا، کاپیتان پارسا، نیلو، روبی، ماهی، دینو، پیکسل، آوا و کوروش.
- **دو قهرمان رایگان** و هشت قهرمان قابل آزادسازی با سکه‌ی داخل بازی.
- **نسخه‌ی رایگان و پریمیوم:** پریمیوم یک خرید یک‌باره است و همه‌ی قهرمان‌ها را آزاد و
  تبلیغ‌ها را خاموش می‌کند.
- **تبلیغات کودک‌محور:** AdMob با درخواست غیرشخصی‌سازی‌شده، محدودیت محتوای عمومی و
  تنظیمات مناسب کودکان.
- **آنلاین همتا به همتا:** اتاق شش‌کاراکتری با PeerJS/WebRTC؛ فقط پیام‌های فنی مسابقه
  رد و بدل می‌شود و چت آزاد وجود ندارد.
- **PWA:** نصب روی وب، پشتیبانی از آفلاین و رابط راست‌به‌چپ فارسی.
- **بدون حساب کاربری:** پیشرفت فقط روی دستگاه ذخیره می‌شود.

## اجرا و ساخت

### پیش‌نیازها

- Node.js 22 یا جدیدتر (توسعه با Node 24 تست شده است)
- npm 10 یا جدیدتر
- برای نسخه‌ی بومی: Android Studio / JDK 21 برای Android و macOS + Xcode برای iOS

### نصب

```bash
npm install
cp .env.example .env
npm run dev
```

در ویندوز PowerShell:

```powershell
Copy-Item .env.example .env
npm run dev
```

سپس `http://localhost:5173` را باز کنید. بازی بدون هیچ کلید تبلیغات یا خریدی هم اجرا
می‌شود؛ در این حالت تبلیغ و خرید «حالت نمایشی» هستند.

### دستورهای اصلی

```bash
npm run dev        # سرور توسعه
npm run typecheck  # بررسی TypeScript
npm run build      # typecheck + build تولیدی در dist/
npm run preview    # پیش‌نمایش build
```

اگر شبیه‌ساز `.bin` در محیط شما مشکل داشت، می‌توانید مستقیم اجرا کنید:

```bash
node node_modules/typescript/bin/tsc --noEmit
node node_modules/vite/bin/vite.js build
node node_modules/vite/bin/vite.js --host
```

### PWA

`vite-plugin-pwa` به‌طور خودکار manifest و Service Worker را می‌سازد. فایل‌های صفحه‌ی
حقوقی (`public/privacy.html` و `public/terms.html`) در build کپی می‌شوند و آفلاین هم
در دسترس‌اند.

## نسخه‌های بومی

پروژه‌های `android/` و `ios/` با Capacitor ساخته شده‌اند. پس از هر تغییر وب:

```bash
npm run cap:sync       # build + sync هر دو پلتفرم
npm run android        # build + sync + باز کردن Android Studio
npm run ios            # build + sync + باز کردن Xcode (فقط macOS)
```

اگر `npx cap` در محیط شما اجرا نشد (مثلاً به‌خاطر شبیه‌ساز ناقص `.bin`)، اسکریپت‌های
بالا از مسیر مستقیم Node استفاده می‌کنند. اجرای مستقیم هم ممکن است:

```bash
node node_modules/@capacitor/cli/bin/capacitor sync
node node_modules/@capacitor/cli/bin/capacitor open android
node node_modules/@capacitor/cli/bin/capacitor open ios
```

### Android

- `applicationId`: `com.girandaz.game`
- `minSdkVersion`: 24، `targetSdkVersion`: 36
- App ID آزمایشی AdMob در `android/app/src/main/AndroidManifest.xml` تنظیم شده است.
- برای انتشار، App ID واقعی AdMob را جایگزین کنید و `VITE_ADMOB_TESTING=false` بگذارید.

### iOS

- Bundle ID: `com.girandaz.game`
- جهت بازی روی iPhone عمودی است.
- `GADApplicationIdentifier` آزمایشی و فهرست `SKAdNetworkItems` در
  `ios/App/App/Info.plist` قرار دارد.
- برای انتشار، App ID واقعی AdMob و فهرست به‌روز SKAdNetwork را از کنسول AdMob
  جایگزین کنید.
- ساخت آرشیو و TestFlight فقط روی macOS با Xcode انجام می‌شود.

> در ویندوز می‌توانید پروژه‌ی iOS را بسازید و فایل‌های آن را ویرایش کنید، اما
> کامپایل، امضا و آپلود App Store نیازمند macOS/Xcode است.

### آیکون و اسپلش

فایل‌های منبع در `resources/` هستند. پس از تغییر طرح:

```bash
npx capacitor-assets generate --android --ios
```

اسکریپت‌های تولید تصویر در `scripts/` قرار دارند. اگر Sharp در محیط شما درست نصب
نشده باشد، `scripts/generate-assets-chrome.mjs` با Chrome کار می‌کند.

## پیکربندی

همه‌ی کلیدهای `VITE_*` در زمان build داخل بسته قرار می‌گیرند و **راز نیستند**.
فایل `.env.example` را به `.env` کپی کنید.

### RevenueCat / خرید

```dotenv
VITE_REVENUECAT_IOS_API_KEY=
VITE_REVENUECAT_ANDROID_API_KEY=
VITE_REVENUECAT_ENTITLEMENT_ID=premium
VITE_PREMIUM_PRODUCT_ID=girandaz_premium_lifetime
```

- در RevenueCat یک Entitlement با شناسه‌ی `premium` بسازید.
- محصول یک‌باره‌ی `girandaz_premium_lifetime` را در App Store Connect و Google Play
  Console تعریف کنید.
- محصول‌ها را در Offering فعلی RevenueCat قرار دهید.
- بدون کلید، دکمه‌ی خرید در وب حالت نمایشی و در بومی «پیکربندی نشده» است.

### AdMob

```dotenv
VITE_ADMOB_IOS_INTERSTITIAL=
VITE_ADMOB_IOS_REWARDED=
VITE_ADMOB_ANDROID_INTERSTITIAL=
VITE_ADMOB_ANDROID_REWARDED=
VITE_ADMOB_TESTING=true
```

اگر شناسه‌ها خالی باشند، شناسه‌های آزمایشی رسمی AdMob استفاده می‌شوند. برای انتشار،
شناسه‌های واقعی را وارد و `VITE_ADMOB_TESTING=false` کنید. App ID واقعی را نیز در
فایل‌های بومی جایگزین کنید.

### مسابقه‌ی آنلاین

```dotenv
VITE_PEER_HOST=
VITE_PEER_PORT=443
VITE_PEER_PATH=/
VITE_PEER_KEY=peerjs
VITE_PEER_SECURE=true
VITE_TURN_URL=
VITE_TURN_USERNAME=
VITE_TURN_CREDENTIAL=
```

بدون `VITE_PEER_HOST` از سرور عمومی PeerJS استفاده می‌شود که فقط برای نمونه‌سازی
مناسب است. برای انتشار، PeerServer اختصاصی و یک سرویس TURN راه‌اندازی کنید.

## ساختار پروژه

```text
src/
  App.tsx                 مسیرها، خرید/تبلیغ، وضعیت کلی
  screens/                خانه، قهرمان‌ها، فروشگاه، تنظیمات، آنلاین، مسابقه، نتیجه
  components/             UI مشترک، مودال، دروازه‌ی والدین، پرتره‌ی قهرمان
  game/
    engine.ts             موتور قطعی مسابقه، برخورد، ستاره، پایان
    renderer.ts           رندر Canvas 2D
  services/
    ads.ts                AdMob و تنظیمات کودک‌محور
    purchases.ts          RevenueCat، بازیابی خرید، Apple Search Ads
    online.ts             PeerJS، اتاق، پیام‌های مسابقه
    audio.ts              صدا و لرزش
  state/profile.ts        ذخیره‌ی محلی پروفایل
  data/heroes.ts          ۱۰ قهرمان اورجینال و اقتصاد پایه
public/
  icons/                  آیکون وب/PWA
  privacy.html            سیاست حریم خصوصی
  terms.html              شرایط استفاده
  legal.css               استایل مشترک صفحات حقوقی
android/ ios/             پروژه‌های Capacitor
docs/                     اسناد انتشار، درآمد و مجوز
resources/                آیکون/اسپلش منبع
scripts/                  تولید دارایی‌های گرافیکی
```

## اقتصاد بازی

| مورد | مقدار |
| --- | --- |
| هدیه‌ی روزانه | ۸۰ سکه |
| تبلیغ جایزه‌دار | ۷۵ سکه، حداکثر ۳ بار در روز |
| پایان مسابقه | ۱۵ سکه پایه + ۴ سکه برای هر ستاره |
| برد | +۴۰ سکه |
| باخت | +۱۲ سکه |
| مسابقه‌ی آنلاین | +۱۵ سکه |
| قهرمان رایگان | آرش و تارا |
| قیمت قهرمان‌ها | ۴۵۰ تا ۲٬۲۰۰ سکه |

همه‌ی قهرمان‌ها فقط ظاهر و داستان متفاوت دارند و مزیت رقابتی یا شانس برد بیشتری
نمی‌دهند. پریمیوم همه‌ی قهرمان‌ها را آزاد می‌کند، اما سکه و پیشرفت را حذف نمی‌کند.

## تبلیغات و خرید

- **AdMob (Android و iOS):** تبلیغ میان‌صفحه‌ای هر سه مسابقه یک‌بار (فقط نسخه‌ی رایگان)
  و تبلیغ جایزه‌دار با پاداش ۷۵ سکه.
- **RevenueCat:** مدیریت خرید یک‌باره‌ی پریمیوم و بازیابی خرید.
- **Apple Search Ads:** اتصال Attribution از طریق RevenueCat. اپل شبکه‌ی تبلیغات
  نمایشی درون‌برنامه‌ای معادل AdMob ندارد؛ بنابراین نمایش تبلیغ در iOS نیز با AdMob
  انجام می‌شود.
- **وب:** هیچ تبلیغ یا خرید واقعی انجام نمی‌شود؛ صفحه‌ی نمایشی داخلی است.

## حریم خصوصی و کودکان

- بدون حساب کاربری، نام، ایمیل، شماره تلفن، موقعیت مکانی یا چت آزاد.
- پیشرفت بازی فقط در حافظه‌ی محلی دستگاه ذخیره می‌شود.
- در مسابقه‌ی آنلاین فقط شناسه‌ی قهرمان، پیشرفت، زمان پایان و پیام‌های فنی رد و بدل
  می‌شود.
- تبلیغات با `tagForChildDirectedTreatment`، `tagForUnderAgeOfConsent` و
  `maxAdContentRating: General` درخواست می‌شوند.
- خرید واقعی پشت قفل والدین (پرسش ریاضی) قرار دارد.

متن کامل در `public/privacy.html` و `public/terms.html` است. پیش از انتشار، ایمیل
پشتیبانی و نشانی میزبانی این دو صفحه را با اطلاعات واقعی جایگزین کنید.

## تست و کیفیت

```bash
npm run typecheck
npm run build
```

سناریوهای تست دستی:

1. اجرای آفلاین و شروع مسابقه با ربات در هر سه سطح سختی.
2. برد/باخت، محاسبه‌ی سکه و ستاره، و نمایش نتیجه.
3. ورود به فروشگاه، دروازه‌ی والدین، خرید نمایشی وب و بازیابی.
4. تبلیغ جایزه‌دار نمایشی، سقف سه‌بار در روز و هدیه‌ی روزانه.
5. آزاد کردن قهرمان با سکه، انتخاب قهرمان و فیلترهای روستر.
6. دو پنجره‌ی مستقل برای ساخت اتاق و پیوستن با کد شش‌کاراکتری.
7. قطع اتصال حریف و نمایش نتیجه‌ی مناسب.
8. تنظیمات، صدا، سختی، حریم خصوصی، شرایط و «شروع دوباره».
9. اندازه‌های ۳۹۰×۸۴۴ و ۳۹۰×۶۶۷ و بررسی اسکرول صفحه‌ی نتیجه.
10. نصب PWA و اجرای آفلاین پس از بارگذاری اول.

اسناد مرتبط:

- [`docs/STORE_RELEASE.md`](docs/STORE_RELEASE.md) — چک‌لیست انتشار
- [`docs/MONETIZATION.md`](docs/MONETIZATION.md) — اقتصاد، تبلیغات و خرید
- [`docs/LICENSING.md`](docs/LICENSING.md) — مالکیت فکری و مجوز شخصیت‌ها

## محدودیت‌های شناخته‌شده

- خرید واقعی در نسخه‌ی وب وجود ندارد و فقط نمایشی است.
- PeerJS عمومی برای نمونه‌سازی است؛ برای تولید به PeerServer/TURN اختصاصی نیاز است.
- ساخت و امضای iOS روی ویندوز ممکن نیست.
- AdMob و RevenueCat بدون کلیدهای واقعی در حالت تست/نمایشی کار می‌کنند.
- آزمون روی دستگاه واقعی iOS/Android انجام نشده است؛ پیش از انتشار، TestFlight و
  Internal Testing لازم است.
- شخصیت‌های دارای مالکیت (میکی، السا، اسپایدرمن، ناروتو و…) در این نسخه وجود ندارند.
  افزودن آن‌ها فقط با مجوز رسمی صاحب اثر ممکن است. جزئیات در `docs/LICENSING.md`.
- پذیرش در دسته‌ی کودکان App Store و برنامه‌ی Families Google Play نیازمند بررسی
  حقوقی/انطباق نهایی و آزمون تبلیغات واقعی است.

## مجوزها

کد و دارایی‌های اورجینال این پروژه فعلاً `UNLICENSED` هستند. فونت Vazirmatn تحت
SIL OFL، React/Vite/Capacitor تحت MIT و PeerJS تحت MIT منتشر شده‌اند. فهرست کامل در
`docs/LICENSING.md` آمده است.

---

ساخته‌شده برای خنده، سرعت و بازی جوانمردانه.
#   g i r - a n d a z  
 