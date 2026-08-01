# هوية "الرَّق الدافئ" (Warm Parchment) — تلاوة قرآنية بس، بدون تفسير

## الوصف
نفس لوحة ألوان `brown-style.md` (رَق دافئ، بني إسبريسو، ذهبي) لكن **بدون بطاقة
تفسير خالص** — تلاوة نظيفة، النص بس في منتصف الشاشة بحركة ظهور متتابع كلمة
كلمة.

- **شكل المحتوى**: آيات قرآنية بس، بدون تفسير.
- **الأصول**: صوت تلاوة آية بآية من `everyayah.com`. مفيش صورة ولا فيديو خارجي.
- **الأبعاد**: 1080×1920 (Shorts عمودي، 9:16)، 60fps.
- **حالة الاستخدام**: تلاوة سورة كاملة أو مقطع منها، من غير تفسير.

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
const OUTPUT_FILENAME = 'Quran_Shorts_Al_Ikhlas';
const SURAH_VERSES = [
    // نص كل آية (text) — من نتيجة curl فعلية على api.alquran.cloud
    // (edition=quran-uthmani)، مش من الذاكرة.
    { text: 'قُلْ هُوَ اللَّهُ أَحَدٌ ۝١' },
    { text: 'اللَّهُ الصَّمَدُ ۝٢' },
    { text: 'لَمْ يَلِدْ وَلَمْ يُولَدْ ۝٣' },
    { text: 'وَلَمْ يَكُن لَّهُ كُفُوًا أَحَدٌ ۝٤' }
];
// ================================================================

let CONFIG = { fps: 60, width: 1080, height: 1920, duration: 35.0 };
const QURAN_FONT = "'Amiri', serif";
const HEADER_FONT = "'Reem Kufi', sans-serif";

const PALETTE = {
    bgTop: "#FAF7F2",       // Warm Parchment
    bgBottom: "#EFE8DC",    // Soft Sand / Light Beige
    textPrimary: "#2C1D11", // Deep Espresso Brown
    accentGold: "#C08A3E",  // Warm Quranic Ochre Gold
    lineBorder: "rgba(192, 138, 62, 0.25)"
};

let audioBuffer = null;   // بيتحدد جوه prepareIdentity()
let parsedScenes = [];    // بيتحدد جوه prepareIdentity()

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

async function prepareIdentity() {
    logToConsole(`جاري تحميل صوت آيات ${SURAH_DISPLAY_NAME} آية بآية من EveryAyah.com (${RECITER_DISPLAY_NAME})...`);
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    const ayahBuffers = [];
    let totalSamples = 0;

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
        const verseFontSize = cue.text.length > 40 ? 68 : 78;
        const verseFont = `700 ${verseFontSize}px ${QURAN_FONT}`;
        // مركز Y = 960 (نص الشاشة بالظبط) — مفيش بطاقة تفسير تاخد مساحة تحت
        const words = layoutArabicParagraph(cue.text, verseFont, 920, 20, verseFontSize * 1.5, 960);
        return { ...cue, font: verseFont, fontSize: verseFontSize, words };
    });
}

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

async function drawSceneAtTime(time) {
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
  الموجودة في `brown-style.md` حرفيًا** — الفرق الوحيد إن مركز النص هنا
  `960` (نص الشاشة الفعلي) بدل `720`، ومفيش `drawEnhancedTafseerPanel` ولا
  `tafseerWords` خالص. لو محتاج تحويل فيديو من الهوية دي للهوية التانية
  (تفسير ↔ من غير تفسير)، الفرق محصور في النقطتين دول بس.
- **لو مهمة طلبت "بنفس هوية Quran.md بس ضيف تفسير"**: أسهل حل عملي إنك تستخدم
  `brown-style.md` مباشرة بدل ما تضيف منطق تفسير هنا من الصفر — هي نفس اللوحة
  البصرية أصلًا ومعمول فيها بطاقة التفسير جاهزة ومختبرة.
