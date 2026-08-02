# هوية "اختبار الحفظ" (Quran Memory Quiz) — أكمل الآية الناقصة

## الوصف
نفس لوحة ألوان "الرَّق الدافئ" (Warm Parchment) المستخدمة في `brown-style.md`
و`Quran.md` — لكن بدل التلاوة الخالصة، الفيديو هنا **اختبار حفظ تفاعلي**: كل
آية بتتعرض الأول بكلمة ناقصة (`[ ؟ ]`) مع 3 اختيارات (أ/ب/ج) تحتها، وبعدها في
لحظة محددة من تلاوة الآية نفسها (نسبة 58% من زمن الآية) الكلمة الصح بتتكشف
ويبان اختيارها بإطار ذهبي، والاختيارات التانية بتخفت. مناسبة لفيديوهات
"اختبر حفظك" القصيرة.

> **نشأة الهوية دي**: اتبنت بمنطق "استيعاب هوية من ملف خارجي" (القسم 1.5 في
> `AGENTS.md`) من ملف خارجي بعنوان داخلي "اختبار تلاوة وحفظ القرآن الكريم
> (Clean)". الألوان والخطوط والتخطيط البصري نفس تصميم عائلة "الرَّق الدافئ"
> بالحرف، ومنطق الكشف عند نسبة 58% ومواضع البطاقات (Y=1100/1230/1360) نفس
> الملف الأصلي بالظبط — لكن منطق جلب الصوت وحساب التوقيتات ودالة الرسم
> المركزية اتبنوا من جديد بما يتماشى مع عقد الرانر، بدل نقل الكود التنفيذي
> القديم كما هو.

**فحص التوافق مع الرانر (القسم 1.5)** — الملف الأصلي فشل في 4 بنود من أصل 6:
- ✅ يستخدم Mediabunny صح (`Output`/`CanvasSource`/`AudioBufferSource`).
- ✅ توقيت الآيات محسوب فعليًا من مدة كل مقطع صوتي حقيقي بعد فك التشفير (مش
  أرقام مخمّنة يدويًا) — نقطة قوة حافظنا عليها.
- ❌ صفر render hooks (`renderStatus`, `renderProgress`, `?autorender=true`,
  `__renderFilename`/`__renderBase64`/`__renderError`) — `exportWithFallback`
  بتنزّل الملف مباشرة بـ `<a download>` من غير أي إشارة للطبقة التقنية
  الأوتوماتيكية، فـ `render-runner.js` كان هيفضل مستني `window.renderStatus`
  للأبد.
- ❌ `@mediabunny/aac-encoder` بيتستدعى ديناميكيًا (`import('@mediabunny/aac-encoder')`)
  من غير ما يتسجل في الـ `importmap` أصلاً (فيها `mediabunny` بس) — هيفشل عند
  التنفيذ الفعلي.
- ❌ `drawSceneAtTime` دالة عادية مش `async` — مخالف لعقد الهوية.
- ❌ مفيش `prepareIdentity()` باسمها؛ المنطق مقسوم بين `preloadEveryAyahQuranAudio()`
  و`buildParsedScenes()` بيتنادوا يدويًا في `init()`، ومنطق جلب/فك تشفير الصوت
  وتجميعه (`fetch` + `decodeAudioData` + دمج الـ buffers يدويًا) بيكرر نفس اللي
  موجود جاهز في الطبقة التقنية الثابتة (`fetchAndDecodeAudio`/`concatenateAudioBuffers`)
  بدل استخدامه مباشرة. مفيش `OUTPUT_FILENAME` كمتغير موحّد — اسم الملف مكتوب
  Hardcoded جوه دالة التصدير نفسها.

- **الأبعاد**: 1080×1920 (Shorts عمودي، 9:16)، 60fps.
- **حالة الاستخدام**: اختبار حفظ تفاعلي لسورة أو مجموعة آيات، كل آية بيها كلمة
  واحدة مخفية واختيارات.
- **شكل بيانات المحتوى** (`SURAH_NUMBER`, `RECITER_ID`, `RECITER_DISPLAY_NAME`,
  `SURAH_DISPLAY_NAME`, `OUTPUT_FILENAME`, `QUIZ_VERSES`): كل عنصر في
  `QUIZ_VERSES` لازم يحتوي `fullText` (نص الآية كامل)، `hiddenWord` (الكلمة
  المستهدفة بالاختبار)، `textWithBlank` (نفس النص بس الكلمة مستبدلة بـ
  `[ ؟ ]`)، `options` (مصفوفة من 3 نصوص، ترتيبها ثابت وهيتعرض بنفس الترتيب),
  و`correctIndex` (رقم index الاختيار الصح في `options`، من 0). الصوت بيتجاب
  من `everyayah.com` آية بآية داخل `prepareIdentity()` بناءً على
  `SURAH_NUMBER`/`RECITER_ID` وطول `QUIZ_VERSES` (نفس منطق `Quran.md`).

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
// مثال (سورة الشرح، الآيات 1-8) يوضح الشكل المطلوب بالظبط — الأسماء والبنية
// ثابتة لهذه الهوية، القيم بس بتتغيّر.
// ================================================================
const SURAH_NUMBER = '094';
const RECITER_ID = 'Alafasy_128kbps';
const RECITER_DISPLAY_NAME = 'الشيخ مشاري راشد العفاسي';
const SURAH_DISPLAY_NAME = 'سُورَةُ الشَّرْحِ';
const OUTPUT_FILENAME = 'Quran_Quiz_Shorts_Ash_Sharh';
const QUIZ_VERSES = [
    {
        fullText: "أَلَمْ نَشْرَحْ لَكَ صَدْرَكَ ۝١",
        hiddenWord: "صَدْرَكَ",
        textWithBlank: "أَلَمْ نَشْرَحْ لَكَ  [ ؟ ]  ۝١",
        options: ["قَلْبَكَ", "صَدْرَكَ", "عَقْلَكَ"],
        correctIndex: 1
    },
    {
        fullText: "وَوَضَعْنَا عَنكَ وِزْرَكَ ۝٢",
        hiddenWord: "وِزْرَكَ",
        textWithBlank: "وَوَضَعْنَا عَنكَ  [ ؟ ]  ۝٢",
        options: ["ذَنْبَكَ", "وِزْرَكَ", "هَمَّكَ"],
        correctIndex: 1
    },
    {
        fullText: "الَّذِي أَنقَضَ ظَهْرَكَ ۝٣",
        hiddenWord: "أَنقَضَ",
        textWithBlank: "الَّذِي  [ ؟ ]  ظَهْرَكَ ۝٣",
        options: ["أَثْقَلَ", "أَنْقَضَ", "أَوْجَعَ"],
        correctIndex: 1
    },
    {
        fullText: "وَرَفَعْنَا لَكَ ذِكْرَكَ ۝٤",
        hiddenWord: "ذِكْرَكَ",
        textWithBlank: "وَرَفَعْنَا لَكَ  [ ؟ ]  ۝٤",
        options: ["قَدْرَكَ", "شَأْنَكَ", "ذِكْرَكَ"],
        correctIndex: 2
    },
    {
        fullText: "فَإِنَّ مَعَ الْعُسْرِ يُسْرًا ۝٥",
        hiddenWord: "يُسْرًا",
        textWithBlank: "فَإِنَّ مَعَ الْعُسْرِ  [ ؟ ]  ۝٥",
        options: ["فَرَجًا", "يُسْرًا", "نَصْرًا"],
        correctIndex: 1
    },
    {
        fullText: "إِنَّ مَعَ الْعُسْرِ يُسْرًا ۝٦",
        hiddenWord: "الْعُسْرِ",
        textWithBlank: "إِنَّ مَعَ  [ ؟ ]  يُسْرًا ۝٦",
        options: ["الضَّيْقِ", "الْعُسْرِ", "الْخَوْفِ"],
        correctIndex: 1
    },
    {
        fullText: "فَإِذَا فَرَغْتَ فَانصَبْ ۝٧",
        hiddenWord: "فَانصَبْ",
        textWithBlank: "فَإِذَا فَرَغْتَ  [ ؟ ]  ۝٧",
        options: ["فَانصَبْ", "فَاصْبِرْ", "فَاسْجُدْ"],
        correctIndex: 0
    },
    {
        fullText: "وَإِلَىٰ رَبِّكَ فَارْغَب ۝٨",
        hiddenWord: "فَارْغَب",
        textWithBlank: "وَإِلَىٰ رَبِّكَ  [ ؟ ]  ۝٨",
        options: ["فَاقْرَبْ", "فَارْغَب", "فَارْجِعْ"],
        correctIndex: 1
    }
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
const REVEAL_THRESHOLD = 0.58; // عند 58% من زمن تلاوة الآية، الإجابة بتتكشف

const PALETTE = {
    bgTop: "#FAF7F2",       // Warm Parchment
    bgBottom: "#EFE8DC",    // Soft Sand / Light Beige
    textPrimary: "#2C1D11", // Deep Espresso Brown
    textMuted: "#6B5343",   // Warm Terracotta / Walnut Brown
    accentGold: "#C08A3E",  // Warm Quranic Ochre Gold
    cardBg: "#FFFFFF",      // Clean White Card Fill
    lineBorder: "rgba(192, 138, 62, 0.25)"
};

const Easing = {
    linear: t => t,
    easeOutCubic: t => 1 - Math.pow(1 - t, 3)
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

function drawSurahQuizHeader(surahName) {
    ctx.save();
    const headerY = 200;

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

    ctx.fillStyle = PALETTE.textPrimary;
    ctx.font = `700 44px ${HEADER_FONT}`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(surahName, CONFIG.width / 2, headerY);

    ctx.fillStyle = PALETTE.accentGold;
    ctx.beginPath(); ctx.roundRect(CONFIG.width / 2 - 210, headerY + 50, 420, 48, 999); ctx.fill();
    ctx.fillStyle = "#FFFFFF"; ctx.font = `700 22px ${HEADER_FONT}`;
    ctx.fillText("؟ اختَبِرْ حِفْظَكَ: أَكْمِلِ الكَلِمَةَ", CONFIG.width / 2, headerY + 73);

    ctx.restore();
}

// مشهد السؤال/الإجابة: النص بفراغ + 3 اختيارات، وعند REVEAL_THRESHOLD
// الكلمة الصح بتتكشف والاختيار الصح بياخد إطار ذهبي والباقي بيخفت
function drawQuranQuizVerseScene(scene, sceneProgress) {
    const isRevealed = sceneProgress >= REVEAL_THRESHOLD;

    ctx.save();
    ctx.font = scene.font;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';

    const targetWords = isRevealed ? scene.fullWords : scene.blankWords;
    targetWords.forEach(w => {
        ctx.save();
        const isHiddenWord = isRevealed && w.text.includes(scene.hiddenWord);
        const isBlankBox = !isRevealed && w.text.includes("[");
        const isVerseMarker = w.text.includes("۝");

        ctx.fillStyle = (isHiddenWord || isBlankBox || isVerseMarker)
            ? PALETTE.accentGold
            : PALETTE.textPrimary;

        ctx.fillText(w.text, w.x, w.y);
        ctx.restore();
    });

    const startY = 1100;
    const cardW = 780, cardH = 95, spacing = 125;
    const optionBadges = ["أ", "ب", "ج"];

    scene.options.forEach((optText, idx) => {
        const cardY = startY + (idx * spacing);
        const isCorrect = idx === scene.correctIndex;
        const cardAlpha = Easing.easeOutCubic(clamp01(sceneProgress / 0.25));

        ctx.save();
        ctx.translate(CONFIG.width / 2, cardY + cardH / 2);
        ctx.shadowColor = 'rgba(0,0,0,0.04)';
        ctx.shadowBlur = 12;
        ctx.shadowOffsetY = 6;

        if (isRevealed) {
            if (isCorrect) {
                ctx.fillStyle = PALETTE.cardBg;
                ctx.beginPath(); ctx.roundRect(-cardW / 2, -cardH / 2, cardW, cardH, 20); ctx.fill();
                ctx.strokeStyle = PALETTE.accentGold; ctx.lineWidth = 3.5; ctx.stroke();

                ctx.fillStyle = PALETTE.accentGold;
                ctx.beginPath(); ctx.arc(-cardW / 2 + 50, 0, 20, 0, Math.PI * 2); ctx.fill();
                ctx.fillStyle = "#FFFFFF"; ctx.font = `700 20px ${HEADER_FONT}`;
                ctx.fillText("✓", -cardW / 2 + 50, 1);

                ctx.fillStyle = PALETTE.textPrimary;
            } else {
                ctx.globalAlpha = 0.35;
                ctx.fillStyle = PALETTE.cardBg;
                ctx.beginPath(); ctx.roundRect(-cardW / 2, -cardH / 2, cardW, cardH, 20); ctx.fill();
                ctx.strokeStyle = PALETTE.lineBorder; ctx.lineWidth = 1.5; ctx.stroke();
                ctx.fillStyle = PALETTE.textMuted;
            }
        } else {
            ctx.globalAlpha = cardAlpha;
            ctx.fillStyle = PALETTE.cardBg;
            ctx.beginPath(); ctx.roundRect(-cardW / 2, -cardH / 2, cardW, cardH, 20); ctx.fill();
            ctx.strokeStyle = PALETTE.lineBorder; ctx.lineWidth = 2; ctx.stroke();

            ctx.fillStyle = "rgba(192, 138, 62, 0.12)";
            ctx.beginPath(); ctx.arc(-cardW / 2 + 50, 0, 20, 0, Math.PI * 2); ctx.fill();
            ctx.fillStyle = PALETTE.accentGold; ctx.font = `700 20px ${HEADER_FONT}`;
            ctx.fillText(optionBadges[idx], -cardW / 2 + 50, 1);

            ctx.fillStyle = PALETTE.textPrimary;
        }

        ctx.font = `700 42px ${QURAN_FONT}`;
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillText(optText, 20, 0);

        ctx.restore();
    });

    ctx.restore();
}

// ================================================================
// 🎨 الدالتين الإلزاميتين في العقد
// ================================================================
async function prepareIdentity() {
    logToConsole(`جاري تحميل صوت آيات ${SURAH_DISPLAY_NAME} آية بآية من EveryAyah.com (${RECITER_DISPLAY_NAME}) لبناء اختبار الحفظ...`);

    const ayahBuffers = [];
    for (let i = 1; i <= QUIZ_VERSES.length; i++) {
        const ayahNum = String(i).padStart(3, '0');
        const url = `https://www.everyayah.com/data/${RECITER_ID}/${SURAH_NUMBER}${ayahNum}.mp3`;
        try {
            ayahBuffers.push(await fetchAndDecodeAudio(url));
            logToConsole(`تم تحميل صوت الآية ${i} بنجاح ✓`);
        } catch (err) {
            logToConsole(`تنبيه تحميل الآية ${i}: ${err.message}`, 'warn');
        }
    }
    if (ayahBuffers.length === 0) throw new Error("تعذر جلب أي ملف صوت من EveryAyah لبناء الاختبار");

    const { buffer, segments } = concatenateAudioBuffers(ayahBuffers);
    audioBuffer = buffer;
    CONFIG.duration = audioBuffer.duration;

    const cues = segments.map((seg, i) => ({ id: i + 1, ...seg, ...QUIZ_VERSES[i] }));
    parsedScenes = cues.map(cue => {
        const font = `700 76px ${QURAN_FONT}`;
        // تخطيط النص مرتين: نسخة بالفراغ ونسخة كاملة، مركز Y = 650 عشان يسيب
        // مساحة كافية تحته لبطاقات الاختيارات الثلاثة (Y = 1100/1225/1350)
        const blankWords = layoutArabicParagraph(cue.textWithBlank, font, 920, 22, 120, 650);
        const fullWords = layoutArabicParagraph(cue.fullText, font, 920, 22, 120, 650);
        return { ...cue, font, blankWords, fullWords };
    });

    logToConsole(`تم دمج تلاوة الآيات وبناء أسئلة الاختبار بنجاح! مدة الشورتس: ${CONFIG.duration.toFixed(2)} ثانية ✓`);
}

async function drawSceneAtTime(time) { // async دايمًا (راجع "عقد ملف هوية .md" بـ AGENTS.md)، حتى لو مفيش await فعلي هنا
    state.currentTime = time;
    drawGlobalBackground();

    if (parsedScenes.length === 0) return;
    const scene = parsedScenes.find(s => time >= s.start && time <= s.end) || parsedScenes[parsedScenes.length - 1];
    const progress = clamp01((time - scene.start) / (scene.end - scene.start));

    drawSurahQuizHeader(scene.surah || SURAH_DISPLAY_NAME);
    drawQuranQuizVerseScene(scene, progress);
}
```

## ملاحظات معروفة
- **نفس عائلة ألوان `brown-style.md`/`Quran.md` بالحرف** (`PALETTE`، الخطوط،
  خلفية الكانفاس، CSS الأزرار والـ HUD) — الهوية دي إضافة نوع محتوى جديد
  (اختبار تفاعلي) لنفس العائلة البصرية، مش تصميم منفصل.
- **الكشف عن الإجابة مربوط بـ `REVEAL_THRESHOLD = 0.58`** من زمن كل آية (مش
  زمن الفيديو الكلي) — لو عايز الكشف يحصل أبكر أو أتأخر، غيّر الرقم ده بس.
- **`isHiddenWord`/`isBlankBox` بيعتمدوا على `.includes()`** على نص الكلمة —
  لو `hiddenWord` كلمة بتتكرر أكتر من مرة في نفس الآية أو جزء من كلمة تانية،
  فكّر تتأكد إن القيمة فريدة بما يكفي في سياق الآية دي قبل الاعتماد عليها.
  البديل الأدق (لو احتجته لاحقًا) إنك تحدد index الكلمة المستهدفة صراحة بدل
  المطابقة النصية.
- **مفيش حقل `surah` منفصل لكل عنصر في `QUIZ_VERSES`** (كان موجود في الملف
  الأصلي بس مكرر بنفس القيمة في كل آية) — استُبدل بالاعتماد على
  `SURAH_DISPLAY_NAME` الموحّد + دعم `scene.surah` الاختياري لو احتجت مستقبلًا
  تخلط آيات من سور مختلفة في نفس الاختبار (نفس منطق `Quran.md`).
- **بطاقات الاختيارات ثابتة على 3 اختيارات بالظبط** (`options.length === 3`
  مفروض ضمنيًا من المواضع الرأسية الثابتة Y=1100/1225/1350) — لو المهمة
  احتاجت عدد اختيارات مختلف، لازم تعدّل حساب `startY`/`spacing` بما يتناسب.
- **جلب الصوت وحساب التوقيتات بيستخدموا `fetchAndDecodeAudio`/`concatenateAudioBuffers`
  الجاهزين في الطبقة التقنية الثابتة** بدل إعادة تعريفهم محليًا زي الملف
  الأصلي — نفس نمط `Quran.md`/`brown-style.md`.
