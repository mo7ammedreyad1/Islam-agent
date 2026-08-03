# هوية "الرَّق الدافئ" (Warm Parchment) — تلاوة بس، بدون تفسير

## الوصف
نفس لوحة ألوان `brown-style.md` (رَق دافئ، بني إسبريسو، ذهبي) لكن **بدون بطاقة
تفسير خالص** — تلاوة نظيفة، النص بس في منتصف الشاشة بحركة ظهور متتابع كلمة
كلمة. مناسبة لفيديوهات التلاوة الخالصة من غير أي شرح.

- **الأبعاد**: 1080×1920 (Shorts عمودي، 9:16)، 60fps.
- **حالة الاستخدام**: تلاوة سورة كاملة أو مقطع منها، من غير تفسير.
- **بيانات المحتوى**: مفيش أي متغيرات هنا خالص (راجع "لا متغيرات لبيانات
  المحتوى" بالقسم 1 من `AGENTS.md`). كل رابط صوت وكل نص آية بيتكتبوا حرفيين
  مباشرة جوه `buildParsedScenes()`، سطر صريح واحد لكل آية — الكود تحت فيه
  مثال حقيقي كامل (سورة الإخلاص) يوضح النمط بالظبط: لما تستخدم الهوية دي في
  مهمة حقيقية، بتعيد كتابة نفس الدالة بروابط ونصوص الآيات الحقيقية للمهمة،
  بنفس الشكل والنمط بالظبط (رابط `everyayah.com` كامل حرفي لكل آية، ونص كل
  آية حرفي في مكان استخدامه). الاستثناء الوحيد هو `OUTPUT_FILENAME`.

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
Blueprint هندسي يُستوعب أولاً، مش نص يُنسخ ويُلصق من غير فهم. المثال تحت
(سورة الإخلاص) يوضح **النمط** بالظبط: روابط صوت حرفية كاملة سطر بسطر بلا حلقة
ولا متغيرات وسيطة، ونص كل آية حرفي في مكان استخدامه. لما تستخدم الهوية دي في
مهمة حقيقية، اكتب نفس الدالتين من جديد بروابط ونصوص المهمة الفعلية (نتيجة
`curl` حقيقية)، بنفس النمط والشكل بالظبط — مش استبدال قيم متغيرات، لأنه مفيش
متغيرات بيانات محتوى أصلًا.

```js
// ================================================================
// ⚙️↔🎨 CONFIG وOUTPUT_FILENAME — الاسمين ثابتين، الطبقة التقنية بتقراهم
// بعد ما buildParsedScenes() يخلص. OUTPUT_FILENAME هو الاستثناء الوحيد
// المسموح كمتغيّر محتوى (واجهة اتفاق تقنية، مش بيانات محتوى حقيقية).
// ================================================================
let CONFIG = { fps: 60, width: 1080, height: 1920, duration: 0 }; // duration بتتحدد فعليًا جوه buildParsedScenes()
const OUTPUT_FILENAME = 'Quran_Shorts_Al_Ikhlas';

// ================================================================
// 🎨 تصميم الهوية — ثوابت وأدوات رسم داخلية (ستايل، مش بيانات محتوى)
// ================================================================
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

// أداة تصميم مساعدة (منطق، مش بيانات محتوى): بتحسب حجم خط الآية حسب طولها
// وترجع تخطيطها جاهز. النص نفسه بيتبعت ليها حرفيًا من مكان الاستدعاء —
// الدالة دي ماعندهاش أي فكرة عن "آيات السورة"، هي منطق تصميم عام بس.
function layoutVerse(literalText) {
    const size = literalText.length > 40 ? 68 : 78;
    const font = `700 ${size}px ${QURAN_FONT}`;
    // مركز Y = 960 (نص الشاشة بالظبط) — مفيش بطاقة تفسير تاخد مساحة تحت
    const words = layoutArabicParagraph(literalText, font, 920, 20, size * 1.5, 960);
    return { font, words };
}

// ================================================================
// 🎨 الدالتين الإلزاميتين في العقد
// ================================================================
async function buildParsedScenes() {
    logToConsole('جاري تحميل صوت آيات سُورَةُ الْإِخْلَاصِ آية بآية من EveryAyah.com (الشيخ مشاري راشد العفاسي)...');

    // كل رابط حرفي كامل، سطر صريح واحد لكل آية — من غير حلقة ولا متغيرات
    // بتبني الرابط من أجزاء (راجع "لا متغيرات لبيانات المحتوى" بالقسم 1).
    // التسامح مع فشل تحميل آية واحدة (try/catch منفصل) من غير ما يوقف الباقي.
    const buffers = [];
    try {
        buffers.push(await fetchAndDecodeAudio('https://www.everyayah.com/data/Alafasy_128kbps/112001.mp3'));
        logToConsole('تم تحميل الآية 1 بنجاح ✓');
    } catch (err) { logToConsole(`تنبيه تحميل الآية 1: ${err.message}`, 'warn'); }
    try {
        buffers.push(await fetchAndDecodeAudio('https://www.everyayah.com/data/Alafasy_128kbps/112002.mp3'));
        logToConsole('تم تحميل الآية 2 بنجاح ✓');
    } catch (err) { logToConsole(`تنبيه تحميل الآية 2: ${err.message}`, 'warn'); }
    try {
        buffers.push(await fetchAndDecodeAudio('https://www.everyayah.com/data/Alafasy_128kbps/112003.mp3'));
        logToConsole('تم تحميل الآية 3 بنجاح ✓');
    } catch (err) { logToConsole(`تنبيه تحميل الآية 3: ${err.message}`, 'warn'); }
    try {
        buffers.push(await fetchAndDecodeAudio('https://www.everyayah.com/data/Alafasy_128kbps/112004.mp3'));
        logToConsole('تم تحميل الآية 4 بنجاح ✓');
    } catch (err) { logToConsole(`تنبيه تحميل الآية 4: ${err.message}`, 'warn'); }

    if (buffers.length === 0) throw new Error('تعذر جلب أي ملف صوت من EveryAyah');

    // بيعيّن قيمة لـ audioBuffer/CONFIG.duration المتعرّفين في الطبقة التقنية
    // — ما بيعيدش تعريفهم بـ let/const (راجع التحذير في القسم 1 من AGENTS.md)
    const { buffer, segments } = concatenateAudioBuffers(buffers);
    audioBuffer = buffer;
    CONFIG.duration = buffer.duration;

    // كل عنصر صريح ومنفصل — نص الآية حرفي مكتوب مباشرة هنا، مفيش مصفوفة
    // نصوص مشتركة بيتلف عليها بحلقة
    parsedScenes = [
        { start: segments[0].start, end: segments[0].end, ...layoutVerse('قُلْ هُوَ اللَّهُ أَحَدٌ ۝') },
        { start: segments[1].start, end: segments[1].end, ...layoutVerse('اللَّهُ الصَّمَدُ ۝') },
        { start: segments[2].start, end: segments[2].end, ...layoutVerse('لَمْ يَلِدْ وَلَمْ يُولَدْ ۝') },
        { start: segments[3].start, end: segments[3].end, ...layoutVerse('وَلَمْ يَكُن لَّهُ كُفُوًا أَحَدٌ ۝') }
    ];

    logToConsole(`تم دمج تلاوة الآيات بنجاح! مدة الشورتس: ${CONFIG.duration.toFixed(2)} ثانية ✓`);
}

async function drawSceneAtTime(time) { // async دايمًا (راجع "عقد ملف هوية .md" بـ AGENTS.md)، حتى لو مفيش await فعلي هنا
    state.currentTime = time;
    drawGlobalBackground();

    if (parsedScenes.length === 0) return;
    const scene = parsedScenes.find(s => time >= s.start && time <= s.end) || parsedScenes[parsedScenes.length - 1];
    const progress = clamp01((time - scene.start) / (scene.end - scene.start));

    // اسم السورة حرفي مباشر هنا — لو المهمة فيها سور متعددة، اكتب الاسم
    // الحرفي المناسب لكل مجموعة مشاهد بدل سطر واحد ثابت زي المثال ده
    drawSurahHeader('سُورَةُ الْإِخْلَاصِ');
    drawQuranVerseScene(scene, progress);
}
```

## ملاحظات معروفة
- **نفس دوال `drawGlobalBackground`/`drawSurahHeader`/`drawQuranVerseScene`
  الموجودة في `brown-style.md` منطقيًا** — الفرق الوحيد إن التخطيط هنا بيحط
  مركز النص على `960` (نص الشاشة الفعلي) بدل `720`، ومفيش
  `drawEnhancedTafseerPanel` ولا أي بطاقة تفسير خالص. لو محتاج تحويل فيديو من
  الهوية دي للهوية التانية (تفسير ↔ من غير تفسير)، الفرق محصور في النقطتين
  دول بس.
- **لو مهمة طلبت "بنفس هوية Quran.md بس ضيف تفسير"**: أسهل حل عملي إنك تستخدم
  `brown-style.md` مباشرة بدل ما تضيف منطق تفسير هنا من الصفر — هي نفس اللوحة
  البصرية أصلًا ومعمول فيها بطاقة التفسير جاهزة ومختبرة.
- **مفيش أي متغيرات بيانات محتوى في الملف ده خالص** (لا `SURAH_NUMBER` ولا
  `RECITER_ID` ولا حتى مصفوفة نصوص مشتركة) — الروابط والنصوص كلها حرفية مباشرة
  جوه `buildParsedScenes()`. `layoutVerse()` أداة تصميم عامة بس (بتحسب حجم
  الخط حسب طول أي نص تُبعته ليها)، مش مخزن بيانات — النص نفسه بيتحدد في مكان
  النداء عليها. الاستثناء الوحيد هو `OUTPUT_FILENAME`.
- **الدالة الإلزامية الأولى اسمها `buildParsedScenes()`** (مش `prepareIdentity()`
  زي إصدارات سابقة من هذا الملف) — الاسم اتغيّر ليطابق عقد `AGENTS.md` الحالي،
  والسلوك الفعلي (جلب الصوت آية بآية، التسامح مع فشل آية واحدة من غير ما يوقف
  الباقي، حساب المدة من الصوت الحقيقي) لم يتغيّر.
