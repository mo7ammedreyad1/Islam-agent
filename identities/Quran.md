# هوية "الرَّق الدافئ" (Warm Parchment) — تلاوة بس، بدون تفسير

## الوصف
نفس لوحة ألوان `brown-style.md` (رَق دافئ، بني إسبريسو، ذهبي) لكن **بدون بطاقة
تفسير خالص** — تلاوة نظيفة، النص بس في منتصف الشاشة بحركة ظهور متتابع كلمة
كلمة. مناسبة لفيديوهات التلاوة الخالصة من غير أي شرح.

- **الأبعاد**: 1080×1920 (Shorts عمودي، 9:16)، 60fps.
- **حالة الاستخدام**: تلاوة سورة كاملة أو مقطع منها، من غير تفسير.
- **شكل بيانات المحتوى** (`SURAH_NUMBER`, `RECITER_ID`, `RECITER_DISPLAY_NAME`,
  `SURAH_DISPLAY_NAME`, `OUTPUT_FILENAME`, `SURAH_VERSES`): كل عنصر في
  `SURAH_VERSES` فيه `text` بس، مفيش حاجة إضافية مطلوبة (لا تفسير ولا غيره).
  الصوت بيتجاب من `everyayah.com` آية بآية داخل `prepareIdentity()` بناءً على
  `SURAH_NUMBER`/`RECITER_ID` وطول `SURAH_VERSES` (راجع الكود تحت).

## روابط الخطوط
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400;1,700&family=Reem+Kufi:wght@400;500;600;700;800&display=swap" rel="stylesheet">
```

## CSS كامل
```css
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
    background-color: #0f0f11; color: #111111;
    font-family: 'Amiri', -apple-system, BlinkMacSystemFont, 'SF Pro Display', serif;
    display: flex; align-items: center; justify-content: center;
    height: 100vh; width: 100vw; overflow: hidden;
}
#viewport { position: relative; width: 100%; height: 100%; display: flex; align-items: center; justify-content: center; padding: 2vh; }
canvas {
    background: #faf7f2; box-shadow: 0 0 80px rgba(0, 0, 0, 0.4);
    border-radius: 16px; border: 1px solid #dcdcdc;
    max-width: 100%; max-height: 86vh; width: auto; height: auto;
    aspect-ratio: 9 / 16; object-fit: contain;
}
#hud {
    position: absolute; top: 3vh; right: 4vw;
    background: rgba(255, 255, 255, 0.95); border: 1px solid #dcdcdc; border-radius: 999px;
    padding: 1.2vh 2.5vw; display: flex; align-items: center; gap: 12px;
    font-size: 14px; font-weight: 600; color: #111; backdrop-filter: blur(12px); z-index: 100;
    font-family: -apple-system, BlinkMacSystemFont, sans-serif;
}
.spinner { width: 18px; height: 18px; border: 2px solid #111; border-top-color: transparent; border-radius: 50%; animation: spin 0.8s linear infinite; }
@keyframes spin { to { transform: rotate(360deg); } }
#controls-overlay {
    position: absolute; bottom: 3vh; display: flex; gap: 12px; z-index: 100;
    background: rgba(255, 255, 255, 0.95); border: 1px solid rgba(0, 0, 0, 0.15);
    padding: 1.2vh 2vw; border-radius: 999px; backdrop-filter: blur(10px);
    max-width: 92vw; flex-wrap: wrap; justify-content: center;
    font-family: -apple-system, BlinkMacSystemFont, sans-serif;
}
.btn {
    background: none; border: none; color: #111;
    font-family: -apple-system, BlinkMacSystemFont, sans-serif;
    font-size: 14px; font-weight: 600; cursor: pointer; padding: 8px 18px; border-radius: 999px;
    display: flex; align-items: center; gap: 6px; transition: all 0.3s ease;
}
.btn-preview { background: rgba(0, 0, 0, 0.05); border: 1px solid rgba(0, 0, 0, 0.1); }
.btn-preview:hover { background: rgba(0, 0, 0, 0.1); }
.btn-render { background: #2c1d11; color: #ffffff; border: 1px solid #2c1d11; }
.btn-render:hover { background: #422d1c; }

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
<canvas id="shortsCanvas" width="1080" height="1920"></canvas>
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
const OUTPUT_FILENAME = 'Quran_Shorts_Al_Ikhlas';
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
let CONFIG = { fps: 60, width: 1080, height: 1920, duration: 0 }; // duration بتتحدد فعليًا جوه prepareIdentity()
let audioBuffer = null; // بيتملى جوه prepareIdentity()

// ================================================================
// 🎨 تصميم الهوية — ثوابت وأدوات رسم داخلية
// ================================================================
let parsedScenes = []; // حالة داخلية للهوية، بتتملى جوه prepareIdentity()

const QURAN_FONT = "'Amiri', serif";
const HEADER_FONT = "'Reem Kufi', sans-serif";

const PALETTE = {
    bgTop: "#FAF7F2",       // Warm Parchment
    bgBottom: "#EFE8DC",    // Soft Sand / Light Beige
    textPrimary: "#2C1D11", // Deep Espresso Brown
    textMuted: "#6B5343",   // Warm Terracotta / Walnut Brown
    accentGold: "#C08A3E",  // Warm Quranic Ochre Gold
    cardBg: "#FFFFFF",      // Clean White Panel Fill
    lineBorder: "rgba(192, 138, 62, 0.25)",
    goldGlow: "rgba(192, 138, 62, 0.12)"
};

function drawGlobalBackground() {
    const grad = ctx.createLinearGradient(0, 0, 0, CONFIG.height);
    grad.addColorStop(0, PALETTE.bgTop);
    grad.addColorStop(1, PALETTE.bgBottom);
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, CONFIG.width, CONFIG.height);

    ctx.strokeStyle = PALETTE.lineBorder;
    ctx.lineWidth = 4;
    ctx.strokeRect(40, 40, CONFIG.width - 80, CONFIG.height - 80);
}

function drawSurahHeader(surahName) {
    ctx.save();
    const headerY = 220;

    ctx.strokeStyle = PALETTE.accentGold;
    ctx.lineWidth = 3;

    ctx.beginPath();
    ctx.moveTo(CONFIG.width / 2 - 240, headerY);
    ctx.lineTo(CONFIG.width / 2 - 120, headerY);
    ctx.stroke();

    ctx.beginPath();
    ctx.moveTo(CONFIG.width / 2 + 120, headerY);
    ctx.lineTo(CONFIG.width / 2 + 240, headerY);
    ctx.stroke();

    ctx.fillStyle = PALETTE.accentGold;
    ctx.beginPath(); ctx.arc(CONFIG.width / 2 - 110, headerY, 6, 0, Math.PI * 2); ctx.fill();
    ctx.beginPath(); ctx.arc(CONFIG.width / 2 + 110, headerY, 6, 0, Math.PI * 2); ctx.fill();

    ctx.fillStyle = PALETTE.textPrimary;
    ctx.font = `700 42px ${HEADER_FONT}`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(surahName, CONFIG.width / 2, headerY);

    ctx.restore();
}

// ظهور متتابع للآية (كلمة كلمة) — بدون بطاقة، النص في منتصف الشاشة مباشرة
function drawQuranVerseScene(scene, sceneProgress) {
    const STAGGER_RATIO = 0.07;
    const WORD_ANIM_DURATION = 0.45;
    const SLIDE_DISTANCE = 25;

    ctx.save();
    ctx.font = scene.font;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';

    scene.words.forEach((w, i) => {
        const wordStart = i * STAGGER_RATIO;
        const wordT = clamp01((sceneProgress - wordStart) / WORD_ANIM_DURATION);
        if (wordT <= 0) return;

        const eased = Easing.easeOutCubic(wordT);
        const isVerseMarker = w.text.includes("۝");

        ctx.save();
        ctx.globalAlpha = eased;
        ctx.fillStyle = isVerseMarker ? PALETTE.accentGold : PALETTE.textPrimary;
        ctx.fillText(w.text, w.x, w.y + (1 - eased) * SLIDE_DISTANCE);
        ctx.restore();
    });
    ctx.restore();
}

// ================================================================
// 🎨 الدالتين الإلزاميتين في العقد
// ================================================================
async function prepareIdentity() {
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
        const verseFontSize = cue.text.length > 40 ? 68 : 78;
        const verseFont = `700 ${verseFontSize}px ${QURAN_FONT}`;
        // مركز Y = 960 (نص الشاشة بالظبط) — مفيش بطاقة تفسير تاخد مساحة تحت
        const words = layoutArabicParagraph(cue.text, verseFont, 920, 20, verseFontSize * 1.5, 960);
        return { ...cue, font: verseFont, fontSize: verseFontSize, words };
    });

    logToConsole(`تم دمج تلاوة الآيات بنجاح! مدة الشورتس: ${CONFIG.duration.toFixed(2)} ثانية ✓`);
}

async function drawSceneAtTime(time) { // async دايمًا (راجع "عقد ملف هوية .md" بـ AGENTS.md)، حتى لو مفيش await فعلي هنا
    state.currentTime = time;
    drawGlobalBackground();

    if (parsedScenes.length === 0) return;
    const scene = parsedScenes.find(s => time >= s.start && time <= s.end) || parsedScenes[parsedScenes.length - 1];
    const progress = clamp01((time - scene.start) / (scene.end - scene.start));

    drawSurahHeader(scene.surah || SURAH_DISPLAY_NAME);
    drawQuranVerseScene(scene, progress);
}
```

## ملاحظات معروفة
- **نفس دوال `drawGlobalBackground`/`drawSurahHeader`/`drawQuranVerseScene`
  الموجودة في `brown-style.md` حرفيًا** — الفرق الوحيد إن `prepareIdentity()`
  هنا بيحط مركز النص على `960` (نص الشاشة الفعلي) بدل `720`، ومفيش
  `drawEnhancedTafseerPanel` ولا `tafseerWords` خالص. لو محتاج تحويل فيديو من
  الهوية دي للهوية التانية (تفسير ↔ من غير تفسير)، الفرق محصور في النقطتين
  دول بس.
- **لو مهمة طلبت "بنفس هوية Quran.md بس ضيف تفسير"**: أسهل حل عملي إنك تستخدم
  `brown-style.md` مباشرة بدل ما تضيف منطق تفسير هنا من الصفر — هي نفس اللوحة
  البصرية أصلًا ومعمول فيها بطاقة التفسير جاهزة ومختبرة.
- **جلب الصوت وبناء بيانات المشاهد دلوقتي مدموجين في `prepareIdentity()` واحدة**
  (بدل ما كانا مقسّمين بين تحميل تلقائي في الطبقة التقنية ودالة
  `buildParsedScenes` منفصلة) — لو بتقارن بنسخة قديمة من هذا الملف شفت فيها
  الاسمين دول، هما بقوا دالة واحدة هنا، والسلوك الفعلي (بما فيه التسامح مع فشل
  تحميل آية واحدة بدون ما يوقف الباقي) لم يتغيّر.
