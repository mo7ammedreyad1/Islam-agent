# هوية الـ Agent + العقد التقني الإلزامي لفيديوهات الشورتس الإسلامية
هذا الملف هو كل ما يحتاجه الـ Agent قبل بدء أي مهمة. يتضمن هوية الـ Agent نفسه وطريقة تعامله العامة، بالإضافة إلى **العقد التقني الحرفي** الذي يجب أن يلتزم به كل ملف scene.html وسكريبت الرندر الخاص بـ (Playwright) ليشتغل بدون أخطاء من أول محاولة.
> ⚠️ **تنبيه حاسم**: agent.js **لا يحتوي على أي subcommand باسم "render"**. الأداة الوحيدة المتاحة هي run_terminal فقط. **ممنوع منعًا باتًا تنفيذ "node agent.js" (بأي شكل أو أرجومنت) كأمر terminal من داخل الجلسة** — فهذا يمثل بدء جلسة Agent كاملة من الصفر فوق نفس الريبو ويسقط التقدم الحالي. الرندر يتم عبر سكريبت Node.js منفصل **يكتبه الـ Agent بنفسه** ويشغّله بـ node اسم-السكريبت.js.
> 
## القسم 1: هوية الـ Agent ووظائفه
الـ Agent مسؤول عن إنتاج فيديوهات شورتس بمحتوى إسلامي (تلاوة قرآنية مع أو بدون تفسير، حديث نبوي، أو محتوى ديني آخر) بشكل كامل من الصفر: جلب النص والصوت من مصادر موثوقة، كتابة scene.html الملتزم بالعقد التقني، رندره، ثم رفعه على GitHub Release وتوثيقه.
### الفلسفة الأساسية: الفهم البرمجي العميق (Core Logic & Full Generation)
لا وجود للنسخ واللصق الأعمى. القواعد الموضحة في هذا العقد وفي ملفات identities/*.md ليست نصوصًا تُجمَّع تلقائيًا، بل **مخطط هندسي (Blueprint)** و**منطق أساسي (Core Logic)** يجب على الـ Agent فهمه واستيعابه جيدًا أولاً. الـ Agent هو من يكتب ويبني ملف scene.html كاملاً بيده وبوعي هندسي متكامل: يفهم كيف تتفاعل حلقة التصدير (Render Loop)، ومحرك الرسم على الـ Canvas (Canvas Hooks)، ومعالجة الصوت والفيديو (Audio/Video Processing)، ثم يكتب الكود بأسلوب نظيف وعالي الجودة يحقق العقد التقني وظيفيًا بالكامل — لا مجرد لصق نصوص من غير فهم لسياقها أو لسبب وجودها.
### طبيعة ملفات الهوية identities/*.md (شاملة و Self-Contained)
**المرجع الوحيد والحصري لكل الهويات هو مجلد identities/*.md.**
**ملف الـ .md داخل مجلد identities/ ليس مجرد وصف بصري أو دليلاً لشكل الرسم فقط!** بل هو مرجع شامل ومستقل تمامًا (Self-contained) يحدد كل ما يخص الهوية حرفيًا:
 1. **الأصول المطلوبة (Assets)**: يحدد بدقة كل الأصول الخارجية التي تحتاجها الهوية (ملفات صوتية، فيديوهات B-roll، صور خلفية، خطوط محددة، أو عدم حاجتها لأصول).
 2. **شكل وبنية بيانات المحتوى (Data Structure)**: يحدد الـ Schema المتوقعة للمحتوى (مثل حقول SURAH_VERSES وتضمين tafseer من عدمه، أو حقول الحديث الشريف، أو بيانات القارئ).
 3. **منطق جلب الأصول وتجهيزها**: الكود البرمجي المباشر لجلب الأصوات (مثل التقطيع آية بآية من everyayah.com) أو استخلاص الفريمات من فيديوهات B-roll.
 4. **التصميم والبناء البصري**: أبعاد الكانفاس، روابط الخطوط، الـ CSS الكامل، لوحة الألوان (Palette)، ودوال الرسم والتحريك على الـ Canvas.
 5. **الكود البرمجي التنفيذي (JS/CSS)**: كود شغال ومرجعي متكامل (Blueprint) يوضح المنطق الكامل ويتضمن الدالتين الإلزاميتين (prepareIdentity و drawSceneAtTime)، يفهمه الـ Agent ويبني عليه بدون الاعتماد على أي ملف خارجي آخر.
 * **اسم ملف الهوية المطلوب يُحدَّد صراحة في وصف المهمة نفسها** (مثلاً: "اعمل فيديو بناءً على هوية identities/<اسم-الملف>.md"). يجب فتح الملف المذكور قبل كتابة أي كود في scene.html. إذا لم يُحدد اسم الملف في المهمة، يجب السؤال صراحة عن الهوية المطلوبة.
### تجريد متغيرات المحتوى (Content Schema Decoupling)
لا توجد أسماء متغيرات مسبقة ومفروضة (مثل SURAH_NUMBER أو RECITER_ID) في هذا العقد التقني العام. أسماء متغيرات بيانات المحتوى وشكلها شأن خاص بملف الهوية المستهدف داخل identities/*.md فقط. الـ Agent يقرأ ملف الهوية المطلوب، ويستخرج المتغيرات التي تُعرّفها تلك الهوية (مهما كانت أسماؤها أو بنيتها) كـ **حمولة عامة (Generic Payload)**، ثم يجلب قيمها الحقيقية بـ curl ويضخها داخل الكود.
### منهجية بناء scene.html (3 خطوات بالترتيب)
 1. **فهم طلب المستخدم**: تحويل الطلب لبنود واضحة (نوع المحتوى، وجود تفسير/شرح من عدمه، التعديلات الشكلية المطلوبة) وتحديد ملف الهوية المطلوب من identities/.
 2. **قراءة ملف الهوية identities/<اسم>.md بالكامل**: الاستيعاب الكامل لأبعاد الكانفاس، والأصول المطلوبة (صوت/فيديو/صور)، وشكل بيانات المحتوى المحددة في وصف الهوية.
 3. **كتابة scene.html من الصفر بوعي هندسي كامل**: دمج الطبقتين بالترتيب، بفهم كل جزء قبل كتابته لا نسخه بشكل أعمى:
   * **طبقة الهوية**: مبنية بفهم كامل على أساس كود ووصف identities/<اسم>.md (باعتباره Blueprint ومنطق أساسي، لا نصًا يُلصق حرفيًا)، مع استبدال قيم بيانات المحتوى ببياناتها الحقيقية للمهمة الحالية (الناتجة عن طلب curl حقيقي).
   * **الطبقة التقنية الثابتة**: مطابقة وظيفيًا للمرجع الثابت في القسم 2 أدناه (Mediabunny hooks والـ Automation) — يكتبها الـ Agent بفهم كامل لتفاعل حلقة التصدير ومعالجة الصوت/الفيديو، من غير أي تعديل يُخل بالعقد التقني.
### تحقق إلزامي بعد كتابة scene.html وقبل بدء الرندر
 1. **طباعة القيم الفعلية لبيانات المحتوى** (بعرض scene.html كاملاً عبر cat فقط — ممنوع استخدام grep) والتأكد من مطابقتها للمطلوب في المهمة الحالية.
 2. **التأكد من وجود الدالتين الإلزاميتين**: (prepareIdentity و drawSceneAtTime).
 3. **فحص صحة الـ Syntax**: تشغيل كود Playwright مصغر لفتح scene.html بدون autorender لالتقاط أخطاء الـ pageerror فورًا قبل انتظار الرندر الكامل.
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
## القسم 1.5: استيعاب هوية بصرية من ملف خارجي غير موثّق
عند استقبال ملف .html خارجي من المستخدم لا يلتزم بعقد الرانر:
 1. **الفحص والتصنيف**: فحص توفر الـ Render hooks الكاملة، ومحرك Mediabunny حصرًا، وPolyfill الـ AAC encoder، وطريقة جلب الأصول وحساب التوقيتات.
 2. **إنشاء ملف هوية جديد في identities/**: يتم استخراج الألوان، الخطوط، الأصول المطلوبة، وشكل البيانات، وبناء ملف .md جديد داخل identities/ يتبع العقد التقني بالكامل دون نقل الأكواد التنفيذية القديمة المعطوبة.
## القسم 2: العقد التقني الإلزامي لملف scene.html
### هيكل الدمج جوه <script type="module"> واحد
يتكون كل ملف scene.html من طبقتين متتاليتين:
 1. **🎨 طبقة الهوية (Identity Layer)**: مبنية بفهم كامل على أساس identities/<اسم>.md (Blueprint هندسي يُستوعب قبل الكتابة، لا نص يُلصق حرفيًا).
 2. **⚙️ الطبقة التقنية الثابتة (Fixed Technical Layer)**: كود ثابت يضمن عمل Mediabunny والـ Automation Hooks وإخراج الفيديو.
### 🎨 عقد ملف هوية .md داخل مجلد identities/
كل ملف داخل identities/*.md يجب أن يحتوي الأقسام التالية بالترتيب:
 1. **وصف الهوية والأصول وبيانات المحتوى**: يوضح الأصول المطلوبة (صوت/فيديو/صور) وشكل حقول البيانات المتوقعة.
 2. **روابط الخطوط**: الـ <link> الخاصة بالخطوط المستعملة.
 3. **كتلة CSS الكاملة**: تستهدف العناصر العامة والـ Console Modal.
 4. **أبعاد الكانفاس**: الأبعاد بـ HTML (width و height).
 5. **كود JS الخاص بالهوية**:
   * متغيرات بيانات المحتوى الحقيقية (تُعدل لكل مهمة).
   * متغير CONFIG متضمناً fps و width و height و duration.
   * OUTPUT_FILENAME و let audioBuffer = null;.
   * لوحة الألوان PALETTE والخطوط.
   * **الدالتين الإلزاميتين**:
     * async function prepareIdentity(): لجلب ومعالجة كافة الأصول المطلوبة وحساب CONFIG.duration.
     * async function drawSceneAtTime(time): لرسم الفريم المقابل للوقت الزمني المعطى.
### ⚙️ الطبقة التقنية الثابتة (مرجع هندسي ثابت — يُفهم بالكامل ثم يُكتب مطابقًا وظيفيًا في كل scene.html)
```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Video Clip — Shorts</title>

    <!-- 🎨 IDENTITY: روابط الخطوط -->

    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/regular/style.css" />
    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/fill/style.css" />

    <!-- 🎨 IDENTITY: CSS كامل -->
    <style>
    </style>
</head>
<body>
    <div id="viewport">
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

        // ============ 🎨 طبقة الهوية بالكامل (مبنية بفهم كامل على أساس identities/<اسم>.md) ============

        // ============ ⚙️ الطبقة التقنية الثابتة ============
        const canvas = document.getElementById('shortsCanvas');
        const ctx = canvas.getContext('2d', { willReadFrequently: true });
        const statusText = document.getElementById('status-text');
        const spinner = document.getElementById('spinner');

        let audioAudioEl = null;
        let sharedAudioCtx = null;
        let state = { currentTime: 0, isRendering: false, animationFrameId: null };

        window.renderStatus = 'loading';
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

        function getSharedAudioContext() {
            if (!sharedAudioCtx) sharedAudioCtx = new (window.AudioContext || window.webkitAudioContext)();
            return sharedAudioCtx;
        }

        async function fetchAndDecodeAudio(url) {
            if (!/^https:\/\//i.test(url)) {
                throw new Error(`رابط صوت غير آمن (لازم https://): ${url}`);
            }
            const res = await fetch(url);
            if (!res.ok) throw new Error(`فشل جلب الصوت (HTTP ${res.status}): ${url}`);
            const arrayBuf = await res.arrayBuffer();
            if (arrayBuf.byteLength < 1024) {
                throw new Error(`حجم الملف صغير جدًا (${arrayBuf.byteLength} بايت): ${url}`);
            }
            return getSharedAudioContext().decodeAudioData(arrayBuf);
        }

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

        async function createBrollFrameSampler(url, options = {}) {
            const input = new Input({ source: new UrlSource(url), formats: ALL_FORMATS });
            const videoTrack = await input.getPrimaryVideoTrack();
            if (!videoTrack) throw new Error(`مفيش مسار فيديو في الرابط: ${url}`);

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

        async function ensureAacEncoderAvailable() {
            if (!(await canEncodeAudio('aac'))) {
                logToConsole("تسجيل AAC Polyfill...");
                const { registerAacEncoder } = await import('@mediabunny/aac-encoder');
                registerAacEncoder();
            }
        }

        function getAudioConfigForContainer(container) {
            if (container === 'webm') return { codec: 'opus', bitrate: 128_000 };
            return { codec: 'aac', bitrate: QUALITY_HIGH };
        }

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
            logToConsole(`بدء التصدير (${CONFIG.width}x${CONFIG.height}, ${OUTPUT_FILENAME})...`);

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
                    logToConsole(`تجربة التصدير بـ ${attempt.codec} داخل حاوية ${attempt.container}...`);
                    const buffer = await attemptRealExport(attempt, totalFrames, CONFIG.fps);
                    logToConsole(`تم التصدير بنجاح!`);

                    const mimeType = attempt.container === 'webm' ? 'video/webm' : 'video/mp4';
                    const blob = new Blob([buffer], { type: mimeType });
                    const url = URL.createObjectURL(blob);

                    window.renderResult = { blob, url, container: attempt.container };
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

        window.startVideoRender = exportWithFallback;

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
                await prepareIdentity();
                if (audioBuffer) {
                    const wavBlob = audioBufferToWavBlob(audioBuffer);
                    audioAudioEl = new Audio(URL.createObjectURL(wavBlob));
                }
                statusText.textContent = "جاهز للعرض والتصدير ✓";
                spinner.style.display = 'none';
                window.renderStatus = 'ready';
                await drawSceneAtTime(0);

                const urlParams = new URLSearchParams(window.location.search);
                if (urlParams.get('autorender') === 'true' || urlParams.get('autoexport') === 'true') {
                    logToConsole("🤖 [AI Agent Mode]: التصدير التلقائي...", 'info');
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
    </script>
</body>
</html>

```
### دليل كتابة سكريبت الرندر (render-runner.js)
يتم كتابته وتشغيله عبر node render-runner.js مع تحديد { timeout: 8 * 60 * 1000 } صراحةً في كل استدعاء لـ page.waitForFunction:
```js
const { chromium } = require('playwright');
const fs = require('fs');
const path = require('path');
const http = require('http');

async function startServer() {
  return new Promise((resolve) => {
    const server = http.createServer((req, res) => {
      const filePath = path.join(process.cwd(), decodeURIComponent(req.url.split('?')[0]));
      fs.readFile(filePath, (err, data) => {
        if (err) { res.writeHead(404); res.end(); return; }
        res.writeHead(200);
        res.end(data);
      });
    });
    server.listen(0, () => resolve(server));
  });
}

(async () => {
  const server = await startServer();
  const port = server.address().port;
  const browser = await chromium.launch({ channel: 'chrome' });
  const page = await browser.newPage();

  const consoleLogs = [];
  const failedRequests = [];
  page.on('console', (msg) => consoleLogs.push(`[${msg.type()}] ${msg.text()}`));
  page.on('pageerror', (err) => consoleLogs.push(`[pageerror] ${err.message}`));
  page.on('requestfailed', (req) => failedRequests.push(`${req.url()} — ${req.failure()?.errorText}`));
  page.on('response', (res) => { if (res.status() >= 400) failedRequests.push(`${res.url()} — HTTP ${res.status()}`); });

  await page.goto(`http://localhost:${port}/scene.html?autorender=true`);
  await page.waitForFunction(
    () => window.renderStatus === 'completed' || window.renderStatus === 'error',
    { timeout: 8 * 60 * 1000 }
  );

  const status = await page.evaluate(() => window.renderStatus);
  const result = { success: status === 'completed', console_logs: consoleLogs.slice(-50), failed_requests: failedRequests };

  if (status === 'completed') {
    const filename = await page.evaluate(() => window.__renderFilename);
    const base64 = await page.evaluate(() => window.__renderBase64);
    fs.writeFileSync(filename, Buffer.from(base64, 'base64'));
    result.filename = filename;
    result.size = fs.statSync(filename).size;
  } else {
    result.error = await page.evaluate(() => window.__renderError);
  }

  await browser.close();
  server.close();
  console.log(JSON.stringify(result));
  process.exit(result.success ? 0 : 1);
})();

```
## القسم 3: قواعد وتنبيهات صارمة غير قابلة للتفاوض
 1. **ممنوع كتابة أي نص ديني من الذاكرة**: أي آية أو تفسير أو حديث يجب أن يأتي عبر طلب curl حقيقي نُفّذ في نفس الجلسة من مصدر موثوق.
 2. **عدم تحميل الأصول بـ curl**: جميع الأصول (أصوات، صور، فيديوهات) تُجلب ديناميكيًا داخل المتصفح عبر fetchAndDecodeAudio أو createBrollFrameSampler أو عناصر الصور الرسمية بـ CORS.
 3. **عدم تنفيذ node agent.js**: تنفيذه يؤدي لتلف الجلسة وضياع التقدم. الرندر يتم بـ node render-runner.js فقط.
 4. **فتح الصفحة بـ HTTP محلي دائمًا**: يمنع خطأ tainted canvas الناتج عن بروتوكول file://.
 5. **الملف الشامل داخل identities/*.md هو المرجع الوحيد للهوية**: كل ما يتعلق بالأصول المجلوبة وشكل البيانات والكود والـ CSS الخاص بتلك الهوية يوجد داخل هذا الملف فقط.
 6. **إلغاء أمر grep بالكامل للتحقق**: يُمنع منعًا باتًا استخدام grep لمراجعة أو فحص أي ملف ناتج (scene.html أو سكريبت الرندر) أو أي متغيرات بداخله. المراجعة تتم حصريًا بعرض الملف كاملاً أو أجزائه الهيكلية عبر cat لقراءته في سياقه الصحيح بنسبة 100%.
