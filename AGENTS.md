# AGENTS.md — دليل الوكيل لإنتاج فيديوهات (أي مجال محتوى، حسب ملف الهوية)

> ⚠️ **تنبيه حاسم**: `agent.js` مفيهوش أي subcommand اسمه "render". **ممنوع
> منعًا باتًا تنفّذ `node agent.js`** كأمر terminal من جوه جلستك — ده مش أداة
> رندر، ده نفس العقل اللي بيكلمك دلوقتي، وتشغيله هيبدأ جلسة Agent كاملة تانية
> من الصفر فوق نفس الريبو ونفس الـ Release، وهيضيع تقدمك الحالي بالكامل. الرندر
> سكريبت Node.js منفصل **إنت اللي بتكتبه** (`render-runner.js`) وتشغّله بـ
> `node render-runner.js` — النسخة الكاملة موجودة في القسم 5 تحت، انسخها زي ما هي.

---

## 1. مين انت

انت Agent مسؤول عن إنتاج فيديو كامل من الصفر — صوت وصورة ونص — في أي
مجال محتوى. مفيش مجال "افتراضي": المجال، شكل المحتوى، وطريقة جلب أصوله بالكامل
قرار ملف الهوية اللي المستخدم بيسميه. المستخدم بيقولك: اسم ملف هوية من
`identities/` + طلبه. إنت: تفتح الملف وتفهمه، تكتب `scene.html`، ترندره،
وترفعه على GitHub Release.

**استثناء**: لو المستخدم بعت كود مشهد جاهز (بأي شكل) وطلب استخراج هويته
البصرية كملف جديد جوه `identities/`، ده مش إنتاج فيديو عادي — اقرا `skill.md`
كامل الأول واتبعه بدقة، بدل خطوات القسم 2 تحت.

## 2. إزاي تكتب `scene.html`

1. افهم طلب المستخدم، وحدد أي ملف هوية من `identities/` طلبه بالاسم — لو مش
   واضح من طلبه، اسأله بدل ما تفترض أو "تتذكر" هوية استخدمتها قبل كده.
2. افتح الملف المطلوب واقرأه كامل، أكتر من مرة لو محتاج. هو **وصف تفصيلي**
   (أبعاد، ألوان، خطوط، حركة، شكل بيانات المحتوى المطلوبة، وطريقة جلب أي أصول
   محتاجها) — **مش كود جاهز تنسخه**. إنت اللي هتكتب الكود من فهمك له. لو فيه
   قسم "ملاحظات معروفة" في آخره، اقرأه.
3. كل حاجة عن شكل المحتوى المطلوب وطريقة جلب أصوله (صوت/صورة/فيديو) قرار الملف
   ده بس — مفيش افتراض هنا في `AGENTS.md`. أي نص أو بيانات مفروض حقيقيتها،
   مصدرها فعل حقيقي في نفس المهمة، بالطريقة اللي الملف حددها — مش من دماغك،
   
4. اكتب `scene.html`: **طبقة الهوية** (كودك انت، بناءً على فهمك للملف) فوق، ثم
   **الطبقة التقنية الثابتة** (تنسخه زى ما هو و يبقى قابل للتعديل فى حاله وحود اخطاء) تحت
   — الاتنين في نفس `<script type="module">` واحد.
5. طبقة الهوية لازم تعرّف بالاسم ده بالظبط عشان الطبقة التقنية تشتغل:
   `CONFIG` (`{fps, width, height, duration}`)، `OUTPUT_FILENAME`، `audioBuffer`
   (أو `null` صراحة لو من غير صوت)، `async prepareIdentity()`،
   `async drawSceneAtTime(time)` (دايمًا `async`). متاح ليك تلقائيًا من غير
   استيراد أو إعادة تعريف: `ctx`، `layoutArabicParagraph(...)`، `Easing`،
   `clamp01(val)`، `toArabicDigits(input)`، `fetchAndDecodeAudio(url)`،
   `concatenateAudioBuffers(buffers)`، `createBrollFrameSampler(url, options?)`.

6. قارن الكود اللي كتبته بند ببند مقابل كل قسم في ملف الهوية (الهندسة
والإحداثيات، الألوان، الخطوط وأحجامها، صيغ الحركة، الخلفية، الأصول، شكل
المحتوى) — مش بس قيم المحتوى. أي قيمة مكتوبة في ملف الهوية لازم تلاقي مقابلها
الدقيق في الكود، وإلا صححه قبل ما تكمل للرندر الكامل: اعرض `scene.html` بأمر `cat scene.html` واقرأه
   كامل من الملف الفعلي على القرص (**ممنوع `grep` أو فحص جزئي**) — قارن قيم
   بيانات المحتوى بالمطلوب فعليًا واتأكد ان كل المطلوب فى ملف الهويه تم تنفيذه في المهمة، وتأكد `prepareIdentity`/
   `drawSceneAtTime` موجودين بنفس الاسمين. بعد كده افتحه headless لكام ثانية
   بس واتأكد مفيش `pageerror` (التصدير الفعلي بقى بره المتصفح بالكامل، فمفيش
   `?autorender=true` ولا أي URL param محتاج تحطه هنا):
   ```js
   const { chromium } = require('playwright');
   const http = require('http');
   const fs = require('fs');
   const path = require('path');
   (async () => {
     const server = http.createServer((req, res) => {
       fs.readFile(path.join(process.cwd(), req.url.split('?')[0]), (err, data) => {
         if (err) { res.writeHead(404); res.end(); return; }
         res.writeHead(200); res.end(data);
       });
     });
     await new Promise(r => server.listen(0, r));
     const port = server.address().port;
     const browser = await chromium.launch({ channel: 'chrome' });
     const page = await browser.newPage();
     let errorFound = null;
     page.on('pageerror', (err) => { errorFound = err.message; });
     await page.goto(`http://localhost:${port}/scene.html`);
     await page.waitForTimeout(3000);
     await browser.close();
     server.close();
     if (errorFound) { console.log('SYNTAX_ERROR:', errorFound); process.exit(1); }
     console.log('SCENE_OK');
   })();
   ```
   لو طبع `SYNTAX_ERROR`، رجع اصلح `scene.html` الأول — ممنوع تكمّل على الرندر
   الكامل.

---

## 3. الطبقة التقنية 

هيكل `<head>`/`<body>` الثابت (الأماكن المعلّمة بـ 🎨 بتتملى من ملف الهوية):

```html
<!DOCTYPE html>
<html lang="🎨IDENTITY_LANG🎨" dir="🎨IDENTITY_DIR🎨">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🎨 IDENTITY: عنوان مناسب لمحتوى الفيديو (مع دعم Agent Auto-Render) 🎨</title>

    <!-- 🎨 IDENTITY: روابط الخطوط -->

    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/regular/style.css" />
    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/fill/style.css" />

    <!-- 🎨 IDENTITY: كتلة الـ CSS كاملة -->
    <style>
    </style>
</head>
<body>
    <div id="viewport">
        <!-- 🎨 IDENTITY: width/height من ملف الهوية -->
        <canvas id="shortsCanvas" width="1080" height="1920"></canvas>
        <div id="hud"><div class="spinner" id="spinner"></div><span id="status-text">جاري تحضير فيديو الشورتس العمودي...</span></div>
        <div id="controls-overlay">
            <button class="btn btn-preview" id="btn-replay"><i class="ph ph-arrow-counter-clockwise"></i> تشغيل المعاينة</button>
            <button class="btn btn-preview" id="btn-toggle-console"><i class="ph ph-terminal-window"></i> سجل الأخطاء</button>
        </div>
        <!-- ⚠️ اتشال زرار التصدير من هنا عمدًا: التصدير الفعلي بقى خارج المتصفح
        بالكامل عن طريق ffmpeg من render-runner.js (القسم 5). المتصفح هنا وظيفته
        رسم كل فريم لما render-runner.js يطلبه بس، مش تصدير ملف كامل بنفسه. -->
    </div>

    <div id="console-modal">
        <div id="console-header">
            <span><i class="ph ph-terminal-window"></i> سجل النظام والأخطاء</span>
            <button class="btn btn-preview" id="btn-close-console" style="color:#fff; background:#ff4444; border:none; padding:4px 14px;">إغلاق</button>
        </div>
        <div id="console-output"></div>
    </div>

    <script type="importmap">
    {
        "imports": {
            "mediabunny": "https://esm.sh/mediabunny@1.50.8"
        }
    }
    </script>

    <!-- ⚠️ mediabunny هنا مقصور على قراءة/فك فريمات فيديو B-roll بس (Input/CanvasSink
    جوه createBrollFrameSampler تحت). التصدير النهائي بقى بره المتصفح تمامًا، عن
    طريق ffmpeg من render-runner.js (القسم 5) — راجع القسم 3 كامل. -->
    <script type="module">
        import { Input, UrlSource, ALL_FORMATS, CanvasSink } from 'mediabunny';

        // ============ 🎨 طبقة الهوية بالكامل — هنا ============

        // ============ ⚙️ الطبقة التقنية الثابتة — من هنا تحت ============
    </script>
</body>
</html>
```

كود الطبقة التقنية الثابتة بالكامل (يحل محل التعليق الأخير فوق):

```js
const canvas = document.getElementById('shortsCanvas');
const ctx = canvas.getContext('2d', { willReadFrequently: true });
const statusText = document.getElementById('status-text');
const spinner = document.getElementById('spinner');

let audioAudioEl = null;
let sharedAudioCtx = null;
let state = { currentTime: 0, isRendering: false, animationFrameId: null };

// --- AI AGENT AUTOMATION HOOKS ---
// الحالة هنا بتغطي التحميل الأولي بس ('loading' → 'ready' أو 'error'). التصدير
// الفعلي بقى مُدار بالكامل من render-runner.js (فريم فريم عن طريق __renderFrame)،
// مش من هنا — فمفيش داعي لحالة 'rendering'/'completed' جوه الصفحة نفسها.
window.renderStatus = 'loading'; // 'loading' | 'ready' | 'error'

function logToConsole(msg, type = 'info') {
    const output = document.getElementById('console-output');
    const line = document.createElement('div');
    line.className = `log-line log-${type}`;
    line.textContent = `[${new Date().toLocaleTimeString()}] ${msg}`;
    output.appendChild(line);
    output.scrollTop = output.scrollHeight;
}

// --- أدوات عامة متاحة دايمًا لكود الهوية، من غير ما يعيد تعريفها ---
function clamp01(val) { return Math.max(0, Math.min(1, val)); }

function toArabicDigits(input) {
    const map = ['٠','١','٢','٣','٤','٥','٦','٧','٨','٩'];
    return String(input).replace(/[0-9]/g, d => map[+d]);
}

const Easing = {
    linear: t => t,
    easeOutCubic: t => 1 - Math.pow(1 - t, 3),
    easeInOutCubic: t => t < 0.5 ? 4 * t * t * t : 1 - Math.pow(-2 * t + 2, 3) / 2
};

function layoutArabicParagraph(text, font, maxWidth, wordGap, lineHeight, centerY) {
    ctx.font = font;
    const words = text.split(' ');
    const lines = [];
    let currentWords = [], currentWidth = 0;

    words.forEach(w => {
        const wordWidth = ctx.measureText(w).width;
        const testWidth = currentWidth + (currentWords.length > 0 ? wordGap : 0) + wordWidth;
        if (testWidth > maxWidth && currentWords.length > 0) {
            lines.push({ words: currentWords, width: currentWidth });
            currentWords = []; currentWidth = 0;
        }
        currentWords.push({ text: w, width: wordWidth });
        currentWidth += (currentWords.length > 1 ? wordGap : 0) + wordWidth;
    });
    if (currentWords.length) lines.push({ words: currentWords, width: currentWidth });

    const totalHeight = lines.length * lineHeight;
    const startY = centerY - totalHeight / 2 + lineHeight / 2;
    const flatWords = [];

    lines.forEach((line, li) => {
        const lineY = startY + li * lineHeight;
        let currentX = (CONFIG.width / 2) + (line.width / 2);
        line.words.forEach(w => {
            const wx = currentX - w.width;
            flatWords.push({ text: w.text, x: wx + w.width / 2, y: lineY });
            currentX -= (w.width + wordGap);
        });
    });
    return flatWords;
}

// --- تحويل AudioBuffer إلى WAV Blob — أداة عامة، مستخدمة هنا لمعاينة الصوت الحية بس ---
function audioBufferToWavBlob(buffer) {
    const numOfChan = buffer.numberOfChannels;
    const length = buffer.length * numOfChan * 2 + 44;
    const out = new DataView(new ArrayBuffer(length));
    let channels = [], sample, offset = 0, pos = 0;

    function setUint16(data) { out.setUint16(pos, data, true); pos += 2; }
    function setUint32(data) { out.setUint32(pos, data, true); pos += 4; }

    setUint32(0x46464952); setUint32(length - 8); setUint32(0x45564157);
    setUint32(0x20746d66); setUint32(16); setUint16(1); setUint16(numOfChan);
    setUint32(buffer.sampleRate); setUint32(buffer.sampleRate * 2 * numOfChan);
    setUint16(numOfChan * 2); setUint16(16); setUint32(0x61746164);
    setUint32(length - pos - 4);

    for (let i = 0; i < buffer.numberOfChannels; i++) channels.push(buffer.getChannelData(i));

    while (offset < buffer.length) {
        for (let i = 0; i < numOfChan; i++) {
            sample = Math.max(-1, Math.min(1, channels[i][offset]));
            sample = (0.5 + sample < 0 ? sample * 32768 : sample * 32767) | 0;
            out.setInt16(pos, sample, true);
            pos += 2;
        }
        offset++;
    }

    return new Blob([out], { type: "audio/wav" });
}

// --- Shared AudioContext — مستخدم داخليًا من fetchAndDecodeAudio/concatenateAudioBuffers ---
function getSharedAudioContext() {
    if (!sharedAudioCtx) sharedAudioCtx = new (window.AudioContext || window.webkitAudioContext)();
    return sharedAudioCtx;
}

// --- جلب وفكّ أي ملف صوت من أي رابط — أداة عامة، مالهاش علاقة بمصدر معيّن ---
// بترفض روابط http:// (لازم https://)، وبتتحقق إن حجم الملف مش صغير بشكل غير
// طبيعي (أقل من ~1 كيلوبايت، غالبًا صفحة خطأ مش صوت حقيقي) قبل ما تحاول تفكّه.
async function fetchAndDecodeAudio(url) {
    if (!/^https:\/\//i.test(url)) {
        throw new Error(`رابط صوت غير آمن (لازم https://): ${url}`);
    }
    const res = await fetch(url);
    if (!res.ok) throw new Error(`فشل جلب الصوت (HTTP ${res.status}): ${url}`);
    const arrayBuf = await res.arrayBuffer();
    if (arrayBuf.byteLength < 1024) {
        throw new Error(`حجم الملف صغير جدًا (${arrayBuf.byteLength} بايت) — على الأغلب صفحة خطأ مش صوت حقيقي: ${url}`);
    }
    return getSharedAudioContext().decodeAudioData(arrayBuf);
}

// --- تلزيق أكتر من AudioBuffer ورا بعض في واحد، وترجيع توقيتات كل مقطع (المدد الحقيقية) ---
function concatenateAudioBuffers(buffers) {
    if (!buffers || buffers.length === 0) throw new Error("مفيش أي AudioBuffer لتلزيقه");

    const sampleRate = buffers[0].sampleRate;
    const channelsCount = buffers[0].numberOfChannels;
    const totalSamples = buffers.reduce((sum, b) => sum + b.length, 0);
    const combined = getSharedAudioContext().createBuffer(channelsCount, totalSamples, sampleRate);

    let sampleOffset = 0;
    let timeOffset = 0.0;
    const segments = [];

    for (const buf of buffers) {
        for (let ch = 0; ch < channelsCount; ch++) {
            combined.getChannelData(ch).set(buf.getChannelData(ch), sampleOffset);
        }
        const duration = buf.duration;
        segments.push({ start: timeOffset, end: timeOffset + duration, duration });
        sampleOffset += buf.length;
        timeOffset += duration;
    }

    return { buffer: combined, segments };
}

// --- أداة عامة: تجيب فريم فيديو B-roll في أي وقت، باستخدام Input+CanvasSink من Mediabunny ---
// استخدام اختياري بالكامل — أي هوية تناديها جوه prepareIdentity() لو محتاجة
// خلفية فيديو (مش صورة ثابتة). نفس قاعدة CORS بتاعة الصور تنطبق هنا: الرابط
// لازم يدعم CORS فعليًا وإلا الكانفاس هيتعتبر "tainted" وقت الرسم عليه.
async function createBrollFrameSampler(url, options = {}) {
    const input = new Input({ source: new UrlSource(url), formats: ALL_FORMATS });
    const videoTrack = await input.getPrimaryVideoTrack();
    if (!videoTrack) throw new Error(`مفيش مسار فيديو (video track) في الرابط: ${url}`);

    const sink = new CanvasSink(videoTrack, {
        width: options.width,
        height: options.height,
        fit: options.fit || 'cover',
        poolSize: options.poolSize || 2,
    });

    return {
        input,
        videoTrack,
        async getFrameAt(time) {
            const wrapped = await sink.getCanvas(time);
            return wrapped ? wrapped.canvas : null;
        },
    };
}

// --- حلقة المعاينة الحية — بتنادي drawSceneAtTime بس (async دايمًا)، من غير أي منطق تصميم ---
function startPreviewLoop() {
    if (state.animationFrameId) cancelAnimationFrame(state.animationFrameId);
    if (audioAudioEl) {
        audioAudioEl.currentTime = 0;
        audioAudioEl.play().catch(e => logToConsole("تنبيه الصوت: " + e.message, 'warn'));
    }

    async function loop() {
        if (state.isRendering) return;
        const currTime = audioAudioEl ? audioAudioEl.currentTime : state.currentTime;
        await drawSceneAtTime(currTime);

        if (currTime < CONFIG.duration) {
            state.animationFrameId = requestAnimationFrame(loop);
        } else {
            statusText.textContent = "جاهز للعرض والتصدير ✓";
            spinner.style.display = 'none';
        }
    }
    state.animationFrameId = requestAnimationFrame(loop);
}

// تحويل ArrayBuffer إلى base64 — جسر عام لأي بيانات ثنائية لازم تعدي من الصفحة
// لـ render-runner.js (Blob/ArrayBuffer مبيرجعوش زي ما هما من page.evaluate)
function arrayBufferToBase64(buffer) {
    let binary = '';
    const bytes = new Uint8Array(buffer);
    const chunkSize = 0x8000;
    for (let i = 0; i < bytes.length; i += chunkSize) {
        binary += String.fromCharCode.apply(null, bytes.subarray(i, i + chunkSize));
    }
    return btoa(binary);
}

async function blobToBase64(blob) {
    return arrayBufferToBase64(await blob.arrayBuffer());
}

// --- EXPOSE AUTOMATION HOOKS FOR AI AGENTS ---
// التصدير بقى بره المتصفح تمامًا: render-runner.js (القسم 5) هو اللي بيلف على
// كل فريم بنفسه، وبيستخدم الفانكشنين دول بس كجسر خفيف — لا ترميز ولا mux هنا
// خالص، كله بيحصل بره في عملية ffmpeg منفصلة.

// بيترسم الفريم في الوقت المطلوب، ويرجّع الـ canvas كـ PNG base64 (من غير
// data:image/png;base64, prefix). بينادَى مرة لكل فريم من render-runner.js.
window.__renderFrame = async function (timestamp) {
    await drawSceneAtTime(timestamp);
    return canvas.toDataURL('image/png').split(',')[1];
};

// بيرجّع الصوت الكامل (لو موجود) كملف WAV بصيغة base64، مرة واحدة بس، عشان
// render-runner.js يكتبه ملف على القرص ويدّيه لـ ffmpeg كمدخل صوت للـ mux.
// null صراحة لو الهوية من غير صوت.
window.__getAudioWavBase64 = async function () {
    if (!audioBuffer) return null;
    return blobToBase64(audioBufferToWavBlob(audioBuffer));
};

// --- أزرار الواجهة (IDs ثابتة، موجودة في هيكل الـ HTML فوق) ---
document.getElementById('btn-replay').addEventListener('click', () => {
    statusText.textContent = "جاري عرض المعاينة...";
    spinner.style.display = 'inline-block';
    startPreviewLoop();
});

document.getElementById('btn-toggle-console').addEventListener('click', () => {
    const modal = document.getElementById('console-modal');
    modal.style.display = modal.style.display === 'flex' ? 'none' : 'flex';
});

document.getElementById('btn-close-console').addEventListener('click', () => {
    document.getElementById('console-modal').style.display = 'none';
});

async function init() {
    try {
        await prepareIdentity(); // 🎨 من طبقة الهوية بالكامل
        if (audioBuffer) {
            const wavBlob = audioBufferToWavBlob(audioBuffer);
            audioAudioEl = new Audio(URL.createObjectURL(wavBlob));
        }
        statusText.textContent = "جاهز للعرض ✓";
        spinner.style.display = 'none';
        // تعريض CONFIG/OUTPUT_FILENAME لـ render-runner.js — بيقرأهم بعد ما
        // renderStatus يبقى 'ready' عشان يحسب عدد الفريمات ويسمي الملف الناتج.
        window.CONFIG = CONFIG; // 🎨 من طبقة الهوية
        window.OUTPUT_FILENAME = OUTPUT_FILENAME; // 🎨 من طبقة الهوية
        window.renderStatus = 'ready';
        await drawSceneAtTime(0); // 🎨 من طبقة الهوية، async دايمًا
    } catch (err) {
        logToConsole("خطأ أثناء التهيئة: " + err.message, 'error');
        statusText.textContent = "حدث خطأ أثناء التحميل";
        window.__renderError = err.message;
        window.renderStatus = 'error';
    }
}

window.addEventListener('load', init);
```

**قاعدة "canvas tainted"**: `scene.html` لازم يُفتح دايمًا بسيرفر HTTP محلي، مش
`file://` (راجع `render-runner.js` في القسم 5). أي صورة أو فريم B-roll بيترسم
على الـ canvas لازم CORS فعليًا مفعّلة (`crossOrigin='anonymous'` للصور، رابط
بيدعم CORS لـ `createBrollFrameSampler`)، وإلا `canvas.toDataURL()` جوه
`__renderFrame` هيرمي `SecurityError: Failed to execute 'toDataURL' ... Tainted
canvases may not be exported` — نفس المشكلة القديمة، بس رسالة الخطأ اتغيّرت لأن
الاستخراج بقى عن طريق `toDataURL` مش `VideoFrame`.

---

## 4. قواعد سريعة

- ممنوع منعًا باتًا تنفيذ `node agent.js` — ده وكيل تاني كامل، مش أداة رندر.
- `render-runner.js` سكريبت منفصل تكتبه إنت (القسم 5) وتشغّله بـ `node render-runner.js`.
- `scene.html` يُفتح دايمًا بسيرفر HTTP محلي، مش `file://`.
- `chromium.launch({ channel: 'chrome' })` إلزامي (القناة المثبّتة في الـ CI، مش الافتراضية).
- أي انتظار لحالة الرندر لازم تايم آوت إجمالي صريح 8 دقايق على الأقل — لا تعتمد على أي default (`render-runner.js` تحت بيعمل ده تلقائيًا).
- ممنوع كتابة أي نص/بيانات مفروض حقيقيتها من الذاكرة — مصدرها فعل حقيقي في نفس المهمة.
- لو الهوية فيها صوت: التوقيت من مدة الصوت الحقيقي بعد فكّه (`fetchAndDecodeAudio`/`concatenateAudioBuffers`)، مش تخمين.
- بعد كتابة `scene.html`: `cat scene.html` كامل (لا `grep`)، وقارن قيم المحتوى بالمطلوب فعليًا.
- قبل الرندر الكامل: فحص headless سريع (القسم 2، بند 6) — لو `SYNTAX_ERROR`، رجّع اصلح `scene.html` أولًا.
- لو الرندر فشل: صحّح `scene.html` مباشرة 
- الفيديو الناتج دايمًا `.mp4` (H.264/AAC عبر ffmpeg) — الاسم `${OUTPUT_FILENAME}.mp4` بالظبط، ممنوع تغييره يدويًا.
- الاستخراج بقى فريم فريم عبر `page.evaluate` (مش ترميز مباشر جوه المتصفح)،
  فالرندر ممكن ياخد وقت أطول شوية من قبل خصوصًا لفيديو أطول من ~60 ثانية — لو
  التايم آوت (8 دقايق) بيتحقق قبل الاكتمال بشكل متكرر مع نفس الهوية، كبّر
  `TIMEOUT_MS` في `render-runner.js` بدل ما تحاول تقلل جودة الرسم.
- لو بتلاقي نفسك بتكرر نفس المحاولة والخطأ أكتر من مرتين على نفس المشكلة، وقف وقارن بنسخة شغالة معروفة بدل ما تكمل تخمين.
- لو ملف الهوية فيه قسم "ملاحظات معروفة" في آخره، اقرأه قبل ما تبدأ.
- ملف الوصف المرفوع مع الفيديو: وصف حقيقي مختصر للمحتوى الفعلي، المدة الكلية، وجود شرح/تفسير إضافي أم لا، ورابط الـ Release.
- الفيديو + ملف الوصف + `scene.html` نفسه — التلاتة يترفعوا كـ assets حقيقية على الـ Release.
- ممنوع منعًا باتًا إنشاء أي ملف أو بيانات وهمية/placeholder (فيديو فاضي بالأصفار،
  صوت صامت مُختلَق، نص "هيتضاف بعدين") لإرضاء أي فحص أو خطوة رفع أو تحقق —
  لو عجزت فعليًا عن تنفيذ خطوة، وقف وأعلن الفشل صراحة في ردك النصي، ولا تلفّق
  نتيجة تبان ناجحة وهي مش حقيقية.
---

## 5. خطوات التنفيذ

1. حدد ملف الهوية المطلوب من طلب المستخدم (اسأله لو غير واضح).
2. افتح الملف واقرأه كامل، وابدأ فعليًا في جلب أي محتوى/بيانات حقيقية محتاجها المهمة بالطريقة اللي الملف حددها.
3. اكتب `scene.html` وراجعه مرتين على الاقل للتأكد انه مطابق تماما للهوية 
4. فحص headless سريع 
5. شغّل `node render-runner.js` (السكريبت تحت).
6. تأكد الرفع الفعلي لكل من: الفيديو، ملف الوصف، `scene.html` (`gh release upload`).
7. اكتب ملف علامة `video_<معرّف فريد>_done.json` يحتوي
   `{"identifier": "...", "release_video_url": "...", "release_md_url": "...", "release_scene_html_url": "..."}`
   لكل فيديو — `agent.js` بيتحقق إن الروابط دي موجودة فعليًا على الـ Release
   (`gh release view`) قبل ما يقبل الملف. بعد آخر فيديو في المهمة، اكتب
   `TASK_COMPLETE.json` يحتوي `{"summary": "...", "videos": [...]}`.

### `render-runner.js`

المعمارية اتغيّرت جوهريًا هنا: المتصفح بقى بيرسم فريم واحد بس لما تطلبه منه
(`window.__renderFrame(timestamp)`)، وإنت (Node) اللي بتلف على كل الفريمات
وتبعتها **مباشرة عبر pipe** لعملية `ffmpeg` منفصلة شغالة في الخلفية — من غير ما
تكتب ولا فريم واحد كملف على القرص. `ffmpeg` هو اللي بيعمل الترميز والـ mux
النهائي (فيديو H.264 + صوت AAC داخل حاوية MP4)، مش المتصفح.

```js
const { chromium } = require('playwright');
const { spawn } = require('child_process');
const fs = require('fs');
const path = require('path');
const http = require('http');

// تايم آوت إجمالي إلزامي — رندر فيديو حقيقي بياخد دقايق، مش قابل للحذف أو
// التقليل. لو فيديو أطول من المعتاد (أكتر من دقيقة بصريح)، كبّره يدويًا هنا.
const TIMEOUT_MS = 8 * 60 * 1000;

function startServer() {
  return new Promise((resolve) => {
    const server = http.createServer((req, res) => {
      const filePath = path.join(process.cwd(), decodeURIComponent(req.url.split('?')[0]));
      fs.readFile(filePath, (err, data) => {
        if (err) { res.writeHead(404); res.end(); return; }
        res.writeHead(200); res.end(data);
      });
    });
    server.listen(0, () => resolve(server));
  });
}

(async () => {
  const server = await startServer();
  const port = server.address().port;
  const browser = await chromium.launch({ channel: 'chrome' }); // مطابق للقناة المثبّتة في الـ CI
  const page = await browser.newPage();

  const consoleLogs = [];
  const failedRequests = [];
  page.on('console', (msg) => consoleLogs.push(`[${msg.type()}] ${msg.text()}`));
  page.on('pageerror', (err) => consoleLogs.push(`[pageerror] ${err.message}`));
  page.on('requestfailed', (req) => failedRequests.push(`${req.url()} — ${req.failure()?.errorText}`));
  page.on('response', (res) => { if (res.status() >= 400) failedRequests.push(`${res.url()} — HTTP ${res.status()}`); });

  await page.goto(`http://localhost:${port}/scene.html`);

  const startTime = Date.now();
  const result = { success: false, elapsed_seconds: 0, console_logs: [], failed_requests: [] };

  function finish(exitCode) {
    result.elapsed_seconds = Number(((Date.now() - startTime) / 1000).toFixed(1));
    result.console_logs = consoleLogs.slice(-50);
    result.failed_requests = failedRequests;
    console.log(JSON.stringify(result)); // اقرأها من الـ output بتاع run_terminal مباشرة
    process.exit(exitCode);
  }

  // --- خطوة 1: استنى الصفحة توصل 'ready' (يعني prepareIdentity نجح فعليًا) ---
  let status = null;
  while (true) {
    status = await page.evaluate(() => window.renderStatus);
    if (status === 'ready' || status === 'error') break;
    if (Date.now() - startTime > TIMEOUT_MS) { status = 'timeout'; break; }
    await page.waitForTimeout(300);
  }

  if (status !== 'ready') {
    result.error = status === 'error'
      ? await page.evaluate(() => window.__renderError)
      : `TimeoutError: الصفحة ما وصلتش لحالة ready خلال ${TIMEOUT_MS / 1000}s`;
    await browser.close();
    server.close();
    finish(1);
    return;
  }

  const config = await page.evaluate(() => window.CONFIG);
  const outputFilename = await page.evaluate(() => window.OUTPUT_FILENAME);
  const totalFrames = Math.ceil(config.duration * config.fps);
  const outputPath = path.join(process.cwd(), `${outputFilename}.mp4`);

  // --- خطوة 2: لو فيه صوت، اسحبه WAV واحد بس واكتبه ملف مؤقت عشان ffmpeg يعمله mux ---
  const audioBase64 = await page.evaluate(() => window.__getAudioWavBase64());
  const audioPath = path.join(process.cwd(), '__render_audio_tmp.wav');
  if (audioBase64) fs.writeFileSync(audioPath, Buffer.from(audioBase64, 'base64'));

  // --- خطوة 3: افتح ffmpeg كعملية خلفية، بياخد فريمات PNG عبر stdin (image2pipe) ---
  const ffmpegArgs = ['-y', '-f', 'image2pipe', '-framerate', String(config.fps), '-i', '-'];
  if (audioBase64) ffmpegArgs.push('-i', audioPath);
  ffmpegArgs.push('-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-preset', 'medium', '-crf', '18');
  if (audioBase64) ffmpegArgs.push('-c:a', 'aac', '-b:a', '192k', '-shortest');
  ffmpegArgs.push('-movflags', '+faststart', outputPath);

  const ffmpeg = spawn('ffmpeg', ffmpegArgs);
  let ffmpegStderr = '';
  ffmpeg.stderr.on('data', (d) => { ffmpegStderr += d.toString(); });
  const ffmpegDone = new Promise((resolve, reject) => {
    ffmpeg.on('close', (code) => (code === 0 ? resolve() : reject(new Error(`ffmpeg exited with code ${code}`))));
    ffmpeg.on('error', reject); // مثلاً ffmpeg مش مثبّت أصلاً على الـ runner
  });

  // --- خطوة 4: للف فريم فريم — نادي على drawSceneAtTime جوه الصفحة، اسحب PNG، ابعته لـ ffmpeg ---
  let lastPercent = -1;
  try {
    for (let i = 0; i < totalFrames; i++) {
      const timestamp = i / config.fps;
      const base64Png = await page.evaluate((t) => window.__renderFrame(t), timestamp);
      const ok = ffmpeg.stdin.write(Buffer.from(base64Png, 'base64'));
      if (!ok) await new Promise((r) => ffmpeg.stdin.once('drain', r));

      const percent = Math.round(((i + 1) / totalFrames) * 100);
      if (percent !== lastPercent) {
        const elapsed = ((Date.now() - startTime) / 1000).toFixed(1);
        console.log(`[+${elapsed}s] frame ${i + 1}/${totalFrames} (${percent}%)`);
        lastPercent = percent;
      }
      if (Date.now() - startTime > TIMEOUT_MS) {
        throw new Error(`TimeoutError: تجاوز الرندر ${TIMEOUT_MS / 1000}s (آخر فريم: ${i}/${totalFrames})`);
      }
    }
    ffmpeg.stdin.end();
    await ffmpegDone;

    result.success = true;
    result.filename = outputPath;
    result.size = fs.statSync(outputPath).size;
  } catch (err) {
    try { ffmpeg.kill('SIGKILL'); } catch (_) {}
    result.error = `${err.message}${ffmpegStderr ? ' | ffmpeg stderr (آخر جزء): ' + ffmpegStderr.slice(-800) : ''}`;
  } finally {
    if (audioBase64) { try { fs.unlinkSync(audioPath); } catch (_) {} }
  }

  result.last_progress_percent = lastPercent;
  await browser.close();
  server.close();
  finish(result.success ? 0 : 1);
})();
```

**استخدم `console_logs`/`failed_requests` مباشرة للتشخيص** — لو ملف صوت أو خط
طلع 404 هتلاقيه صريح في `failed_requests`. لو فشل الرندر، `last_progress_percent`
بيوريك لحد فين وصل قبل ما يفشل أو يتوقف. لو الخطأ فيه `ffmpeg exited with code`
أو `ffmpeg stderr`، المشكلة في الترميز نفسه (مثلاً `ffmpeg` مش موجود على
الـ runner) مش في الرسم — راجع خطوة تثبيت `ffmpeg` في
`.github/workflows/render.yml`.
