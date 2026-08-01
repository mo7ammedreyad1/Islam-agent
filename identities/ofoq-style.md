# هوية "أفق الليلية" (Ofoq Night) — سينمائية، خلفية صورة اختيارية بحركة Ken Burns

## الوصف
طابع سينمائي هادئ: **واجهة تحكم فاتحة/بيضاء** (زجاجية شفافة، مطابقة تمامًا
لباقي هويات المشروع في شكل الـ HUD/الأزرار) تحيط بفيديو **محتواه غامق ومظلم**
— التباين ده مقصود في التصميم الأصلي ومش خطأ. المحتوى نفسه: خلفية إما صورة
حقيقية بحركة تكبير بطيئة (تقنية Ken Burns) أو تدرج رمادي فاتح احتياطي، وفوقها
**فينييت داكنة** بتخلي أي نص أبيض فوقها واضح مهما كانت الخلفية. هيدر ثابت
باسم السورة والقارئ أعلى الشاشة بخط Reem Kufi، والآية نفسها في المنتصف بحركة
**كتلة واحدة** (مش كلمة كلمة) fade + scale + إزاحة خفيفة لأعلى. دقة 720×1280
(أصغر من باقي الهويات، بس نفس نسبة 9:16). مفيش بانل تفسير — تلاوة minimal بس.

- **شكل المحتوى**: آيات قرآنية بس، بدون تفسير.
- **الأصول**: صوت تلاوة آية بآية من `everyayah.com` (إلزامي). صورة خلفية
  اختيارية بحركة Ken Burns (معطّلة افتراضيًا).
- **الأبعاد**: 720×1280 (Shorts عمودي، 9:16، دقة أصغر من باقي الهويات)، 60fps.
- **حالة الاستخدام**: تلاوة قصيرة/سورة صغيرة، طابع سينمائي هادئ بدل الرَّق الكلاسيكي.

> **نشأة الهوية دي**: اتبنت بمنطق "استيعاب ستايل فقط" (القسم 1.5 في
> `AGENTS.md`) من ملف خارجي (`ai_studio_code_-_2026-07-27T072109_860.html`،
> بعنوان داخلي "Ofoq Studio - Surah Al-Ikhlas Shorts 720p") فشل في 4 من 6 بنود
> فحص التوافق (صفر render hooks، مفيش AAC polyfill، صوت من `quranicaudio.com`
> بدل `everyayah.com`، وتوقيت آيات مكتوب يدويًا). القيم اللي هنا (الألوان،
> الخطوط، حركة Ken Burns) مطابقة للملف الأصلي بالحرف بعد مراجعة دقيقة —
> راجع "ملاحظات معروفة" آخر الصفحة لتفاصيل الفروقات عن استخراج أول أقل دقة.

## روابط الخطوط
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400;1,700&family=Reem+Kufi:wght@400..700&family=IBM+Plex+Sans+Arabic:wght@100;200;300;400;500;600;700;800&display=swap" rel="stylesheet">
```

## CSS كامل
**ملاحظة**: الـ HUD والأزرار وسجل الأخطاء هنا **فاتحة/بيضاء** — مطابقة لباترن
باقي هويات المشروع، مش داكنة زي محتوى الفيديو نفسه.

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

## كود JS — الهوية بالكامل (محتوى + أصول + تصميم)
انسخ الكتلة دي حرفيًا في مكان "🎨 كود ملف الهوية بالكامل" جوه `scene.html`.

```js
// ================================================================
// ⬛ منطقة قابلة للتعديل لكل فيديو جديد — غيّر هنا بس
// ================================================================
const SURAH_NUMBER = '112';                 // رقم السورة، 3 أرقام، مستخدم في رابط الصوت
const RECITER_ID = 'Alafasy_128kbps';       // مجلد القارئ في everyayah.com
const RECITER_DISPLAY_NAME = 'تلاوة الشيخ مشاري راشد العفاسي';
const SURAH_DISPLAY_NAME = 'سُورَةُ الْإِخْلَاصِ';
const OUTPUT_FILENAME = 'Quran_Shorts_Al_Ikhlas_Ofoq';
const SURAH_VERSES = [
    // نص كل آية (text) — من نتيجة curl فعلية على api.alquran.cloud
    // (edition=quran-uthmani)، مش من الذاكرة.
    { text: 'قُلْ هُوَ اللَّهُ أَحَدٌ ۝١' },
    { text: 'اللَّهُ الصَّمَدُ ۝٢' },
    { text: 'لَمْ يَلِدْ وَلَمْ يُولَدْ ۝٣' },
    { text: 'وَلَمْ يَكُن لَّهُ كُفُوًا أَحَدٌ ۝٤' }
];

// خلفية صورة اختيارية بحركة Ken Burns — افتراضيًا معطّلة (null) عشان الهوية
// تشتغل من غير أي اعتماد على رابط خارجي غير مضمون الاستمرارية. لو عايز
// تفعّلها لفيديو معيّن، حط رابط صورة CORS-safe هنا (تحقق بـ `curl -sI` إن فيه
// header اسمه access-control-allow-origin قبل ما تستخدمه).
const BACKGROUND_IMAGE_URL = null;
// ================================================================

let CONFIG = { fps: 60, width: 720, height: 1280, duration: 22.0 };

const QURAN_FONT = "'Amiri', 'Georgia', 'Times New Roman', serif";
const HEADER_FONT = "'Reem Kufi', sans-serif";

const PALETTE = {
    bgFallbackTop: "#FFFFFF",
    bgFallbackBottom: "#E5E5E5",
    vignetteInner: "rgba(0, 0, 0, 0.20)",
    vignetteOuter: "rgba(0, 0, 0, 0.75)",
    textPrimary: "#FFFFFF",
    textMuted: "rgba(255, 255, 255, 0.85)",
    dividerLine: "rgba(255, 255, 255, 0.3)",
    shadowHeader: "rgba(0, 0, 0, 0.65)",
    shadowVerse: "rgba(0, 0, 0, 0.8)"
};

const KEN_BURNS_START_ZOOM = 1.05;
const KEN_BURNS_ZOOM_RANGE = 0.08; // الزووم بيوصل لـ 1.05 + 0.08 = 1.13 في آخر الفيديو

let audioBuffer = null;   // بيتحدد جوه prepareIdentity()
let parsedScenes = [];    // بيتحدد جوه prepareIdentity()
let bgImage = null;       // بيتحدد جوه prepareIdentity() لو BACKGROUND_IMAGE_URL موجود

async function fetchAyahAudio(ayahIndex) {
    const ayahNum = String(ayahIndex).padStart(3, '0');
    const url = `https://www.everyayah.com/data/${RECITER_ID}/${SURAH_NUMBER}${ayahNum}.mp3`;
    const res = await fetch(url);
    const arrayBuf = await res.arrayBuffer();
    if (arrayBuf.byteLength < 5000) {
        throw new Error(`حجم غير طبيعي (${arrayBuf.byteLength} بايت) للآية ${ayahIndex} — راجع الرابط`);
    }
    return arrayBuf;
}

// تحميل صورة الخلفية الاختيارية — async حقيقي (متفرّع عن prepareIdentity)،
// فشلها لا يوقف تجهيز باقي الهوية (fallback تلقائي للتدرج اللوني)
function loadOptionalBackgroundImage() {
    if (!BACKGROUND_IMAGE_URL) return Promise.resolve();
    return new Promise((resolve) => {
        const img = new Image();
        img.crossOrigin = "anonymous";
        img.onload = () => { bgImage = img; resolve(); };
        img.onerror = () => {
            logToConsole("تعذر تحميل صورة الخلفية، هيتم الاكتفاء بالتدرج اللوني.", "warn");
            resolve();
        };
        img.src = BACKGROUND_IMAGE_URL;
    });
}

async function prepareIdentity() {
    logToConsole(`جاري تحميل صوت آيات ${SURAH_DISPLAY_NAME} آية بآية من EveryAyah.com (${RECITER_DISPLAY_NAME})...`);
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    const ayahBuffers = [];
    let totalSamples = 0;

    // الصوت والصورة الاختيارية بيتحمّلوا بالتوازي — الصورة مش هتأخر التلاوة
    const imageLoadPromise = loadOptionalBackgroundImage();

    for (let i = 1; i <= SURAH_VERSES.length; i++) {
        const arrayBuf = await fetchAyahAudio(i);
        const decodedBuf = await audioCtx.decodeAudioData(arrayBuf);
        ayahBuffers.push(decodedBuf);
        totalSamples += decodedBuf.length;
        logToConsole(`تم تحميل الآية ${i} بنجاح ✓`);
    }

    if (ayahBuffers.length === 0) throw new Error("تعذر جلب ملفات الصوت من EveryAyah");

    const sampleRate = ayahBuffers[0].sampleRate;
    const channelsCount = ayahBuffers[0].numberOfChannels;
    audioBuffer = audioCtx.createBuffer(channelsCount, totalSamples, sampleRate);

    let sampleOffset = 0;
    let timeOffset = 0.0;
    const rawCues = [];

    for (let i = 0; i < ayahBuffers.length; i++) {
        const buf = ayahBuffers[i];
        for (let ch = 0; ch < channelsCount; ch++) {
            audioBuffer.getChannelData(ch).set(buf.getChannelData(ch), sampleOffset);
        }
        const duration = buf.duration;
        rawCues.push({ id: i + 1, start: timeOffset, end: timeOffset + duration, ...SURAH_VERSES[i] });
        sampleOffset += buf.length;
        timeOffset += duration;
    }

    logToConsole(`تم دمج تلاوة الآيات بنجاح! مدة الشورتس: ${timeOffset.toFixed(2)} ثانية ✓`);

    parsedScenes = rawCues.map(cue => {
        const fontSize = cue.text.length > 30 ? 52 : 60;
        const font = `700 ${fontSize}px ${QURAN_FONT}`;
        const words = layoutArabicParagraph(cue.text, font, 580, 14, fontSize * 1.5, CONFIG.height / 2);
        return { ...cue, font, words };
    });

    await imageLoadPromise; // نتأكد إن الصورة (لو موجودة) خلصت قبل أول رسمة فعلية
}

// دالة "cover" عامة لرسم صورة تملأ مستطيل معيّن من غير تشويه نسبتها (زي CSS background-size:cover)
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

function drawGlobalBackground(time) {
    const grad = ctx.createLinearGradient(0, 0, 0, CONFIG.height);
    grad.addColorStop(0, PALETTE.bgFallbackTop);
    grad.addColorStop(1, PALETTE.bgFallbackBottom);
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, CONFIG.width, CONFIG.height);

    if (bgImage) {
        const totalProgress = clamp01(time / CONFIG.duration);
        const zoom = KEN_BURNS_START_ZOOM + (totalProgress * KEN_BURNS_ZOOM_RANGE);
        drawMediaCover(bgImage, 0, 0, CONFIG.width, CONFIG.height, 0, zoom);
    }

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

async function drawSceneAtTime(time) {
    drawGlobalBackground(time);
    drawTopHeader(time);

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
- **الألوان الحقيقية في الأصل مفيهاش أي "ذهبي" ولا خلفية كحلي داكنة خالص** —
  الخلفية الاحتياطية فاتحة (`#FFFFFF → #E5E5E5`)، والغمقان السينمائي مصدره
  **الفينييت السوداء فوقها بس**. لو شفت نسخة أقدم بألوان كحلي/ذهبي، دي كانت
  استخراج أول أقل دقة، اتلغت.
- **الصوت والصورة الاختيارية بيتحمّلوا بالتوازي** جوه `prepareIdentity`
  (`loadOptionalBackgroundImage()` بتتنادى من غير `await` فوري، والنتيجة
  بتتستنى في الآخر بس) — عشان الصورة (لو مفعّلة) ماتأخّرش تحميل التلاوة.
- **مفيش بانل تفسير في الهوية دي أصلًا** — لو مهمة طلبت تفسير بهذا الستايل،
  محتاج تضيف حقل `tafseer` لكل عنصر في `SURAH_VERSES` وترسمه بمنطق شبيه بلي
  في `identities/brown-style.md`، بس بألوان تتماشى مع الخلفية الداكنة هنا.
- **أبعاد الكانفاس 720×1280 مش 1080×1920** (نفس نسبة 9:16 بس دقة أقل) — لو
  محتاج دقة أعلى، غيّر `CONFIG.width`/`CONFIG.height` في `<canvas>` وفي الـ JS
  مع بعض، وراجع إن قيم `layoutArabicParagraph` جوه `prepareIdentity` اتناسبت
  مع الأبعاد الجديدة.
- **CSS الأصلي لـ`#console-modal` كان فيه `display: none;` و`display: flex;`
  مكررين في نفس القاعدة** (باگ حقيقي في الملف الأصلي كان هيخلي نافذة السجل
  ظاهرة افتراضيًا) — اتصحح هنا، سايب `display: none;` بس.
- **عناصر اتستبعدت عمدًا من الملف الأصلي**: دالة `getWorkingCodecConfig()`
  (فحص كودك مسبق — منطق تصدير صرف، مستبعد حسب قاعدة "الكود التنفيذي ما
  بينتقلش")، `ensureFontsLoaded()` (انتظار تحميل الخطوط — غير ضروري عمليًا
  لأن تحميل الصوت بياخد وقت كافي)، ومكتبة GSAP (كانت محمّلة في الأصل لكن
  مفيهاش أي استخدام فعلي — كود ميت).
