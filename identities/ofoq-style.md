# هوية "أفق الليلية" (Ofoq Night) — سينمائية، خلفية صورة اختيارية بحركة Ken Burns

## الوصف
طابع سينمائي هادئ: **واجهة تحكم فاتحة/بيضاء** (زجاجية شفافة، مطابقة تمامًا
لباقي هويات المشروع في شكل الـ HUD/الأزرار) تحيط بفيديو **محتواه غامق ومظلم**
— التباين ده مقصود في التصميم الأصلي ومش خطأ. المحتوى نفسه: خلفية إما صورة
حقيقية بحركة تكبير بطيئة (تقنية Ken Burns) أو تدرج رمادي فاتح احتياطي، وفوقها
**فينييت داكنة** (تعتيم تدريجي من المنتصف للأطراف) بتخلي أي نص أبيض فوقها واضح
مهما كانت الخلفية. هيدر ثابت باسم السورة والقارئ أعلى الشاشة بخط Reem Kufi،
والآية نفسها في المنتصف بحركة **كتلة واحدة** (مش كلمة كلمة) fade + scale + إزاحة
خفيفة لأعلى. دقة 720×1280 (أصغر من باقي الهويات، بس نفس نسبة 9:16). مفيش بانل
تفسير — تلاوة minimal بس.

> **نشأة الهوية دي ونسختها**: اتبنت بمنطق "استيعاب هوية من ملف خارجي" (القسم
> 1.5 في `AGENTS.md`) من ملف خارجي (`ai_studio_code_-_2026-07-27T072109_860.html`،
> بعنوان داخلي "Ofoq Studio - Surah Al-Ikhlas Shorts 720p"). **هذا الملف هو
> النسخة الثالثة** لهذه الهوية — استُخرج الستايل البصري الأصلي بمراجعة كل سطر
> في الملف الأصلي وقيمة كل لون/رقم/دالة بالحرف (مش تقريب أو إعادة تخيّل)، ثم
> أُعيد بناء منطق جلب الصوت والصورة وبيانات المحتوى بالكامل بما يتماشى مع
> "عقد ملف هوية `.md`" ذي الطبقتين (self-contained، `prepareIdentity` +
> `drawSceneAtTime` غير متزامنتين). الفروق عن أي نسخة أقدم من هذا الملف موثّقة
> في "ملاحظات معروفة" آخر الصفحة.

**فحص التوافق مع الرانر (القسم 1.5)** — الملف الأصلي فشل في 4 من 6 بنود:
- ✅ يستخدم Mediabunny صح (`Output`/`CanvasSource`/`AudioBufferSource`).
- ✅ الصورة الخلفية بتتحمّل بـ `img.crossOrigin = "anonymous"` بشكل صحيح تقنيًا.
- ❌ صفر render hooks (`renderStatus`, `renderProgress`, `startVideoRender`,
  `?autorender=true`, `video-render-complete`, `__renderFilename`/`__renderBase64`).
- ❌ مفيش AAC encoder polyfill — هيفشل على الـ CI.
- ❌ الصوت ملف واحد كامل من `quranicaudio.com`، مش آية بآية من `everyayah.com`.
- ❌ توقيت الآيات (`RAW_CUES`) أرقام مكتوبة يدويًا بالتخمين، مش محسوبة من تقطيع
  صوتي حقيقي (حتى لو `CONFIG.duration` الكلية كانت فعلًا بتتحسب من
  `audioBuffer.duration` — التناقض ده بالظبط سبب رقم 5 في القسم 1.5: قرار
  تنفيذي متسرّب جوه حاجة شكلها "بيانات مشهد").

نتيجة الفحص لا تغيّر القرار (استخراج ستايل + منطق جلب أصول مبني من الصفر، زي
أي ملف بيفشل بند واحد على الأقل) — لكنها بتوضح إن فصل التصميم عن التنفيذ هنا
كان مباشر نسبيًا لأن الملف منظّم كويس أصلًا.

- **الأبعاد**: 720×1280 (Shorts عمودي، 9:16، دقة أصغر من باقي الهويات)، 60fps.
- **حالة الاستخدام**: تلاوة قصيرة/سورة صغيرة، طابع سينمائي هادئ بدل الرَّق الكلاسيكي.
- **شكل بيانات المحتوى** (`SURAH_NUMBER`, `RECITER_ID`, `RECITER_DISPLAY_NAME`,
  `SURAH_DISPLAY_NAME`, `OUTPUT_FILENAME`, `SURAH_VERSES`): كل عنصر في
  `SURAH_VERSES` فيه `text` بس، مفيش حاجة إضافية مطلوبة. الصوت بيتجاب من
  `everyayah.com` آية بآية داخل `prepareIdentity()` بنفس باترن الهويات التانية
  في المشروع (راجع الكود تحت).

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
الحالية (نتيجة `curl` فعلية).

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
    bgFallbackTop: "#FFFFFF",           // خلفية احتياطية فاتحة (لو مفيش صورة أو لسه مبتحملتش)
    bgFallbackBottom: "#E5E5E5",
    vignetteInner: "rgba(0, 0, 0, 0.20)",  // فينييت: تعتيم خفيف في المنتصف
    vignetteOuter: "rgba(0, 0, 0, 0.75)",  // فينييت: تعتيم قوي في الأطراف
    textPrimary: "#FFFFFF",
    textMuted: "rgba(255, 255, 255, 0.85)",
    dividerLine: "rgba(255, 255, 255, 0.3)",
    shadowHeader: "rgba(0, 0, 0, 0.65)",
    shadowVerse: "rgba(0, 0, 0, 0.8)"
};

// --- خلفية صورة اختيارية بحركة Ken Burns (تكبير بطيء طول مدة الفيديو) ---
// افتراضيًا متعطّلة (null) عشان الهوية تشتغل من غير أي اعتماد على رابط صورة
// خارجي غير مضمون الاستمرارية. الرابط الأصلي في ملف "Ofoq Studio" كان:
//   https://images.unsplash.com/photo-1470071459604-3b5ec3a7fe05?q=80&w=1920&auto=format&fit=crop
// لو عايز تفعّل وضع الصورة لفيديو معيّن، حط رابط صورة CORS-safe هنا (لازم
// تتأكد إنه بيرجّع header يسمح بالوصول من مصدر مختلف، وإلا الكانفاس هيتعتبر
// "tainted" ويفشل التصدير — راجع قاعدة CORS في القسم 2 من AGENTS.md).
const BACKGROUND_IMAGE_URL = null;
const KEN_BURNS_START_ZOOM = 1.05;
const KEN_BURNS_ZOOM_RANGE = 0.08; // الزووم بيوصل لـ 1.05 + 0.08 = 1.13 في آخر الفيديو

let bgImage = null; // بيتملى (لو BACKGROUND_IMAGE_URL موجود) جوه prepareIdentity()، بانتظار فعلي

// تحميل صورة واحدة، بانتظار حقيقي (Promise) — بديل نمط fire-and-forget القديم
function loadImage(url) {
    return new Promise((resolve, reject) => {
        const img = new Image();
        img.crossOrigin = "anonymous";
        img.onload = () => resolve(img);
        img.onerror = () => reject(new Error(`تعذر تحميل الصورة: ${url}`));
        img.src = url;
    });
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
    // 1) خلفية احتياطية: تدرج رمادي فاتح (يظهر دايمًا كطبقة أولى، وبيتغطى بالصورة لو موجودة)
    const grad = ctx.createLinearGradient(0, 0, 0, CONFIG.height);
    grad.addColorStop(0, PALETTE.bgFallbackTop);
    grad.addColorStop(1, PALETTE.bgFallbackBottom);
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, CONFIG.width, CONFIG.height);

    // 2) خلفية صورة اختيارية بحركة Ken Burns (بس لو bgImage اتحمّلت فعلًا)
    if (bgImage) {
        const totalProgress = clamp01(time / CONFIG.duration);
        const zoom = KEN_BURNS_START_ZOOM + (totalProgress * KEN_BURNS_ZOOM_RANGE);
        drawMediaCover(bgImage, 0, 0, CONFIG.width, CONFIG.height, 0, zoom);
    }

    // 3) فينييت داكنة فوق أي خلفية (صورة أو تدرج) — عشان النص الأبيض يفضل واضح دايمًا
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
    if (BACKGROUND_IMAGE_URL) {
        try {
            bgImage = await loadImage(BACKGROUND_IMAGE_URL);
        } catch (err) {
            logToConsole("تعذر تحميل صورة الخلفية، هيتم الاكتفاء بالتدرج اللوني. " + err.message, "warn");
        }
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

async function drawSceneAtTime(time) { // async دايمًا (راجع "عقد ملف هوية .md" بـ AGENTS.md)، حتى لو مفيش await فعلي هنا
    state.currentTime = time;
    drawGlobalBackground(time);
    drawTopHeader(time);

    // fallback لآخر مشهد لو الوقت عدّى نهاية آخر آية بهامش صغير (تحسين بسيط
    // عن الأصلي — راجع "ملاحظات معروفة")
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

**تصحيحات عن نسخة سابقة أبسط من هذا الملف** (لو كنت شفت نسخة "أفق الليلية" قبل
كده بألوان كحلي/ذهبي — دي كانت استخراج أول أقل دقة، اتلغت واتستبدلت بالنسخة دي):
- **الألوان الحقيقية في الأصل مفيهاش أي "ذهبي" (`#D4AF37`) ولا خلفية كحلي
  داكنة خالص** — دي كانت إضافة من عندي في المحاولة الأولى. الخلفية الاحتياطية
  الحقيقية في الملف الأصلي **فاتحة** (`#FFFFFF → #E5E5E5`)، والغمقان اللي
  بيدّي طابع "سينمائي داكن" مصدره **الفينييت السوداء فوقها بس** (`rgba(0,0,0,0.20)`
  في المنتصف لـ `rgba(0,0,0,0.75)` في الأطراف)، مش لون خلفية داكن مرسوم مباشرة.
- **الـ HUD/الأزرار في الأصل فاتحة/بيضاء** (زجاج شفاف أبيض، نص أسود) — **مش
  داكنة**. الغمقان في التصميم خاص بمحتوى الفيديو (الكانفاس) بس، مش بواجهة
  التحكم المحيطة بيه.
- زر التصدير أسود (`#111111`) مش ذهبي.

**عناصر من الملف الأصلي استُبعدت عمدًا** (ومش هتلاقيها هنا):
- **`getWorkingCodecConfig()`**: دالة كانت بتـ"تجرّب" كذا إعداد ترميز فيديو
  فعليًا (تفتح `Output` تجريبي وتقفله) قبل التصدير الحقيقي، عشان تختار أنسب
  كودك. دي منطق تصدير/تنفيذ صرف — مستبعدة بالكامل حسب قاعدة "المنطق التنفيذي
  الخام ما بينتقلش أبدًا" في القسم 1.5. الطبقة التقنية الثابتة في `AGENTS.md`
  بتحقق نفس الهدف (مرونة لو كودك مش مدعوم) بآلية تانية: قائمة محاولات مرتبة مع
  `try/catch` على كل محاولة كاملة، مش فحص مسبق منفصل — نفس المتانة عمليًا.
- **مصدر الصوت (`quranicaudio.com`, ملف واحد للسورة كاملة) وتوقيت `RAW_CUES`
  اليدوي**: مستبعدين، وبدل منهم جلب صوت آية بآية من `everyayah.com` عن طريق
  `fetchAndDecodeAudio`/`concatenateAudioBuffers` (نفس الباترن المتّبع في باقي
  هويات المشروع الحالية) — التوقيت بقى محسوب فعليًا من مدة الصوت الحقيقي، مش
  أرقام مكتوبة يدويًا (بندين 4 و5 في فحص التوافق فوق).
- **`ensureFontsLoaded()`**: دالة كانت بتستنى `document.fonts.load(...)` قبل
  أول رسم عشان تتجنب "فلاش" خط احتياطي في الفريم الأول. مستبعدة لتبسيط الواجهة
  بين طبقة الهوية والطبقة التقنية (مفيش hook جاهز ليها في الطبقة التقنية
  الثابتة حاليًا). عمليًا مش بتبقى مشكلة لأن تحميل الصوت من `everyayah.com` جوه
  `prepareIdentity()` بياخد وقت كافي (بيلف على كل آية لوحدها) بحيث خطوط Google
  Fonts بتكون خلصت تحميل قبل أول فريم فعلي بيتصدّر.
- **GSAP** (`<script src=".../gsap.min.js">`): محمّلة في رأس الملف الأصلي لكن
  **مش مستخدمة في أي مكان فعليًا** (صفر استدعاءات `gsap.`) — كود ميت، مش
  منقولة هنا خالص.
- **تعطيل الأزرار (`disabled`) لحد ما الأصول تتحمّل**: مرتبطة بهيكل HTML مخصّص
  للملف الأصلي، وبيهيكل الـ `<body>` الثابت في القسم 2 من `AGENTS.md` مفيهوش
  الخاصية دي على الأزرار من الأساس — مش هتتضاف هنا عشان الهيكل الثابت "ما
  يتعدلش أبدًا".

**تصحيح حقيقي في الملف الأصلي نفسه** (مش استبعاد، ده باگ فعلي بتصحيحه هنا):
- CSS الأصلي لـ `#console-modal` فيه `display: none;` **و** `display: flex;`
  مع بعض في نفس الـ rule (تكرار خاصية بقيمتين مختلفتين) — في CSS آخر قيمة
  مكررة هي اللي بتكسب، يعني عمليًا الأصل كان هيخلي نافذة سجل الأخطاء **ظاهرة
  بشكل افتراضي** فوق الفيديو من أول ما الصفحة تفتح، وده مش المقصود (زي ما
  بيتضح من منطق زرار الفتح/الإغلاق نفسه). الكود هنا سايب `display: none;` بس،
  زي باقي هويات المشروع.

**ملاحظات تصميم إضافية**:
- **الحركة هنا "كتلة واحدة" مش كلمة كلمة**: بعكس `brown-style.md`/`Quran.md`
  اللي بيعملوا "ظهور متتابع" لكل كلمة لوحدها، هنا الآية كلها بتتحرك كوحدة واحدة
  (fade + scale + إزاحة Y بسيطة) — لو غيّرت لهوية تانية وحسّيت الحركة "مختلفة
  في الإحساس"، ده هو السبب بالظبط.
- **وضع الصورة الاختيارية (`BACKGROUND_IMAGE_URL`) متعطّل افتراضيًا**: لو مهمة
  طلبت الطابع الفوتوغرافي الأصلي بالظبط، فعّله برابط صورة حقيقي بيدعم CORS
  (تأكد بـ `curl -sI` على الرابط إن فيه header اسمه `access-control-allow-origin`
  قبل ما تستخدمه — لو مفيش، الكانفاس هيبقى "tainted" والتصدير هيفشل). التحميل
  دلوقتي بـ `await loadImage(...)` جوه `prepareIdentity()` (مش fire-and-forget
  زي نسخة أقدم من هذا الملف) — يعني لو الصورة موجودة، أول فريم بيترسم هيلاقيها
  جاهزة فعليًا، مش هتظهر فجأة بعد أول كام فريم.
- **مركز الآية على `CONFIG.height / 2` بالظبط** (نص الشاشة الحقيقي، بعكس
  `brown-style.md` اللي بيزيح المركز لفوق عشان يسيب مكان لبطاقة تفسير) — مفيش
  بطاقة تفسير هنا فمفيش داعي للإزاحة.
- **جلب الصوت وبناء بيانات المشاهد دلوقتي مدموجين في `prepareIdentity()` واحدة**
  مع تحميل الصورة الاختيارية، بدل ما كانوا الثلاثة متفرقين (تحميل تلقائي في
  الطبقة التقنية، `buildParsedScenes` منفصلة، وتحميل صورة fire-and-forget خارج
  أي دالة). السلوك الفعلي لجلب الصوت (بما فيه التسامح مع فشل تحميل آية واحدة
  بدون ما يوقف الباقي) لم يتغيّر.
