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
6. قبل ما تكمّل للرندر الكامل: اعرض `scene.html` بأمر `cat scene.html` واقرأه
   كامل من الملف الفعلي على القرص (**ممنوع `grep` أو فحص جزئي**) — قارن قيم
   بيانات المحتوى بالمطلوب فعليًا في المهمة، وتأكد `prepareIdentity`/
   `drawSceneAtTime` موجودين بنفس الاسمين. بعد كده افتحه headless لكام ثانية
   بس (من غير `?autorender=true`) واتأكد مفيش `pageerror`:
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

## 3. الطبقة التقنية الثابتة — نسخة حرفية، بدون أي اختصار

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
            <button class="btn btn-render" id="btn-render-start"><i class="ph-fill ph-video-camera"></i> تصدير Shorts (MP4 + صوت)</button>
        </div>
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
            "mediabunny": "https://esm.sh/mediabunny@1.50.8",
            "@mediabunny/aac-encoder": "https://esm.sh/@mediabunny/aac-encoder@1.50.8?deps=mediabunny@1.50.8"
        }
    }
    </script>

    <script type="module">
        import { Output, Mp4OutputFormat, WebMOutputFormat, BufferTarget, CanvasSource, AudioBufferSource, QUALITY_HIGH, canEncodeAudio, Input, UrlSource, ALL_FORMATS, CanvasSink } from 'mediabunny';

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
window.renderStatus = 'loading'; // 'loading' | 'ready' | 'rendering' | 'completed' | 'error'
window.renderProgress = 0.0;
window.renderResult = null;

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

// --- AAC Encoder Polyfill — إلزامي على بيئة الـ CI (Chrome على Linux مفيهوش
// AAC encoder أصلي في WebCodecs، سبب تاريخي مرتبط برخصة AAC) ---
async function ensureAacEncoderAvailable() {
    if (!(await canEncodeAudio('aac'))) {
        logToConsole("تسجيل AAC Polyfill للأنظمة غير المدعومة أصليًا...");
        const { registerAacEncoder } = await import('@mediabunny/aac-encoder');
        registerAacEncoder();
    }
}

function getAudioConfigForContainer(container) {
    if (container === 'webm') return { codec: 'opus', bitrate: 128_000 };
    return { codec: 'aac', bitrate: QUALITY_HIGH };
}
// ملاحظة حاسمة: 'avc'/'aac'/'opus' هنا نصوص عادية (strings)، مش قيم مستوردة —
// لا يوجد export اسمه VideoCodec/AudioCodec وقت التشغيل (TypeScript type بس).

async function attemptRealExport(attempt, totalFrames, fps) {
    const format = attempt.container === 'webm' ? new WebMOutputFormat() : new Mp4OutputFormat();
    const output = new Output({ format, target: new BufferTarget() });
    const videoSource = new CanvasSource(canvas, attempt);
    const audioSource = new AudioBufferSource(getAudioConfigForContainer(attempt.container));

    output.addVideoTrack(videoSource, { frameRate: fps });
    output.addAudioTrack(audioSource);

    await output.start();
    if (audioBuffer) await audioSource.add(audioBuffer);
    audioSource.close();

    const frameDuration = 1 / fps;
    for (let i = 0; i < totalFrames; i++) {
        const timestamp = i / fps;
        window.renderProgress = timestamp / CONFIG.duration;
        await drawSceneAtTime(timestamp);
        await videoSource.add(timestamp, frameDuration);
    }
    videoSource.close();
    await output.finalize();
    return output.target.buffer;
}

// تحويل ArrayBuffer إلى base64 — جسر لسكريبت الـ Playwright (Blob مينفعش يترجع من page.evaluate)
function arrayBufferToBase64(buffer) {
    let binary = '';
    const bytes = new Uint8Array(buffer);
    const chunkSize = 0x8000;
    for (let i = 0; i < bytes.length; i += chunkSize) {
        binary += String.fromCharCode.apply(null, bytes.subarray(i, i + chunkSize));
    }
    return btoa(binary);
}

async function exportWithFallback() {
    state.isRendering = true;
    window.renderStatus = 'rendering';
    if (state.animationFrameId) cancelAnimationFrame(state.animationFrameId);
    if (audioAudioEl) audioAudioEl.pause();

    spinner.style.display = 'inline-block';
    statusText.textContent = "جاري تصدير فيديو الشورتس فريم فريم...";
    logToConsole(`بدء عملية التصدير (${CONFIG.width}x${CONFIG.height}, ${OUTPUT_FILENAME})...`);

    await ensureAacEncoderAvailable();

    const videoAttempts = [
        { codec: 'avc', bitrate: QUALITY_HIGH, container: 'mp4' },
        { codec: 'avc', bitrate: 3_500_000, container: 'mp4' },
        { codec: 'avc', fullCodecString: 'avc1.42001f', bitrate: 3_000_000, container: 'mp4' },
        { codec: 'vp9', bitrate: 4_000_000, container: 'webm' },
        { codec: 'vp8', bitrate: 3_000_000, container: 'webm' }
    ];

    const totalFrames = Math.ceil(CONFIG.duration * CONFIG.fps);

    for (const attempt of videoAttempts) {
        try {
            logToConsole(`تجربة التصدير بـ ${attempt.codec} (${attempt.bitrate ? (attempt.bitrate/1e6)+'Mbps' : 'auto'}) داخل حاوية ${attempt.container}...`);
            const buffer = await attemptRealExport(attempt, totalFrames, CONFIG.fps);
            logToConsole(`تم التصدير بنجاح! نوع الحاوية: ${attempt.container}`);

            const mimeType = attempt.container === 'webm' ? 'video/webm' : 'video/mp4';
            const blob = new Blob([buffer], { type: mimeType });
            const url = URL.createObjectURL(blob);

            window.renderResult = { blob, url, container: attempt.container };

            // امتداد الملف بيتحدد فعليًا من نوع الحاوية الحقيقي اللي نجح — ممنوع
            // لأي سكريبت خارجي يغيّره لاحقًا.
            window.__renderFilename = `${OUTPUT_FILENAME}.${attempt.container}`;
            window.__renderBase64 = arrayBufferToBase64(buffer);

            window.renderStatus = 'completed';
            window.renderProgress = 1.0;
            window.dispatchEvent(new CustomEvent('video-render-complete', { detail: window.renderResult }));

            const a = document.createElement('a');
            a.href = url;
            a.download = `${OUTPUT_FILENAME}.${attempt.container}`;
            a.click();

            statusText.textContent = "تم التصدير والتحميل بنجاح ✓";
            spinner.style.display = 'none';
            state.isRendering = false;
            return window.renderResult;
        } catch (err) {
            logToConsole(`محاولة ${attempt.codec} لم تكتمل: ${err.message}`, 'warn');
        }
    }

    statusText.textContent = "فشل التصدير! راجع سجل الأخطاء.";
    spinner.style.display = 'none';
    state.isRendering = false;
    window.__renderError = "فشلت جميع محاولات التصدير";
    window.renderStatus = 'error';
    throw new Error("فشلت جميع محاولات التصدير");
}

// --- EXPOSE AUTOMATION HOOKS FOR AI AGENTS ---
window.startVideoRender = exportWithFallback;

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

document.getElementById('btn-render-start').addEventListener('click', () => {
    exportWithFallback();
});

async function init() {
    try {
        await prepareIdentity(); // 🎨 من طبقة الهوية بالكامل
        if (audioBuffer) {
            const wavBlob = audioBufferToWavBlob(audioBuffer);
            audioAudioEl = new Audio(URL.createObjectURL(wavBlob));
        }
        statusText.textContent = "جاهز للعرض والتصدير ✓";
        spinner.style.display = 'none';
        window.renderStatus = 'ready';
        await drawSceneAtTime(0); // 🎨 من طبقة الهوية، async دايمًا

        // AUTOMATED AGENT TRIGGER via URL Parameter: ?autorender=true
        const urlParams = new URLSearchParams(window.location.search);
        if (urlParams.get('autorender') === 'true' || urlParams.get('autoexport') === 'true') {
            logToConsole("🤖 [AI Agent Mode]: تم اكتشاف أمر الريندر التلقائي في رابط الصفحة — جاري التصدير التلقائي...", 'info');
            setTimeout(() => { exportWithFallback(); }, 600);
        }
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
بيدعم CORS لـ `createBrollFrameSampler`)، وإلا خطأ
`VideoFrames can't be created from tainted sources`.

---

## 4. قواعد سريعة

- ممنوع منعًا باتًا تنفيذ `node agent.js` — ده وكيل تاني كامل، مش أداة رندر.
- `render-runner.js` سكريبت منفصل تكتبه إنت (القسم 5) وتشغّله بـ `node render-runner.js`.
- `scene.html` يُفتح دايمًا بسيرفر HTTP محلي، مش `file://`.
- `chromium.launch({ channel: 'chrome' })` إلزامي (القناة المثبّتة في الـ CI، مش الافتراضية).
- أي انتظار لحالة الرندر لازم تايم آوت إجمالي صريح 8 دقايق على الأقل — لا تعتمد على أي default (`render-runner.js` تحت بيعمل ده تلقائيًا).
- ممنوع تحميل أي صوت/صورة/فيديو محليًا بـ `curl` والإشارة له بمسار محلي — الجلب دايمًا وقت التشغيل جوه المتصفح، بالطريقة اللي ملف الهوية حددها.
- ممنوع كتابة أي نص/بيانات مفروض حقيقيتها من الذاكرة — مصدرها فعل حقيقي في نفس المهمة.
- لو الهوية فيها صوت: التوقيت من مدة الصوت الحقيقي بعد فكّه (`fetchAndDecodeAudio`/`concatenateAudioBuffers`)، مش تخمين.
- بعد كتابة `scene.html`: `cat scene.html` كامل (لا `grep`)، وقارن قيم المحتوى بالمطلوب فعليًا.
- قبل الرندر الكامل: فحص headless سريع (القسم 2، بند 6) — لو `SYNTAX_ERROR`، رجّع اصلح `scene.html` أولًا.
- لو الرندر فشل: صحّح `scene.html` مباشرة وأعد تشغيل `node render-runner.js` — ممنوع ملفات تجربة منفصلة (`test_xxx.html`).
- امتداد الفيديو الناتج زي ما هو من `window.__renderFilename` — ممنوع تغييره يدويًا (`cp video.webm video.mp4` وما شابه).
- لو بتلاقي نفسك بتكرر نفس المحاولة والخطأ أكتر من مرتين على نفس المشكلة، وقف وقارن بنسخة شغالة معروفة بدل ما تكمل تخمين.
- لو ملف الهوية فيه قسم "ملاحظات معروفة" في آخره، اقرأه قبل ما تبدأ.
- ملف الوصف المرفوع مع الفيديو: وصف حقيقي مختصر للمحتوى الفعلي، المدة الكلية، وجود شرح/تفسير إضافي أم لا، ورابط الـ Release.
- الفيديو + ملف الوصف + `scene.html` نفسه — التلاتة يترفعوا كـ assets حقيقية على الـ Release.

---

## 5. خطوات التنفيذ

1. حدد ملف الهوية المطلوب من طلب المستخدم (اسأله لو غير واضح).
2. افتح الملف واقرأه كامل، وابدأ فعليًا في جلب أي محتوى/بيانات حقيقية محتاجها المهمة بالطريقة اللي الملف حددها.
3. اكتب `scene.html` (القسم 2 + القسم 3).
4. فحص headless سريع (القسم 2، بند 6).
5. شغّل `node render-runner.js` (السكريبت تحت).
6. تأكد الرفع الفعلي لكل من: الفيديو، ملف الوصف، `scene.html` (`gh release upload`).
7. اكتب ملف علامة `video_<معرّف فريد>_done.json` يحتوي
   `{"identifier": "...", "release_video_url": "...", "release_md_url": "...", "release_scene_html_url": "..."}`
   لكل فيديو — `agent.js` بيتحقق إن الروابط دي موجودة فعليًا على الـ Release
   (`gh release view`) قبل ما يقبل الملف. بعد آخر فيديو في المهمة، اكتب
   `TASK_COMPLETE.json` يحتوي `{"summary": "...", "videos": [...]}`.

### `render-runner.js`

```js
const { chromium } = require('playwright');
const fs = require('fs');
const path = require('path');
const http = require('http');

// تايم آوت إجمالي إلزامي — رندر فيديو حقيقي بياخد دقايق، مش قابل للحذف أو التقليل
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

  // ?autorender=true بيخلي الصفحة تستدعي window.startVideoRender() لوحدها بعد التحميل
  await page.goto(`http://localhost:${port}/scene.html?autorender=true`);

  // --- تتبع تقدم مباشر: بولّينج على renderStatus/renderProgress بدل انتظار
  // صامت لحد الاكتمال أو التايم آوت. التايم آوت هنا مُدار يدويًا (مش
  // page.waitForFunction) عشان يفضل واضح وصريح دايمًا، من غير خطر إنك تنسى
  // تحطه في نداء جديد لاحقًا. ---
  const startTime = Date.now();
  let lastPercent = -1;
  let finalStatus = null;

  while (true) {
    const { status, progress } = await page.evaluate(() => ({
      status: window.renderStatus,
      progress: window.renderProgress,
    }));

    const percent = Math.round((progress || 0) * 100);
    const elapsed = ((Date.now() - startTime) / 1000).toFixed(1);
    if (percent !== lastPercent) {
      console.log(`[+${elapsed}s] renderStatus=${status} progress=${percent}%`);
      lastPercent = percent;
    }

    if (status === 'completed' || status === 'error') { finalStatus = status; break; }

    if (Date.now() - startTime > TIMEOUT_MS) { finalStatus = 'timeout'; break; }
    await page.waitForTimeout(2000);
  }

  const result = {
    success: finalStatus === 'completed',
    elapsed_seconds: Number(((Date.now() - startTime) / 1000).toFixed(1)),
    last_progress_percent: lastPercent,
    console_logs: consoleLogs.slice(-50),
    failed_requests: failedRequests,
  };

  if (finalStatus === 'completed') {
    const filename = await page.evaluate(() => window.__renderFilename);
    const base64 = await page.evaluate(() => window.__renderBase64);
    fs.writeFileSync(filename, Buffer.from(base64, 'base64'));
    result.filename = filename;
    result.size = fs.statSync(filename).size;
  } else if (finalStatus === 'error') {
    result.error = await page.evaluate(() => window.__renderError);
  } else {
    result.error = `TimeoutError: تجاوز الرندر ${TIMEOUT_MS / 1000} ثانية من غير اكتمال (آخر تقدم مسجّل: ${lastPercent}%)`;
  }

  await browser.close();
  server.close();
  console.log(JSON.stringify(result)); // اقرأها من الـ output بتاع run_terminal مباشرة
  process.exit(result.success ? 0 : 1);
})();
```

**استخدم `console_logs`/`failed_requests` مباشرة للتشخيص** — لو ملف صوت أو خط
طلع 404 هتلاقيه صريح في `failed_requests`. لو فشل الرندر، `last_progress_percent`
بيوريك لحد فين وصل قبل ما يفشل أو يتوقف.
