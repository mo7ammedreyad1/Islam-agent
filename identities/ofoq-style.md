# هوية "أفق الليلية" (Ofoq Night) — سينمائية، خلفية فيديو B-roll حقيقي متحرك (أولوية) أو صورة Ken Burns احتياطية

## الوصف
طابع سينمائي هادئ: **واجهة تحكم فاتحة/بيضاء** (زجاجية شفافة، مطابقة تمامًا
لباقي هويات المشروع في شكل الـ HUD/الأزرار) تحيط بفيديو **محتواه غامق ومظلم**
— التباين ده مقصود في التصميم الأصلي ومش خطأ. المحتوى نفسه: خلفية أساسها
**فيديو B-roll حقيقي متحرك** (منظر طبيعي/سماء/بحر... إلخ يجيبه الـ Agent من
مصدر مجاني وقت التنفيذ)، وفي حالة تعذّر إيجاد فيديو مناسب فقط تسقط الخلفية
لصورة ثابتة بحركة تكبير بطيئة (تقنية Ken Burns)، وفي أسوأ الأحوال تدرج رمادي
فاتح. فوق أي من الاختيارين الثلاثة **فينييت داكنة** (تعتيم تدريجي من المنتصف
للأطراف) بتخلي أي نص أبيض فوقها واضح مهما كانت الخلفية. هيدر ثابت باسم السورة
والقارئ أعلى الشاشة بخط Reem Kufi، والآية نفسها في المنتصف بحركة **كتلة واحدة**
(مش كلمة كلمة) fade + scale + إزاحة خفيفة لأعلى. دقة 720×1280 (أصغر من باقي
الهويات، بس نفس نسبة 9:16). مفيش بانل تفسير — تلاوة minimal بس.

> **نشأة الهوية دي ونسختها**: اتبنت بمنطق "استيعاب هوية من ملف خارجي" (القسم
> 1.5 في `AGENTS.md`) من ملف خارجي (`ai_studio_code_-_2026-07-27T072109_860.html`،
> بعنوان داخلي "Ofoq Studio - Surah Al-Ikhlas Shorts 720p"). استُخرج الستايل
> البصري الأصلي بمراجعة كل سطر في الملف الأصلي وقيمة كل لون/رقم/دالة بالحرف،
> ثم أُعيد بناء منطق جلب الصوت والصورة وبيانات المحتوى بالكامل بما يتماشى مع
> "عقد ملف هوية `.md`" ذي الطبقتين (self-contained، `prepareIdentity` +
> `drawSceneAtTime` غير متزامنتين).
>
> **هذا الملف هو النسخة الخامسة**. النسخة الرابعة خلّت خلفية الفيديو
> (`BACKGROUND_VIDEO_URL`) **حقل بيانات إلزامي يملأه الـ Agent في كل مهمة**
> (زي `SURAH_VERSES` بالظبط) بدل ما كانت صورة اختيارية معطّلة بـ `null` بشكل
> دائم. النسخة الخامسة دي (الحالية) بتضيف 3 حاجات فوق كده، مبنية على مشكلة
> فعلية حصلت وقت التنفيذ (فيديو خلفية بيرجع 403 وقت التصدير الحقيقي رغم نجاح
> فحص CORS المبدئي، ومفيش أي تنبيه واضح إن الفيديو النهائي خرج من غير خلفية
> خالص):
> 1. تحذير صريح من استخدام buckets تجريبية عامة (زي عيّنات Google التطويرية) كمصدر فيديو خلفية.
> 2. علم `window.__backgroundStatus` بيتحدد آخر `prepareIdentity()` عشان يبين بوضوح لو الفيديو خرج من غير أي خلفية حقيقية.
> 3. مرجع ثابت بأسماء مجلدات قراء `everyayah.com` المتحقَّق منها فعليًا (صح وغلط) عشان الـ Agent ميضيعش وقت يعيد اكتشافها كل مهمة.
>
> التفاصيل الكاملة والسبب موثّقين في "ملاحظات معروفة" آخر الصفحة.

**فحص التوافق مع الرانر (القسم 1.5)** — الملف الأصلي فشل في 4 من 6 بنود
(هذا الفحص تاريخي على الملف الخارجي الأصلي ولا يتغيّر بتحديث النسخة الرابعة):
- ✅ يستخدم Mediabunny صح (`Output`/`CanvasSource`/`AudioBufferSource`).
- ✅ الصورة الخلفية بتتحمّل بـ `img.crossOrigin = "anonymous"` بشكل صحيح تقنيًا.
- ❌ صفر render hooks (`renderStatus`, `renderProgress`, `startVideoRender`,
  `?autorender=true`, `video-render-complete`, `__renderFilename`/`__renderBase64`).
- ❌ مفيش AAC encoder polyfill — هيفشل على الـ CI.
- ❌ الصوت ملف واحد كامل من `quranicaudio.com`، مش آية بآية من `everyayah.com`.
- ❌ توقيت الآيات (`RAW_CUES`) أرقام مكتوبة يدويًا بالتخمين، مش محسوبة من تقطيع
  صوتي حقيقي.

- **الأبعاد**: 720×1280 (Shorts عمودي، 9:16، دقة أصغر من باقي الهويات)، 60fps.
- **حالة الاستخدام**: تلاوة قصيرة/سورة صغيرة، طابع سينمائي هادئ بدل الرَّق الكلاسيكي.
- **شكل بيانات المحتوى** (`SURAH_NUMBER`, `RECITER_ID`, `RECITER_DISPLAY_NAME`,
  `SURAH_DISPLAY_NAME`, `OUTPUT_FILENAME`, `SURAH_VERSES`, `BACKGROUND_VIDEO_URL`):
  كل عنصر في `SURAH_VERSES` فيه `text` بس. الصوت بيتجاب من `everyayah.com` آية
  بآية داخل `prepareIdentity()` بنفس باترن الهويات التانية في المشروع.
  `BACKGROUND_VIDEO_URL` **حقل بيانات إلزامي** زي أي حقل تاني — قيمته الحقيقية
  بتيجي من بحث/تحقق فعلي وقت تنفيذ المهمة (راجع القسم اللي بعد ده)، مش قيمة
  ثابتة أو تخمين.

### منطق اختيار وجلب فيديو الخلفية (إلزامي لكل مهمة تستخدم هذه الهوية)
الهوية دي بقت معتمدة أساسًا على فيديو B-roll حقيقي متحرك كخلفية، مش صورة ثابتة.
دور الـ Agent في كل مهمة قبل كتابة `scene.html`:

1. **تحديد الموضوع البصري**: من طلب المستخدم لو حدد نوع الخلفية (مثلاً "منظر
   طبيعي")، أو باختيار موضوع هادئ متماشي مع الطابع السينمائي الداكن للهوية
   (سماء/غروب/بحر/جبال/سحاب) لو المستخدم ماحددش.
2. **البحث عن فيديو حر الاستخدام** من مصادر مجانية، بالترتيب:
   - لو فيه مفتاح API متاح في متغيرات البيئة، استخدم بحث Pixabay
     (`https://pixabay.com/api/videos/?key=$PIXABAY_API_KEY&q=<الموضوع>`)
     أو Pexels (`https://api.pexels.com/videos/search?query=<الموضوع>` مع
     Header باسم `Authorization: $PEXELS_API_KEY`) واختر أعلى دقة متاحة قريبة
     من 720×1280 أو أي نسبة تقفل بـ `object-fit: cover` كويس.
   - لو مفيش أي مفتاح API متاح، دوّر (curl/بحث) على رابط فيديو `.mp4` مباشر
     من مصادر لا تتطلب مفتاح (مثل Mixkit أو Coverr) بنفس الموضوع.
3. **تحقق إلزامي من CORS قبل أي استخدام** — نفس قاعدة الصور بالحرف:
   ```bash
   curl -sI "<رابط الفيديو>" | grep -i access-control-allow-origin
   ```
   لو الـ header ده مش موجود، الرابط ده مرفوض تمامًا ولازم تجربة مصدر تاني —
   استخدامه هيخلي الكانفاس "tainted" ويكسر التصدير بالكامل، مش مجرد تحذير.

   ⚠️ **نجاح فحص `curl -sI` مش ضمان كافي وحده**: بعض السيرفرات (خصوصًا
   buckets/عيّنات التجربة العامة) بترجّع header الـ CORS بشكل سليم على طلب
   `HEAD` بسيط، لكن بترفض الطلب الفعلي (`GET`/streaming من متصفح حقيقي وقت
   التصدير) بـ `403 Forbidden` — لأن الرفض مبني على حماية hotlink أو حد
   استخدام مش ظاهر في الـ headers. **ممنوع استخدام buckets/روابط عيّنات
   تجريبية عامة معروفة** كمصدر خلفية إنتاج فعلي — مثال محدد ممنوع:
   `commondatastorage.googleapis.com/gtv-videos-bucket/...` (bucket عيّنات
   Google للتجربة التقنية بس، مش مخصص للاستخدام المباشر في فيديوهات نهائية).
   فضّل دايمًا مصادر فيديو مخصصة أصلًا للتضمين/الاستخدام الحر (نتائج بحث
   Pixabay/Pexels الفعلية عبر الـ API، أو مصادر CDN لمنصات كده زي Mixkit
   وCoverr) على أي رابط "عيّنة تجريبية" عام.
4. بعد التأكد من رابط سليم، يُضخ في `BACKGROUND_VIDEO_URL` كقيمة حقيقية قبل
   كتابة `scene.html` — بنفس منطق حقن بيانات المحتوى التانية (`SURAH_VERSES`
   إلخ) في القسم 4 من `AGENTS.md`.
5. **خطة تراجع عند الفشل (Fallback)**: لو بعد محاولات معقولة (2-3 مصادر) مفيش
   فيديو مناسب يعدّي فحص CORS، الـ Agent يرجع لصورة منظر طبيعي ثابتة بنفس
   الأسلوب (رابط CORS-safe في `BACKGROUND_IMAGE_URL`)، ويوثّق في الـ log إنه
   استخدم الخيار الاحتياطي وليه. الفشل الكامل في الاتنين (فيديو وصورة) يسيب
   الخلفية على التدرج الرمادي الاحتياطي الأخير — النظام لازم يفضل يشتغل ويصدّر
   الفيديو في كل الأحوال، بس بأعلى جودة خلفية ممكنة متاحة فعليًا.

### مرجع ثابت: مجلدات قراء `everyayah.com` (متحقَّق منها فعليًا)
كل رابط صوت من `everyayah.com` شكله `https://www.everyayah.com/data/<RECITER_ID>/<SURAH_NUMBER><AYAH_NUMBER>.mp3`
(الرقمين `SURAH_NUMBER` و`AYAH_NUMBER` كل واحد 3 خانات بـ padding أصفار).
اسم مجلد القارئ (`RECITER_ID`) **حساس جدًا للتهجئة والفواصل**، وتخمينه غلط
بيكلف دقايق فحص بالـ curl كل مهمة من غير داعي. القيم تحت اتأكد منها فعليًا
بـ `curl -sI` (200 = شغالة، 404 = غلط):

| القارئ | `RECITER_ID` الصحيح | ملاحظة |
|---|---|---|
| مشاري راشد العفاسي | `Alafasy_128kbps` | ✅ متحقَّق |
| محمد أيوب | `Muhammad_Ayyoub_128kbps` | ✅ متحقَّق — لاحظ **Ayyoub بحرفين O**، مش `Ayyub` |
| عبد الباسط عبد الصمد (مرتّل) | `Abdul_Basit_Murattal_192kbps` | ✅ متحقَّق |

**تهجئات جُرّبت واتأكد إنها غلط (404) — متتكررش**:
`Muhammad_Ayyub_128kbps`، `Muhammad_Ayyub_32kbps`، `Muhammad_Ayyub` (من غير
kbps)، `Minshawi_128kbps`. مصدر `media.blubrry.com`/`audio.islamweb.net`
كبديل لصوت محمد أيوب اتجرب برده ورجع 404 أو تحويل مكسور — متعتمدش عليه.

لو المهمة طلبت قارئ مش في الجدول، لازم يتأكد بـ `curl -sI` قبل الاستخدام
زي أي رابط تاني، **ويُضاف للجدول ده** (تحديث الملف نفسه) بعد التأكد، عشان
الجدول يفضل بيكبر ويوفر وقت مع الوقت بدل ما يتكرر نفس التخمين.

## روابط الخطوط
نفس رابط الخطوط الموجود في الملف الأصلي بالحرف (يشمل أوزان IBM Plex Sans Arabic
الكاملة من 100 لـ800، حتى لو مش كلها مستخدمة فعليًا في الرسم، لأنها بتُستخدم في
الـ HUD/الواجهة):
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400;1,700&family=Reem+Kufi:wght@400..700&family=IBM+Plex+Sans+Arabic:wght@100;200;300;400;500;600;700;800&display=swap" rel="stylesheet">
```

## CSS كامل
**ملاحظة**: الـ HUD والأزرار وسجل الأخطاء هنا **فاتحة/بيضاء** (زجاجية شفافة على
خلفية صفحة داكنة #0f0f11) — مطابقة لباترن باقي هويات المشروع، مش داكنة زي محتوى
الفيديو نفسه. متلخبطش بين الاتنين.

```css
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
    background-color: #0f0f11; color: #111111;
    font-family: -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Arabic', 'IBM Plex Sans Arabic', sans-serif;
    display: flex; align-items: center; justify-content: center;
    height: 100vh; width: 100vw; overflow: hidden;
}
#viewport { position: relative; width: 100%; height: 100%; display: flex; align-items: center; justify-content: center; padding: 2vh; }
canvas {
    background: #f2f2f2; box-shadow: 0 0 80px rgba(0, 0, 0, 0.4);
    border-radius: 12px; border: 1px solid #dcdcdc;
    max-width: 100%; max-height: 86vh; width: auto; height: auto;
    aspect-ratio: 9 / 16; object-fit: contain;
}
#hud {
    position: absolute; top: 3vh; right: 4vw;
    background: rgba(255, 255, 255, 0.95); border: 1px solid #dcdcdc; border-radius: 999px;
    padding: 1.2vh 2.5vw; display: flex; align-items: center; gap: 12px;
    font-size: 14px; font-weight: 600; color: #111; backdrop-filter: blur(12px); z-index: 100;
    font-family: -apple-system, BlinkMacSystemFont, 'IBM Plex Sans Arabic', sans-serif;
}
.spinner { width: 18px; height: 18px; border: 2px solid #111; border-top-color: transparent; border-radius: 50%; animation: spin 0.8s linear infinite; }
@keyframes spin { to { transform: rotate(360deg); } }
#controls-overlay {
    position: absolute; bottom: 3vh; display: flex; gap: 12px; z-index: 100;
    background: rgba(255, 255, 255, 0.95); border: 1px solid rgba(0, 0, 0, 0.15);
    padding: 1.2vh 2vw; border-radius: 999px; backdrop-filter: blur(10px);
    max-width: 92vw; flex-wrap: wrap; justify-content: center;
    font-family: -apple-system, BlinkMacSystemFont, 'IBM Plex Sans Arabic', sans-serif;
}
.btn {
    background: none; border: none; color: #111;
    font-family: -apple-system, BlinkMacSystemFont, 'IBM Plex Sans Arabic', sans-serif;
    font-size: 14px; font-weight: 600; cursor: pointer; padding: 8px 18px; border-radius: 999px;
    display: flex; align-items: center; gap: 6px; transition: all 0.3s ease;
}
.btn-preview { background: rgba(0, 0, 0, 0.05); border: 1px solid rgba(0, 0, 0, 0.1); }
.btn-preview:hover { background: rgba(0, 0, 0, 0.1); }
.btn-render { background: #111111; color: #ffffff; border: 1px solid #111111; }
.btn-render:hover { background: #333333; }

#console-modal {
    display: none; position: fixed; top: 8vh; left: 8vw; width: 84vw; height: 75vh;
    background: rgba(10, 10, 12, 0.96); border: 1px solid #333; border-radius: 16px;
    z-index: 999; color: #00ff66; font-family: monospace; padding: 20px;
    box-shadow: 0 20px 60px rgba(0,0,0,0.8); backdrop-filter: blur(16px);
    flex-direction: column; gap: 12px;
}
#console-header {
    display: flex; justify-content: space-between; align-items: center;
    border-bottom: 1px solid #222; padding-bottom: 10px; color: #fff; font-size: 15px; font-weight: bold;
}
#console-output {
    flex: 1; overflow-y: auto; white-space: pre-wrap; word-break: break-all;
    font-size: 13px; line-height: 1.6; padding-right: 5px;
}
.log-line { margin-bottom: 4px; border-bottom: 1px solid rgba(255,255,255,0.03); padding-bottom: 2px; }
.log-info { color: #00ff66; }
.log-warn { color: #ffcc00; }
.log-error { color: #ff5555; font-weight: bold; }
```

## أبعاد الكانفاس
```html
<canvas id="shortsCanvas" width="720" height="1280"></canvas>
```

## كود JS — طبقة الهوية بالكامل (self-contained)
افهم الكتلة دي بالكامل قبل كتابتها في "طبقة الهوية" جوه `scene.html` — دي
Blueprint هندسي يُستوعب أولاً، مش نص يُنسخ ويُلصق من غير فهم — وبعدين اكتبها في
الملف الناتج مع استبدال قيم قسم "بيانات المحتوى" بس بالقيم الحقيقية للمهمة
الحالية (نتيجة `curl`/بحث فعلي، بما فيها `BACKGROUND_VIDEO_URL`).

```js
// ================================================================
// 📋 بيانات المحتوى — بالقيم دي إنت (الوكيل) اللي بتكتبها/تعدّلها لكل
// مهمة (نتيجة curl فعلية، راجع القسم 4 في AGENTS.md). القيم تحت مجرد
// مثال (سورة الإخلاص) يوضح الشكل المطلوب بالظبط — الأسماء والبنية ثابتة
// لهذه الهوية، القيم بس بتتغيّر.
// ================================================================
const SURAH_NUMBER = '112';
const RECITER_ID = 'Alafasy_128kbps';
const RECITER_DISPLAY_NAME = 'الشيخ مشاري راشد العفاسي';
const SURAH_DISPLAY_NAME = 'سُورَةُ الْإِخْلَاصِ';
const OUTPUT_FILENAME = 'Ofoq_Night_Al_Ikhlas';
const SURAH_VERSES = [
    { text: 'قُلْ هُوَ اللَّهُ أَحَدٌ ۝' },
    { text: 'اللَّهُ الصَّمَدُ ۝' },
    { text: 'لَمْ يَلِدْ وَلَمْ يُولَدْ ۝' },
    { text: 'وَلَمْ يَكُن لَّهُ كُفُوًا أَحَدٌ ۝' }
];

// خلفية الفيديو — حقل بيانات إلزامي (زي SURAH_VERSES بالظبط): رابط فيديو
// حقيقي مُتحقَّق منه (CORS + محتوى مناسب) يجيبه الـ Agent وقت تنفيذ المهمة
// عبر Pexels/Pixabay/مصدر مجاني آخر (راجع "منطق اختيار وجلب فيديو الخلفية"
// في وصف الهوية). القيمة تحت "null" هي مجرد مثال بنية الحقل، مش قيمة نهائية
// مسموح تسيبها زي ما هي في مهمة فعلية.
const BACKGROUND_VIDEO_URL = null; // مثال بعد الملء: 'https://cdn.pixabay.com/video/.../xxxxx.mp4'
// صورة احتياطية (اختيارية) — تتفعّل فقط لو فشل إيجاد فيديو مناسب (راجع خطة التراجع)
const BACKGROUND_IMAGE_URL = null;

// ================================================================
// ⚙️↔🎨 متغيرات العقد مع الطبقة التقنية — الأسماء دي ثابتة، الطبقة
// التقنية بتقرأها بعد ما prepareIdentity() يخلص
// ================================================================
let CONFIG = { fps: 60, width: 720, height: 1280, duration: 0 }; // duration بتتحدد فعليًا جوه prepareIdentity()
let audioBuffer = null; // بيتملى جوه prepareIdentity()

// ================================================================
// 🎨 تصميم الهوية — ثوابت وأدوات رسم داخلية
// ================================================================
let parsedScenes = []; // حالة داخلية للهوية، بتتملى جوه prepareIdentity()

// نفس سلسلة الخط الاحتياطية الموجودة في الملف الأصلي بالحرف (Georgia/Times كبديل لو Amiri اتأخر تحميله)
const QURAN_FONT = "'Amiri', 'Georgia', 'Times New Roman', serif";
const HEADER_FONT = "'Reem Kufi', sans-serif";

const PALETTE = {
    bgFallbackTop: "#FFFFFF",           // خلفية احتياطية فاتحة (لو مفيش فيديو ولا صورة، أو لسه مبتحملتش)
    bgFallbackBottom: "#E5E5E5",
    vignetteInner: "rgba(0, 0, 0, 0.20)",  // فينييت: تعتيم خفيف في المنتصف
    vignetteOuter: "rgba(0, 0, 0, 0.75)",  // فينييت: تعتيم قوي في الأطراف
    textPrimary: "#FFFFFF",
    textMuted: "rgba(255, 255, 255, 0.85)",
    dividerLine: "rgba(255, 255, 255, 0.3)",
    shadowHeader: "rgba(0, 0, 0, 0.65)",
    shadowVerse: "rgba(0, 0, 0, 0.8)"
};

// --- خلفية فيديو B-roll حقيقي (أولوية) أو صورة Ken Burns احتياطية ---
const KEN_BURNS_START_ZOOM = 1.05;
const KEN_BURNS_ZOOM_RANGE = 0.08; // الزووم بيوصل لـ 1.05 + 0.08 = 1.13 في آخر الفيديو (وضع الصورة الاحتياطي بس)

let bgImage = null;      // بيتملى (لو استُخدم الاحتياطي بالصورة) جوه prepareIdentity()
let brollSampler = null; // بيتملى (لو BACKGROUND_VIDEO_URL موجود وصالح) جوه prepareIdentity()

// تحميل صورة واحدة، بانتظار حقيقي (Promise)
function loadImage(url) {
    return new Promise((resolve, reject) => {
        const img = new Image();
        img.crossOrigin = "anonymous";
        img.onload = () => resolve(img);
        img.onerror = () => reject(new Error(`تعذر تحميل الصورة: ${url}`));
        img.src = url;
    });
}

// دالة "cover" عامة لرسم صورة أو فريم فيديو يملأ مستطيل معيّن من غير تشويه نسبته (زي CSS background-size:cover)
function drawMediaCover(el, dx, dy, dw, dh, radius = 0, zoom = 1) {
    const dims = { w: el.naturalWidth || el.width, h: el.naturalHeight || el.height };
    const scale = Math.max(dw / dims.w, dh / dims.h) * zoom;
    const sw = dw / scale, sh = dh / scale;
    const sx = (dims.w - sw) / 2, sy = (dims.h - sh) / 2;
    ctx.save();
    if (radius > 0) {
        ctx.beginPath();
        ctx.roundRect(dx, dy, dw, dh, radius);
        ctx.clip();
    }
    ctx.drawImage(el, sx, sy, sw, sh, dx, dy, dw, dh);
    ctx.restore();
}

async function drawGlobalBackground(time) {
    // 1) خلفية احتياطية أخيرة: تدرج رمادي فاتح (يظهر دايمًا كطبقة أولى، وبيتغطى بالفيديو/الصورة لو موجودة)
    const grad = ctx.createLinearGradient(0, 0, 0, CONFIG.height);
    grad.addColorStop(0, PALETTE.bgFallbackTop);
    grad.addColorStop(1, PALETTE.bgFallbackBottom);
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, CONFIG.width, CONFIG.height);

    // 2) فيديو B-roll حقيقي متحرك (أولوية أولى لو الـ sampler جاهز)
    if (brollSampler) {
        try {
            const frame = await brollSampler.getFrameAt(time);
            if (frame) drawMediaCover(frame, 0, 0, CONFIG.width, CONFIG.height, 0, 1);
        } catch (err) {
            // فشل سحب فريم لحظي بس — سيب التدرج الاحتياطي لهذا الفريم واستمر، مش لازم يوقف التصدير كله
        }
    }
    // 3) صورة ثابتة بحركة Ken Burns (أولوية ثانية، بس لو مفيش فيديو خالص)
    else if (bgImage) {
        const totalProgress = clamp01(time / CONFIG.duration);
        const zoom = KEN_BURNS_START_ZOOM + (totalProgress * KEN_BURNS_ZOOM_RANGE);
        drawMediaCover(bgImage, 0, 0, CONFIG.width, CONFIG.height, 0, zoom);
    }

    // 4) فينييت داكنة فوق أي خلفية (فيديو أو صورة أو تدرج) — عشان النص الأبيض يفضل واضح دايمًا
    const vignette = ctx.createRadialGradient(
        CONFIG.width / 2, CONFIG.height / 2, CONFIG.width / 4,
        CONFIG.width / 2, CONFIG.height / 2, CONFIG.height / 1.5
    );
    vignette.addColorStop(0, PALETTE.vignetteInner);
    vignette.addColorStop(1, PALETTE.vignetteOuter);
    ctx.fillStyle = vignette;
    ctx.fillRect(0, 0, CONFIG.width, CONFIG.height);
}

function drawTopHeader(time) {
    const fadeInDuration = 1.5;
    const alpha = clamp01(time / fadeInDuration);

    ctx.save();
    ctx.globalAlpha = alpha;

    ctx.shadowColor = PALETTE.shadowHeader;
    ctx.shadowBlur = 10;
    ctx.shadowOffsetX = 0;
    ctx.shadowOffsetY = 3;

    ctx.fillStyle = PALETTE.textPrimary;
    ctx.font = `700 48px ${HEADER_FONT}`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(SURAH_DISPLAY_NAME, CONFIG.width / 2, 120);

    ctx.fillStyle = PALETTE.textMuted;
    ctx.font = `500 26px ${HEADER_FONT}`;
    ctx.fillText(RECITER_DISPLAY_NAME, CONFIG.width / 2, 180);

    ctx.strokeStyle = PALETTE.dividerLine;
    ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.moveTo(CONFIG.width / 2 - 80, 220);
    ctx.lineTo(CONFIG.width / 2 + 80, 220);
    ctx.stroke();

    ctx.restore();
}

// ================================================================
// 🎨 الدالتين الإلزاميتين في العقد
// ================================================================
async function prepareIdentity() {
    // أولوية 1: فيديو B-roll حقيقي
    if (BACKGROUND_VIDEO_URL) {
        try {
            brollSampler = await createBrollFrameSampler(BACKGROUND_VIDEO_URL, {
                width: CONFIG.width,
                height: CONFIG.height,
                fit: 'cover'
            });
            logToConsole("تم تجهيز فيديو الخلفية (B-roll) بنجاح ✓");
        } catch (err) {
            logToConsole("تعذر تجهيز فيديو الخلفية، هيتم تجربة الصورة الاحتياطية إن وجدت. " + err.message, "warn");
        }
    }
    // أولوية 2: صورة ثابتة (بس لو الفيديو فشل أو مش موجود أصلًا)
    if (!brollSampler && BACKGROUND_IMAGE_URL) {
        try {
            bgImage = await loadImage(BACKGROUND_IMAGE_URL);
        } catch (err) {
            logToConsole("تعذر تحميل صورة الخلفية، هيتم الاكتفاء بالتدرج اللوني. " + err.message, "warn");
        }
    }

    // علم صريح لحالة الخلفية النهائية — بيقرأه render-runner.js بعد التصدير
    // عشان "نجاح التصدير" ميتلخبطش مع "نجاح تحميل الخلفية فعليًا". فشل صامت
    // في الخلفية لازم يبان بوضوح، مش يتوه وسط نجاح التصدير الشكلي.
    window.__backgroundStatus = brollSampler ? 'video' : (bgImage ? 'image' : 'fallback_gradient');
    if (window.__backgroundStatus === 'fallback_gradient') {
        logToConsole("⚠️ فشل تحميل أي خلفية (فيديو ولا صورة) — الفيديو هيتصدّر بتدرج رمادي بس من غير أي خلفية حقيقية.", 'error');
    } else {
        logToConsole(`الخلفية جاهزة (${window.__backgroundStatus === 'video' ? 'فيديو B-roll' : 'صورة ثابتة'}) ✓`);
    }

    logToConsole(`جاري تحميل صوت آيات ${SURAH_DISPLAY_NAME} آية بآية من EveryAyah.com (${RECITER_DISPLAY_NAME})...`);

    const ayahBuffers = [];
    for (let i = 1; i <= SURAH_VERSES.length; i++) {
        const ayahNum = String(i).padStart(3, '0');
        const url = `https://www.everyayah.com/data/${RECITER_ID}/${SURAH_NUMBER}${ayahNum}.mp3`;
        try {
            ayahBuffers.push(await fetchAndDecodeAudio(url));
            logToConsole(`تم تحميل الآية ${i} بنجاح ✓`);
        } catch (err) {
            logToConsole(`تنبيه تحميل الآية ${i}: ${err.message}`, 'warn');
        }
    }
    if (ayahBuffers.length === 0) throw new Error("تعذر جلب أي ملف صوت من EveryAyah");

    const { buffer, segments } = concatenateAudioBuffers(ayahBuffers);
    audioBuffer = buffer;
    CONFIG.duration = audioBuffer.duration;

    const cues = segments.map((seg, i) => ({ id: i + 1, ...seg, ...SURAH_VERSES[i] }));
    parsedScenes = cues.map(cue => {
        const fontSize = cue.text.length > 30 ? 52 : 60;
        const font = `700 ${fontSize}px ${QURAN_FONT}`;
        const words = layoutArabicParagraph(cue.text, font, 580, 14, fontSize * 1.5, CONFIG.height / 2);
        return { ...cue, font, words };
    });

    logToConsole(`تم دمج تلاوة الآيات بنجاح! مدة الشورتس: ${CONFIG.duration.toFixed(2)} ثانية ✓`);
}

async function drawSceneAtTime(time) { // async دايمًا (راجع "عقد ملف هوية .md" بـ AGENTS.md)
    state.currentTime = time;
    await drawGlobalBackground(time); // await إلزامي دلوقتي: سحب فريم الفيديو (getFrameAt) عملية غير متزامنة
    drawTopHeader(time);

    // fallback لآخر مشهد لو الوقت عدّى نهاية آخر آية بهامش صغير
    const activeScene = parsedScenes.find(s => time >= s.start && time < s.end)
        || parsedScenes[parsedScenes.length - 1];
    if (!activeScene) return;

    const sceneDuration = activeScene.end - activeScene.start;
    const localTime = time - activeScene.start;

    const fadeInDuration = 0.6;
    const fadeOutDuration = 0.5;

    let progressFactor = 1.0;
    if (localTime < fadeInDuration) {
        progressFactor = localTime / fadeInDuration;
    } else if (localTime > sceneDuration - fadeOutDuration) {
        progressFactor = (sceneDuration - localTime) / fadeOutDuration;
    }

    const b = Easing.easeOutCubic(clamp01(progressFactor));
    const alpha = b;
    const offsetY = (1.0 - b) * 20;
    const scale = 0.96 + (0.04 * b);

    ctx.save();
    ctx.globalAlpha = alpha;

    const centerX = CONFIG.width / 2;
    const centerY = CONFIG.height / 2;
    ctx.translate(centerX, centerY - offsetY);
    ctx.scale(scale, scale);
    ctx.translate(-centerX, -centerY);

    ctx.shadowColor = PALETTE.shadowVerse;
    ctx.shadowBlur = 15;
    ctx.shadowOffsetX = 0;
    ctx.shadowOffsetY = 4;

    ctx.fillStyle = PALETTE.textPrimary;
    ctx.font = activeScene.font;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';

    activeScene.words.forEach(word => {
        ctx.fillText(word.text, word.x, word.y);
    });

    ctx.restore();
}
```

## ملاحظات معروفة

**تحديث النسخة الخامسة (هذا الملف) — سبب التعديل**: في مهمة فعلية استخدمت
النسخة الرابعة، الـ Agent اختار
`https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerBlazes.mp4`
كخلفية، وفحص `curl -sI` عليه رجع CORS سليم — لكن وقت التصدير الفعلي (طلب
`GET`/streaming حقيقي من المتصفح) السيرفر رفض بـ `403 Forbidden`. بما إن تحميل
الخلفية ملفوف في `try/catch`، الفشل اتمسك بهدوء والتصدير كمّل عادي وطلع فيديو
"ناجح" تقنيًا (ملف MP4 اتصدّر واترفع) لكن **من غير أي خلفية خالص** (تدرج رمادي
+ فينييت بس) — ومفيش أي مؤشر واضح في مخرجات التصدير يلفت النظر للمشكلة دي.
بالتوازي، نفس المهمة ضاعت فيها دقايق تكتشف من الصفر إن `Muhammad_Ayyub_128kbps`
غلط (الصح `Muhammad_Ayyoub_128kbps`) رغم إن ده اتكشف واتصحح في مهمة سابقة على
نفس الهوية. النسخة دي بتعالج الاثنين: تحذير صريح من buckets العيّنات التجريبية
(راجع "منطق اختيار وجلب فيديو الخلفية")، وعلم `window.__backgroundStatus`
(راجع نهاية `prepareIdentity()`) يخلي فشل الخلفية واضح فورًا مش مطمور جوه نجاح
التصدير الشكلي، ومرجع ثابت لأسماء مجلدات القراء الصح/الغلط عشان الوقت ميضيعش
تاني.

**تحديث النسخة الرابعة — سبب التعديل**: في النسخة الثالثة كان
`BACKGROUND_IMAGE_URL = null` قيمة **معطّلة بشكل دائم بتصميم**، والغرض منها
كان "تشتغل الهوية من غير أي اعتماد على رابط خارجي غير مضمون". النتيجة العملية
كانت إن أي مهمة فعلية بتستخدم الهوية دي كانت بتطلع بخلفية فاضية (تدرج رمادي +
فينييت بس) لأن محدش كان بيغيّر القيمة دي فعليًا وقت التنفيذ. النسخة دي بتقلب
المنطق: **الخلفية بقت مصدرها فيديو B-roll حقيقي إلزامي يجيبه الـ Agent بنفسه**
من مصدر مجاني (Pexels/Pixabay/غيره) في كل مهمة (تفاصيل الآلية في "منطق اختيار
وجلب فيديو الخلفية" أول الملف)، والصورة الثابتة والتدرج بقوا مجرد شبكة أمان
(fallback) لو الفيديو مش متاح، مش الخيار الافتراضي.

**تصحيحات عن نسخة أقدم من هذا الملف** (لو كنت شفت نسخة "أفق الليلية" قبل كده
بألوان كحلي/ذهبي — دي كانت استخراج أول أقل دقة، اتلغت واتستبدلت):
- **الألوان الحقيقية في الأصل مفيهاش أي "ذهبي" (`#D4AF37`) ولا خلفية كحلي
  داكنة خالص** — الخلفية الاحتياطية الحقيقية في الملف الأصلي **فاتحة**
  (`#FFFFFF → #E5E5E5`)، والغمقان اللي بيدّي طابع "سينمائي داكن" مصدره
  **الفينييت السوداء فوقها بس** (`rgba(0,0,0,0.20)` في المنتصف لـ
  `rgba(0,0,0,0.75)` في الأطراف)، مش لون خلفية داكن مرسوم مباشرة.
- **الـ HUD/الأزرار في الأصل فاتحة/بيضاء** (زجاج شفاف أبيض، نص أسود) — **مش
  داكنة**. الغمقان في التصميم خاص بمحتوى الفيديو (الكانفاس) بس.
- زر التصدير أسود (`#111111`) مش ذهبي.

**عناصر من الملف الأصلي استُبعدت عمدًا** (ومش هتلاقيها هنا):
- **`getWorkingCodecConfig()`**: منطق تصدير/تنفيذ صرف — مستبعدة بالكامل حسب
  قاعدة "المنطق التنفيذي الخام ما بينتقلش أبدًا" في القسم 1.5. الطبقة التقنية
  الثابتة في `AGENTS.md` بتحقق نفس الهدف بآلية تانية (قائمة محاولات مرتبة مع
  `try/catch` على كل محاولة كاملة).
- **مصدر الصوت (`quranicaudio.com`, ملف واحد للسورة كاملة) وتوقيت `RAW_CUES`
  اليدوي**: مستبعدين، وبدل منهم جلب صوت آية بآية من `everyayah.com` عن طريق
  `fetchAndDecodeAudio`/`concatenateAudioBuffers`.
- **`ensureFontsLoaded()`**: مستبعدة لتبسيط الواجهة بين طبقة الهوية والطبقة
  التقنية (مفيش hook جاهز ليها في الطبقة التقنية الثابتة حاليًا). عمليًا مش
  بتبقى مشكلة لأن تحميل الصوت والفيديو جوه `prepareIdentity()` بياخد وقت كافي
  بحيث خطوط Google Fonts بتكون خلصت تحميل قبل أول فريم فعلي بيتصدّر.
- **GSAP**: محمّلة في رأس الملف الأصلي لكن مش مستخدمة فعليًا — كود ميت، مش منقولة هنا.
- **تعطيل الأزرار (`disabled`) لحد ما الأصول تتحمّل**: الهيكل الثابت في القسم 2
  من `AGENTS.md` مفيهوش الخاصية دي على الأزرار من الأساس — مش هتتضاف هنا.

**تصحيح حقيقي في الملف الأصلي نفسه** (مش استبعاد، ده باگ فعلي اتصحح هنا):
- CSS الأصلي لـ `#console-modal` فيه `display: none;` **و** `display: flex;`
  مع بعض في نفس الـ rule — في CSS آخر قيمة مكررة هي اللي بتكسب، يعني عمليًا
  كان هيخلي نافذة سجل الأخطاء **ظاهرة بشكل افتراضي** فوق الفيديو من أول ما
  الصفحة تفتح. الكود هنا سايب `display: none;` بس.

**ملاحظات تصميم إضافية**:
- **الحركة هنا "كتلة واحدة" مش كلمة كلمة**: بعكس `brown-style.md`/`Quran.md`
  اللي بيعملوا "ظهور متتابع" لكل كلمة لوحدها، هنا الآية كلها بتتحرك كوحدة واحدة.
- **الفيديو/الصورة بتتحمّل بانتظار حقيقي (`await`) جوه `prepareIdentity()`**
  (مش fire-and-forget) — يعني لو الفيديو أو الصورة موجودة، أول فريم بيترسم
  هيلاقيها جاهزة فعليًا، مش هتظهر فجأة بعد أول كام فريم.
- **`getFrameAt(time)` بتتنادى مرة لكل فريم وقت التصدير الحقيقي** (قد يكون
  مئات المرات على حسب مدة المقطع وعدد الفريمات في الثانية) — دي نفس آلية
  `createBrollFrameSampler` المستخدمة في هويات المشروع الأخرى اللي بتدعم فيديو
  خلفية، فمفيش أي حمل إضافي غير معتاد على الطبقة التقنية.
- **مركز الآية على `CONFIG.height / 2` بالظبط** (نص الشاشة الحقيقي، بعكس
  `brown-style.md` اللي بيزيح المركز لفوق عشان يسيب مكان لبطاقة تفسير) — مفيش
  بطاقة تفسير هنا فمفيش داعي للإزاحة.
