# هوية "الرَّق الدافئ" (Warm Parchment) — مع بطاقة تفسير

## الوصف
طابع كلاسيكي دافئ يحاكي ورق المصحف القديم: خلفية بيج/رملية فاتحة، نص القرآن
بالبني الغامق (إسبريسو)، وخطوط ذهبية للفواصل والتفاصيل. المشهد فيه هيدر ثابت
باسم السورة، الآية نفسها بحركة "ظهور متتابع" كلمة كلمة، وتحتها **بطاقة تفسير
بيضاء** بحركة ظهور مماثلة. مناسبة لفيديوهات التلاوة + التفسير الميسّر معًا.

- **الأبعاد**: 1080×1920 (Shorts عمودي، 9:16)، 60fps.
- **حالة الاستخدام**: أي سورة عايز تعرضها بتلاوة + تفسير مبسّط في نفس الفيديو.
- **حقل إضافي مطلوب في `SURAH_VERSES`**: كل عنصر لازم يحتوي `tafseer` (نص
  التفسير الميسّر للآية) بالإضافة لـ `text` — من غيره بطاقة التفسير هتفضل فاضية.

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

## كود JS — تصميم الهوية
انسخ الكتلة دي حرفيًا في "منطقة 2/3 — تصميم الهوية" جوه `scene.html` (راجع
القسم 2 في `AGENTS.md`). الدالتين `buildParsedScenes` و`drawSceneAtTime` في
الآخر هما نقطة الاتصال بالطبقة التقنية الثابتة — لازم يتعرّفوا بنفس الاسمين.

```js
// --- CONFIG & PALETTE (Vertical Shorts 1080x1920 — الرَّق الدافئ) ---
let CONFIG = { fps: 60, width: 1080, height: 1920, duration: 35.0 };
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

// ظهور متتابع للآية (كلمة كلمة، من غير بطاقة/خلفية حوليها)
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

// بطاقة التفسير البيضاء أسفل الشاشة، بنفس منطق الظهور المتتابع
function drawEnhancedTafseerPanel(scene, sceneProgress) {
    if (!scene.tafseerWords) return;

    const panelW = 920, panelH = 320;
    const panelX = (CONFIG.width - panelW) / 2;
    const panelY = CONFIG.height - 420;

    const cardAlpha = Easing.easeOutCubic(clamp01(sceneProgress / 0.3));

    ctx.save();
    ctx.globalAlpha = cardAlpha;

    ctx.shadowColor = "rgba(44, 29, 17, 0.06)";
    ctx.shadowBlur = 25;
    ctx.shadowOffsetY = 10;

    ctx.fillStyle = PALETTE.cardBg;
    ctx.beginPath(); ctx.roundRect(panelX, panelY, panelW, panelH, 24); ctx.fill();

    ctx.strokeStyle = PALETTE.lineBorder;
    ctx.lineWidth = 2.5;
    ctx.stroke();

    ctx.strokeStyle = PALETTE.goldGlow;
    ctx.lineWidth = 1.5;
    ctx.beginPath(); ctx.roundRect(panelX + 10, panelY + 10, panelW - 20, panelH - 20, 18); ctx.stroke();

    ctx.fillStyle = PALETTE.accentGold;
    ctx.font = `700 24px ${HEADER_FONT}`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText("﴿ التَّفْسِيرُ المُمَيَّسَرُ ﴾", CONFIG.width / 2, panelY + 50);

    ctx.strokeStyle = PALETTE.lineBorder; ctx.lineWidth = 1.5;
    ctx.beginPath();
    ctx.moveTo(CONFIG.width / 2 - 120, panelY + 80);
    ctx.lineTo(CONFIG.width / 2 + 120, panelY + 80);
    ctx.stroke();

    ctx.font = scene.tafseerFont;
    ctx.fillStyle = PALETTE.textMuted;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';

    const wordStagger = 0.035;
    const wordAnimDuration = 0.35;
    const wordSlideDist = 15;

    scene.tafseerWords.forEach((w, idx) => {
        const wordStart = idx * wordStagger;
        const wordT = clamp01((sceneProgress - wordStart) / wordAnimDuration);
        if (wordT <= 0) return;

        const easedWord = Easing.easeOutCubic(wordT);
        ctx.save();
        ctx.globalAlpha = easedWord * cardAlpha;
        ctx.fillText(w.text, w.x, w.y + (1 - easedWord) * wordSlideDist);
        ctx.restore();
    });

    ctx.restore();
}

// --- الدالتين الإلزاميتين ---
function buildParsedScenes() {
    parsedScenes = RAW_CUES.map(cue => {
        const verseFontSize = cue.text.length > 40 ? 68 : 78;
        const verseFont = `700 ${verseFontSize}px ${QURAN_FONT}`;
        const words = layoutArabicParagraph(cue.text, verseFont, 920, 20, verseFontSize * 1.5, 720);

        const tafseerFontSize = 34;
        const tafseerFont = `500 ${tafseerFontSize}px ${QURAN_FONT}`;
        const tafseerWords = cue.tafseer
            ? layoutArabicParagraph(cue.tafseer, tafseerFont, 820, 12, tafseerFontSize * 1.6, CONFIG.height - 340)
            : null;

        return { ...cue, font: verseFont, fontSize: verseFontSize, words, tafseerFont, tafseerWords };
    });
}

function drawSceneAtTime(time) {
    state.currentTime = time;
    drawGlobalBackground();

    if (parsedScenes.length === 0) return;
    const scene = parsedScenes.find(s => time >= s.start && time <= s.end) || parsedScenes[parsedScenes.length - 1];
    const progress = clamp01((time - scene.start) / (scene.end - scene.start));

    drawSurahHeader(scene.surah || SURAH_DISPLAY_NAME);
    drawQuranVerseScene(scene, progress);
    drawEnhancedTafseerPanel(scene, progress);
}
```

## ملاحظات معروفة
- **بطاقة التفسير بتعتمد على وجود `cue.tafseer`** — لو عنصر في `SURAH_VERSES`
  متبعتش ليه حقل `tafseer`، `tafseerWords` هيبقى `null` والبطاقة مش هتترسم
  أصلًا لهذه الآية (`drawEnhancedTafseerPanel` بترجع فورًا لو `!scene.tafseerWords`).
  لو المهمة عايزة تفسير، تأكد إن **كل** عنصر في `SURAH_VERSES` معاه `tafseer`.
- **`drawSurahHeader` بتقبل `scene.surah || SURAH_DISPLAY_NAME`**: لو حبيت
  تكتب اسم السورة داخل كل عنصر في `SURAH_VERSES` (حقل `surah`) بدل الاعتماد
  على `SURAH_DISPLAY_NAME` الموحّد، الكود بيدعم الحالتين.
- **مركز Y لنص الآية ثابت على `720`** جوه `buildParsedScenes` (مش نص الشاشة
  بالظبط) عشان يسيب مساحة كافية لبطاقة التفسير تحته — لو شيلت بطاقة التفسير
  في مهمة معيّنة، فكّر تغيّر الرقم ده لمركز الشاشة (`CONFIG.height / 2`) عشان
  الآية تبقى في المنتصف الفعلي من غير مساحة فاضية تحتها.
