# هوية "أفق الليلية" (Ofoq Night) — سينمائية داكنة، تلاوة بس

## الوصف
طابع سينمائي داكن حديث: خلفية غرادينت داكنة (كحلي غامق → أسود تقريبًا) مع
فينييت (تعتيم أطراف)، نص أبيض بارز بظل قوي، ولمسة ذهبية دافئة للفواصل والحدود.
هيدر ثابت باسم السورة والقارئ أعلى الشاشة، وعداد آيات صغير ذهبي أسفلها
(بالأرقام العربية-الهندية). دقة أصغر من باقي الهويات (720×1280 بدل 1080×1920،
نفس نسبة 9:16). مفيش بانل تفسير — تلاوة minimal بس.

> **نشأة الهوية دي**: اتبنت بمنطق "استيعاب ستايل فقط" (القسم 1.5 في
> `AGENTS.md`) من ملف خارجي (هوية "Ofoq Studio") فشل في كل بنود فحص التوافق
> الستة — مفيهوش أي render hooks، مصدره الصوتي كان `quranicaudio.com` بدل
> `everyayah.com`، خلفيته صورة من Unsplash، وتوقيت آياته كان مكتوب يدويًا مش
> محسوب من مدة صوت حقيقية. **مفيش أي سطر من كوده التنفيذي اتنقل هنا** — بس
> الألوان، الخطوط، وفكرة الرأس الثابت + الفينييت + العداد.

- **الأبعاد**: 720×1280 (Shorts عمودي، 9:16، دقة أصغر)، 60fps.
- **حالة الاستخدام**: تلاوة قصيرة/سورة صغيرة، طابع عصري بدل الرَّق الكلاسيكي.
- **حقول `SURAH_VERSES`**: `text` بس، مفيش حاجة إضافية مطلوبة.

## روابط الخطوط
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400;1,700&family=Reem+Kufi:wght@400;500;600;700;800&family=IBM+Plex+Sans+Arabic:wght@400;500;600;700&display=swap" rel="stylesheet">
```

## CSS كامل
```css
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
    background-color: #0a0a0c; color: #f2f2f2;
    font-family: 'IBM Plex Sans Arabic', -apple-system, BlinkMacSystemFont, sans-serif;
    display: flex; align-items: center; justify-content: center;
    height: 100vh; width: 100vw; overflow: hidden;
}
#viewport { position: relative; width: 100%; height: 100%; display: flex; align-items: center; justify-content: center; padding: 2vh; }
canvas {
    background: #05060a; box-shadow: 0 0 90px rgba(0, 0, 0, 0.65);
    border-radius: 18px; border: 1px solid rgba(212, 175, 55, 0.25);
    max-width: 100%; max-height: 86vh; width: auto; height: auto;
    aspect-ratio: 9 / 16; object-fit: contain;
}
#hud {
    position: absolute; top: 3vh; right: 4vw;
    background: rgba(18, 18, 22, 0.85); border: 1px solid rgba(255, 255, 255, 0.1); border-radius: 999px;
    padding: 1.2vh 2.5vw; display: flex; align-items: center; gap: 12px;
    font-size: 14px; font-weight: 600; color: #f2f2f2; backdrop-filter: blur(12px); z-index: 100;
    font-family: 'IBM Plex Sans Arabic', sans-serif;
}
.spinner { width: 18px; height: 18px; border: 2px solid #D4AF37; border-top-color: transparent; border-radius: 50%; animation: spin 0.8s linear infinite; }
@keyframes spin { to { transform: rotate(360deg); } }
#controls-overlay {
    position: absolute; bottom: 3vh; display: flex; gap: 12px; z-index: 100;
    background: rgba(18, 18, 22, 0.85); border: 1px solid rgba(255, 255, 255, 0.1);
    padding: 1.2vh 2vw; border-radius: 999px; backdrop-filter: blur(10px);
    max-width: 92vw; flex-wrap: wrap; justify-content: center;
    font-family: 'IBM Plex Sans Arabic', sans-serif;
}
.btn {
    background: none; border: none; color: #f2f2f2;
    font-family: 'IBM Plex Sans Arabic', sans-serif;
    font-size: 14px; font-weight: 600; cursor: pointer; padding: 8px 18px; border-radius: 999px;
    display: flex; align-items: center; gap: 6px; transition: all 0.3s ease;
}
.btn-preview { background: rgba(255, 255, 255, 0.06); border: 1px solid rgba(255, 255, 255, 0.12); }
.btn-preview:hover { background: rgba(255, 255, 255, 0.12); }
.btn-render { background: #D4AF37; color: #14100a; border: 1px solid #D4AF37; }
.btn-render:hover { background: #e6c358; }

#console-modal {
    display: none; position: fixed; top: 8vh; left: 8vw; width: 84vw; height: 75vh;
    background: rgba(8, 8, 10, 0.97); border: 1px solid #2a2a2e; border-radius: 16px;
    z-index: 999; color: #00ff8c; font-family: monospace; padding: 20px;
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
.log-info { color: #00ff8c; }
.log-warn { color: #ffcc00; }
.log-error { color: #ff5555; font-weight: bold; }
```

## أبعاد الكانفاس
```html
<canvas id="shortsCanvas" width="720" height="1280"></canvas>
```

## كود JS — تصميم الهوية
انسخ الكتلة دي حرفيًا في "منطقة 2/3 — تصميم الهوية" جوه `scene.html`.

```js
// --- CONFIG & PALETTE (Vertical Shorts 720x1280 — أفق الليلية) ---
let CONFIG = { fps: 60, width: 720, height: 1280, duration: 22.0 };
const QURAN_FONT = "'Amiri', serif";
const HEADER_FONT = "'Reem Kufi', sans-serif";
const VERSE_CENTER_Y = CONFIG.height / 2 + 60;

const PALETTE = {
    bgTop: "#1B1F2A",        // Deep Slate Navy
    bgBottom: "#05060A",     // Near Black
    textPrimary: "#FFFFFF",
    textMuted: "rgba(255, 255, 255, 0.72)",
    accentGold: "#D4AF37",   // Warm Gold Accent
    lineBorder: "rgba(212, 175, 55, 0.30)",
    vignette: "rgba(0, 0, 0, 0.55)"
};

function drawGlobalBackground() {
    const grad = ctx.createLinearGradient(0, 0, 0, CONFIG.height);
    grad.addColorStop(0, PALETTE.bgTop);
    grad.addColorStop(1, PALETTE.bgBottom);
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, CONFIG.width, CONFIG.height);

    const vignette = ctx.createRadialGradient(
        CONFIG.width / 2, CONFIG.height / 2, CONFIG.width / 4,
        CONFIG.width / 2, CONFIG.height / 2, CONFIG.height / 1.4
    );
    vignette.addColorStop(0, 'rgba(0, 0, 0, 0)');
    vignette.addColorStop(1, PALETTE.vignette);
    ctx.fillStyle = vignette;
    ctx.fillRect(0, 0, CONFIG.width, CONFIG.height);

    ctx.strokeStyle = PALETTE.lineBorder;
    ctx.lineWidth = 2;
    ctx.strokeRect(28, 28, CONFIG.width - 56, CONFIG.height - 56);
}

function drawTopHeader(time) {
    const fadeInDuration = 1.2;
    const alpha = clamp01(time / fadeInDuration);

    ctx.save();
    ctx.globalAlpha = alpha;

    ctx.shadowColor = "rgba(0, 0, 0, 0.7)";
    ctx.shadowBlur = 10;
    ctx.shadowOffsetY = 3;

    ctx.fillStyle = PALETTE.textPrimary;
    ctx.font = `700 42px ${HEADER_FONT}`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(SURAH_DISPLAY_NAME, CONFIG.width / 2, 130);

    ctx.fillStyle = PALETTE.textMuted;
    ctx.font = `500 22px ${HEADER_FONT}`;
    ctx.fillText(RECITER_DISPLAY_NAME, CONFIG.width / 2, 172);

    ctx.shadowBlur = 0;
    ctx.strokeStyle = PALETTE.accentGold;
    ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.moveTo(CONFIG.width / 2 - 70, 202);
    ctx.lineTo(CONFIG.width / 2 + 70, 202);
    ctx.stroke();

    ctx.restore();
}

function drawAyahCounter(activeScene, total) {
    if (!activeScene) return;
    const label = `${toArabicDigits(activeScene.id)} / ${toArabicDigits(total)}`;
    ctx.save();
    ctx.fillStyle = PALETTE.accentGold;
    ctx.font = `600 26px ${HEADER_FONT}`;
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.globalAlpha = 0.9;
    ctx.fillText(label, CONFIG.width / 2, CONFIG.height - 90);
    ctx.restore();
}

// --- الدالتين الإلزاميتين ---
function buildParsedScenes() {
    parsedScenes = RAW_CUES.map(cue => {
        const verseFontSize = cue.text.length > 30 ? 52 : 62;
        const verseFont = `700 ${verseFontSize}px ${QURAN_FONT}`;
        const words = layoutArabicParagraph(cue.text, verseFont, 580, 16, verseFontSize * 1.55, VERSE_CENTER_Y);
        return { ...cue, font: verseFont, fontSize: verseFontSize, words };
    });
}

function drawSceneAtTime(time) {
    state.currentTime = time;
    drawGlobalBackground();
    drawTopHeader(time);

    const activeScene = parsedScenes.find(s => time >= s.start && time < s.end);
    if (activeScene) {
        const sceneDuration = activeScene.end - activeScene.start;
        const localTime = time - activeScene.start;

        const fadeInDuration = 0.5;
        const fadeOutDuration = 0.4;

        let progressFactor = 1.0;
        if (localTime < fadeInDuration) {
            progressFactor = localTime / fadeInDuration;
        } else if (localTime > sceneDuration - fadeOutDuration) {
            progressFactor = (sceneDuration - localTime) / fadeOutDuration;
        }

        const b = Easing.easeOutCubic(clamp01(progressFactor));
        const alpha = b;
        const offsetY = (1.0 - b) * 24;
        const scale = 0.95 + (0.05 * b);

        ctx.save();
        ctx.globalAlpha = alpha;

        const centerX = CONFIG.width / 2;
        const centerY = VERSE_CENTER_Y;
        ctx.translate(centerX, centerY - offsetY);
        ctx.scale(scale, scale);
        ctx.translate(-centerX, -centerY);

        ctx.shadowColor = "rgba(0, 0, 0, 0.85)";
        ctx.shadowBlur = 18;
        ctx.shadowOffsetY = 4;

        ctx.fillStyle = PALETTE.textPrimary;
        ctx.font = activeScene.font;
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';

        activeScene.words.forEach(word => {
            ctx.fillText(word.text, word.x, word.y);
        });

        ctx.restore();

        drawAyahCounter(activeScene, parsedScenes.length);
    }
}
```

## ملاحظات معروفة
- **مفيش بانل تفسير في الهوية دي أصلًا** (تلاوة بس، minimal) — لو مهمة طلبت
  تفسير بهذا الستايل، محتاج تضيف حقل `tafseer` لكل عنصر في `SURAH_VERSES`
  وترسمه بمنطق شبيه بلي في `identities/brown-style.md` (بطاقة بيضاء)، بس
  ألوانها لازم تتغيّر عشان تتماشى مع الخلفية الداكنة هنا — بطاقة بيضاء زي
  brown-style هتتنافر بصريًا مع خلفية أفق الليلية الداكنة من غير تعديل.
- **`drawTopHeader` و`drawAyahCounter` بيعتمدوا على `SURAH_DISPLAY_NAME` و
  `RECITER_DISPLAY_NAME`** (من طبقة محتوى المهمة) — لازم يتحدّثوا مع
  `SURAH_NUMBER`/`RECITER_ID` مع بعض، وإلا هيظهر اسم سورة أو قارئ غلط في
  الفيديو حتى لو الصوت والنص صح.
- **أبعاد الكانفاس 720×1280 مش 1080×1920** (نفس نسبة 9:16 بس دقة أقل) — لو
  المهمة محتاجة دقة أعلى بنفس الستايل، غيّر `CONFIG.width`/`CONFIG.height` في
  `<canvas>` وفي الـ JS مع بعض، وراجع إن `VERSE_CENTER_Y` وقيم `maxWidth` في
  نداء `layoutArabicParagraph` جوه `buildParsedScenes` اتناسبوا مع الأبعاد
  الجديدة (مش هيبقوا مظبوطين تلقائيًا).
- **`drawSceneAtTime` هنا بتستخدم `find(s => time >= s.start && time < s.end)`
  من غير fallback لآخر مشهد** (بعكس `brown-style.md`/`Quran.md` اللي بيعملوا
  `|| parsedScenes[last]`) — يعني لو `time` طلعت أكبر من نهاية آخر آية بهامش
  صغير (نادر لكن وارد بسبب تقريب الفريمات)، ممكن الفريم الأخير يطلع من غير
  نص. مش مشكلة عمليًا لأن التصدير بيوقف عند `CONFIG.duration` بالظبط، بس لو
  لاحظت الفريم الأخير فاضي، ضيف نفس الـ fallback.
