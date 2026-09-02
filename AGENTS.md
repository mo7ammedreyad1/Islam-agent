
```markdown
# AGENTS.md — دليل الوكيل لإنتاج ورندر الفيديوهات (Execution & Render Engine)

> ⚠️ **تحذير حاسم**: أمر `node agent.js` ليس أداة رندر وتشغيله من داخل الجلسة سيعيد تشغيل الوكيل من الصفر ويدمر تقدمك. عملية الرندر تتم حصرياً عبر تشغيل سكريبت `render-runner.js` الموضح في القسم 4 بأمر: `node render-runner.js`.

---

## 1. دور الوكيل ومسار الإنتاج الحاسم (Agent Mandate & Pipeline)

أنت **عقل تنفيذي ومحرك إنتاج برمجي** مسؤول عن تحويل أي ملف هوية في مجلد `identities/` إلى فيديو متكامل عالي الدقة (صوت، صورة، حركة، ومؤثرات).

### مسار العمل الإلزامي (Production Pipeline):
1. **قراءة وفهم ملف الهوية**: افتح ملف الهوية المحدد من `identities/<name>.md` وافهم مواصفاته البصرية بالكامل (الأبعاد، الألوان، أسلوب الحركة، الخطوط، وتوزيع المشاهد).
2. **كتابة `scene.html`**: ابنِ المشهد بدقة متناهية؛ حيث تضع كود تصميم الهوية في الجزء المخصص، متبوعاً بالطبقة التقنية الثابتة داخل نفس ملف الـ HTML.
3. **الفحص السريع (Pre-flight Check)**: شغّل فحص Headless للتحقق من خلو الملف من أي أخطاء برمجية أو فشل في تحميل الخطوط والأصول قبل بدء الرندر.
4. **الرندر المحلي (`render-runner.js`)**: شغّل محرك الرندر لترميز الفريمات بدقة عبر `ffmpeg` واستخراج ملف الـ `.mp4`.
5. **التسليم والرفع**: ارفع الفيديو المصدر، ملف الوصف، وملف `scene.html` إلى الـ Release وسجل ملفات الإنجاز المطلوبة.

---

## 2. العقد المعماري والطبقة التقنية الثابتة (Architectural Contract & Shell)

### 2.1 العقد التقني (Technical Contract)
يجب أن تعرّف طبقة الهوية المتغيرات والدوال التالية بدقة ليعمل المحرك بسلاسة:
- `CONFIG`: `{ fps: 30, width: number, height: number, duration: number }`.
- `OUTPUT_FILENAME`: اسم الملف بدون امتداد.
- `audioBuffer`: كائن `AudioBuffer` أو `null` صراحة عند عدم وجود صوت.
- `async function prepareIdentity()`: دالة غير متزامنة لتهيئة الخطوط والمتجهات والأصول.
- `async function drawSceneAtTime(time)`: دالة الرسم اللحظية على الكانفاس لكل فريم (دائماً `async`).
- **أدوات البيئة المتاحة تلقائياً**: `ctx`, `layoutArabicParagraph(...)`, `clamp01(val)`, `fetchAndDecodeAudio(url)`, `concatenateAudioBuffers(buffers)`, `createBrollFrameSampler(url, options?)`.

### 2.2 قالب HTML والطبقة التقنية الثابتة (Fixed Technical Shell)
انسخ الهيكل التالي وضع كود الهوية في المكان المخصص:

```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><!-- IDENTITY: SCENE TITLE --></title>

    <!-- روابط الخطوط الرسمية -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans+Arabic:wght@400;500;600;700;800;900&family=Plus+Jakarta+Sans:wght@500;600;700;800;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/regular/style.css" />
    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/fill/style.css" />

    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; user-select: none; }
        body {
            background-color: #07090e; color: #ffffff;
            font-family: 'IBM Plex Sans Arabic', 'Plus Jakarta Sans', sans-serif;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            min-height: 100vh; min-height: 100dvh; overflow: hidden; padding: 10px;
        }
        /* إطار العرض المتجاوب تلقائياً مع كافة الأبعاد ونسب الشاشات */
        #viewport {
            position: relative;
            max-height: 86vh;
            max-width: 94vw;
            width: auto;
            height: auto;
            background: #f5f5f5;
            box-shadow: 0 30px 80px rgba(0, 0, 0, 0.75), 0 0 1px 1px rgba(255, 255, 255, 0.1);
            border-radius: 18px;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        canvas { width: 100%; height: 100%; object-fit: contain; display: block; }
        #hud {
            position: absolute; top: 16px; right: 16px;
            background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(14px);
            color: #0a0f1d; padding: 6px 14px; border-radius: 30px;
            font-size: 12px; font-weight: 700; display: flex; align-items: center; gap: 8px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08); border: 1px solid rgba(0, 0, 0, 0.06); z-index: 10;
        }
        .spinner {
            width: 12px; height: 12px; border: 2px solid #e5e5ea;
            border-top: 2px solid #0a0f1d; border-radius: 50%; animation: spin 0.8s linear infinite;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        #controls-overlay {
            position: absolute; bottom: 16px; left: 50%; transform: translateX(-50%);
            display: flex; gap: 8px; z-index: 10; background: rgba(10, 15, 29, 0.92);
            padding: 6px 12px; border-radius: 40px; backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.14);
        }
        .btn {
            font-family: inherit; font-size: 12px; font-weight: 700; padding: 6px 14px;
            border-radius: 20px; border: none; cursor: pointer; display: flex;
            align-items: center; gap: 6px; transition: all 0.2s cubic-bezier(0.16, 1, 0.3, 1);
        }
        .btn-preview { background: rgba(255, 255, 255, 0.12); color: #f8fafc; }
        .btn-preview:hover { background: rgba(255, 255, 255, 0.22); }
        .btn-render { background: #0a0f1d; color: #ffffff; border: 1px solid rgba(255, 255, 255, 0.2); }
        .btn-render:hover { background: #1e293b; }
        #console-modal {
            position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%);
            width: 680px; max-width: 92vw; height: 400px; background: #090d16;
            border: 1px solid #1e293b; border-radius: 14px; box-shadow: 0 30px 80px rgba(0,0,0,0.8);
            z-index: 100; display: none; flex-direction: column; overflow: hidden;
        }
        #console-header {
            background: #151d2d; padding: 12px 18px; display: flex;
            justify-content: space-between; align-items: center; font-size: 13px; font-weight: bold;
        }
        #console-output {
            flex: 1; padding: 14px; overflow-y: auto; font-family: monospace;
            font-size: 12px; color: #cbd5e1; background: #060910; line-height: 1.6;
        }
        .log-line { margin-bottom: 4px; }
        .log-info { color: #38bdf8; }
        .log-warn { color: #facc15; }
        .log-error { color: #f87171; }
    </style>
</head>
<body>
    <div id="viewport">
        <canvas id="videoCanvas"></canvas>
        <div id="hud"><div class="spinner" id="spinner"></div><span id="status-text">جاري إعداد المشهد...</span></div>
        <div id="controls-overlay">
            <button class="btn btn-preview" id="btn-replay"><i class="ph ph-arrow-counter-clockwise"></i> تشغيل المعاينة</button>
            <button class="btn btn-preview" id="btn-toggle-console"><i class="ph ph-terminal-window"></i> سجل الأخطاء</button>
            <button class="btn btn-render" id="btn-render-start"><i class="ph-fill ph-video-camera"></i> تصدير الفيديو</button>
        </div>
    </div>

    <div id="console-modal">
        <div id="console-header">
            <span><i class="ph ph-terminal-window"></i> سجل النظام والعمليات البرمجية</span>
            <button class="btn btn-preview" id="btn-close-console" style="color:#fff; background:#ef4444; border:none; padding:4px 14px;">إغلاق</button>
        </div>
        <div id="console-output"></div>
    </div>

    <script type="module">
        // ============ IDENTITY LAYER (CUSTOM LOGIC) ============

        // ============ FIXED TECHNICAL LAYER (RUNTIME) ============
        const canvas = document.getElementById('videoCanvas');
        const ctx = canvas.getContext('2d', { willReadFrequently: true });
        const statusText = document.getElementById('status-text');
        const spinner = document.getElementById('spinner');

        let audioAudioEl = null;
        let sharedAudioCtx = null;
        let state = { currentTime: 0, isRendering: false, animationFrameId: null };

        window.renderStatus = 'loading';

        function logToConsole(msg, type = 'info') {
            const output = document.getElementById('console-output');
            const line = document.createElement('div');
            line.className = `log-line log-${type}`;
            line.textContent = `[${new Date().toLocaleTimeString()}] ${msg}`;
            output.appendChild(line);
            output.scrollTop = output.scrollHeight;
        }

        function clamp01(val) { return Math.max(0, Math.min(1, val)); }

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
            if (!/^https:\/\//i.test(url)) throw new Error(`رابط صوت غير آمن: ${url}`);
            const res = await fetch(url);
            if (!res.ok) throw new Error(`فشل جلب الصوت: ${url}`);
            const arrayBuf = await res.arrayBuffer();
            if (arrayBuf.byteLength < 1024) throw new Error(`حجم الملف صغير جداً: ${url}`);
            return getSharedAudioContext().decodeAudioData(arrayBuf);
        }

        function concatenateAudioBuffers(buffers) {
            if (!buffers || buffers.length === 0) throw new Error("لا يوجد AudioBuffer للتجميع");
            const sampleRate = buffers[0].sampleRate;
            const channelsCount = buffers[0].numberOfChannels;
            const totalSamples = buffers.reduce((sum, b) => sum + b.length, 0);
            const combined = getSharedAudioContext().createBuffer(channelsCount, totalSamples, sampleRate);

            let sampleOffset = 0, timeOffset = 0.0;
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
            const videoEl = document.createElement('video');
            videoEl.crossOrigin = 'anonymous'; videoEl.muted = true;
            videoEl.playsInline = true; videoEl.preload = 'auto'; videoEl.src = url;

            await new Promise((resolve, reject) => {
                videoEl.onloadeddata = resolve;
                videoEl.onerror = () => reject(new Error(`فشل تحميل فيديو B-roll: ${url}`));
            });

            const targetW = options.width || videoEl.videoWidth;
            const targetH = options.height || videoEl.videoHeight;
            const fit = options.fit || 'cover';
            const sampleCanvas = document.createElement('canvas');
            sampleCanvas.width = targetW; sampleCanvas.height = targetH;
            const sampleCtx = sampleCanvas.getContext('2d');

            sampleCtx.drawImage(videoEl, 0, 0, 1, 1);
            let corsOk = true;
            try { sampleCtx.getImageData(0, 0, 1, 1); } catch (e) { corsOk = false; }

            if (!corsOk && options.posterFallbackUrl) {
                const posterImg = new Image(); posterImg.crossOrigin = 'anonymous';
                await new Promise((resolve, reject) => {
                    posterImg.onload = resolve;
                    posterImg.onerror = () => reject(new Error(`فشل تحميل صورة fallback: ${options.posterFallbackUrl}`));
                    posterImg.src = options.posterFallbackUrl;
                });
                return {
                    videoEl: null, isFallbackImage: true,
                    async getFrameAt(time) {
                        const zoom = 1.0 + Math.min(time / 10, 1) * 0.06;
                        const iw = posterImg.naturalWidth, ih = posterImg.naturalHeight;
                        const scale = Math.max(targetW / iw, targetH / ih) * zoom;
                        const dw = iw * scale, dh = ih * scale;
                        const dx = (targetW - dw) / 2, dy = (targetH - dh) / 2;
                        sampleCtx.clearRect(0, 0, targetW, targetH);
                        sampleCtx.drawImage(posterImg, dx, dy, dw, dh);
                        return sampleCanvas;
                    }
                };
            }

            if (!corsOk) throw new Error(`فيديو B-roll لا يدعم CORS: ${url}`);
            const useFrameCallback = typeof videoEl.requestVideoFrameCallback === 'function';

            return {
                videoEl,
                async getFrameAt(time) {
                    const seekTime = Math.min(Math.max(time, 0), Math.max(videoEl.duration - 0.01, 0));
                    await new Promise((resolve, reject) => {
                        const timeoutId = setTimeout(() => reject(new Error(`تأخر الفريم عند ${seekTime}s: ${url}`)), 5000);
                        if (useFrameCallback) {
                            videoEl.requestVideoFrameCallback(() => { clearTimeout(timeoutId); resolve(); });
                        } else {
                            videoEl.onseeked = () => { clearTimeout(timeoutId); resolve(); };
                        }
                        videoEl.currentTime = seekTime;
                    });

                    const vw = videoEl.videoWidth, vh = videoEl.videoHeight;
                    const scale = fit === 'contain' ? Math.min(targetW / vw, targetH / vh) : Math.max(targetW / vw, targetH / vh);
                    const dw = vw * scale, dh = vh * scale;
                    const dx = (targetW - dw) / 2, dy = (targetH - dh) / 2;

                    sampleCtx.clearRect(0, 0, targetW, targetH);
                    sampleCtx.drawImage(videoEl, dx, dy, dw, dh);
                    return sampleCanvas;
                }
            };
        }

        function startPreviewLoop() {
            if (state.animationFrameId) cancelAnimationFrame(state.animationFrameId);
            if (audioAudioEl) {
                audioAudioEl.currentTime = 0;
                audioAudioEl.play().catch(e => logToConsole("تنبيه الصوت: " + e.message, 'warn'));
            }

            const startTime = performance.now();
            async function loop(now) {
                if (state.isRendering) return;
                const elapsed = (now - startTime) / 1000;
                const currTime = audioAudioEl ? audioAudioEl.currentTime : (elapsed % CONFIG.duration);
                state.currentTime = currTime;
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

        const OFOQ_FRAME_FORMAT = 'image/png';
        window.__ofoqTotalFrames = 0;
        window.__ofoqFps = 0;
        window.__ofoqOutputFilename = '';
        window.__ofoqAudioWavBase64 = null;

        async function __ofoqGetFrameBatch(startFrame, count) {
            const frames = [];
            for (let i = 0; i < count; i++) {
                const frameIndex = startFrame + i;
                if (frameIndex >= window.__ofoqTotalFrames) break;
                const timestamp = frameIndex / window.__ofoqFps;
                await drawSceneAtTime(timestamp);
                const dataUrl = canvas.toDataURL(OFOQ_FRAME_FORMAT);
                frames.push(dataUrl.substring(dataUrl.indexOf(',') + 1));
            }
            return frames;
        }
        window.__ofoqGetFrameBatch = __ofoqGetFrameBatch;

        function arrayBufferToBase64(buffer) {
            let binary = '';
            const bytes = new Uint8Array(buffer);
            const chunkSize = 0x8000;
            for (let i = 0; i < bytes.length; i += chunkSize) {
                binary += String.fromCharCode.apply(null, bytes.subarray(i, i + chunkSize));
            }
            return btoa(binary);
        }

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
            logToConsole("التصدير الفعلي يتم عبر render-runner.js (Node + ffmpeg).", 'warn');
        });

        async function init() {
            try {
                await prepareIdentity();
                canvas.width = CONFIG.width;
                canvas.height = CONFIG.height;

                const viewport = document.getElementById('viewport');
                viewport.style.aspectRatio = `${CONFIG.width} / ${CONFIG.height}`;
                viewport.style.width = `min(94vw, calc(86vh * (${CONFIG.width} / ${CONFIG.height})))`;

                window.__ofoqFps = CONFIG.fps;
                window.__ofoqTotalFrames = Math.ceil(CONFIG.duration * CONFIG.fps);
                window.__ofoqOutputFilename = `${OUTPUT_FILENAME}.mp4`;

                if (audioBuffer) {
                    const wavBlob = audioBufferToWavBlob(audioBuffer);
                    audioAudioEl = new Audio(URL.createObjectURL(wavBlob));
                    window.__ofoqAudioWavBase64 = arrayBufferToBase64(await wavBlob.arrayBuffer());
                }

                statusText.textContent = "جاهز للعرض والتصدير ✓";
                spinner.style.display = 'none';
                window.renderStatus = 'ready';
                await drawSceneAtTime(0);
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

---

## 3. بروتوكول التحقق وفحص ما قبل الرندر (Pre-Flight Verification)

قبل بدء الرندر الكامل، يجب إنشاء وتشغيل سكريبت فحص Headless سريع للتأكد من خلو `scene.html` من أي خطأ برمجية أو فشل تحميل:

```js
// _headless_check.js
const { chromium } = require('playwright');
const http = require('http');
const fs = require('fs');
const path = require('path');

const CHECK_TIMEOUT_MS = 15000;

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

  let pageErr = null;
  const failedRequests = [];
  page.on('pageerror', (err) => { pageErr = err.message; });
  page.on('requestfailed', (req) => failedRequests.push(`${req.url()} — ${req.failure()?.errorText}`));
  page.on('response', (res) => { if (res.status() >= 400) failedRequests.push(`${res.url()} — HTTP ${res.status()}`); });

  await page.goto(`http://localhost:${port}/scene.html`);

  const start = Date.now();
  let status = null;
  while (Date.now() - start < CHECK_TIMEOUT_MS) {
    status = await page.evaluate(() => window.renderStatus).catch(() => null);
    if (status === 'ready' || status === 'error' || pageErr) break;
    await page.waitForTimeout(200);
  }

  await browser.close();
  server.close();

  if (pageErr) { console.log('SYNTAX_ERROR:', pageErr); process.exit(1); }
  if (status === 'error') {
    const errMsg = await page.evaluate(() => window.__renderError).catch(() => 'غير معروف');
    console.log('SYNTAX_ERROR:', errMsg, '| failed_requests:', JSON.stringify(failedRequests));
    process.exit(1);
  }
  if (status !== 'ready') {
    console.log(`SYNTAX_ERROR: لم يكتمل التحضير خلال ${CHECK_TIMEOUT_MS / 1000} ثانية (status=${status})`);
    process.exit(1);
  }
  if (failedRequests.length) {
    console.log('SYNTAX_ERROR: طلبات شبكة فشلت:', JSON.stringify(failedRequests));
    process.exit(1);
  }
  console.log('SCENE_OK');
})();
```
*يتم تشغيله بأمر: `node _headless_check.js`. إذا طبع `SCENE_OK` انتقل للرندر فوراً.*

---

## 4. محرك الرندر وقواعد التشغيل (`render-runner.js` & Execution)

### 4.1 سكريبت الرندر الشامل (`render-runner.js`)
```js
const { chromium } = require('playwright');
const { spawn } = require('child_process');
const fs = require('fs');
const path = require('path');
const http = require('http');

const TIMEOUT_MS = 20 * 60 * 1000;
const FRAME_BATCH_SIZE = 24;
const FFMPEG_LOG_CAP = 30;

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

function writeWithBackpressure(stream, buffer) {
  return new Promise((resolve, reject) => {
    const ok = stream.write(buffer, (err) => { if (err) reject(err); });
    if (ok) resolve();
    else stream.once('drain', resolve);
  });
}

(async () => {
  const server = await startServer();
  const port = server.address().port;
  const browser = await chromium.launch({ channel: 'chrome' });
  const page = await browser.newPage();

  const consoleLogs = [];
  const failedRequests = [];
  const ffmpegLog = [];
  page.on('console', (msg) => consoleLogs.push(`[${msg.type()}] ${msg.text()}`));
  page.on('pageerror', (err) => consoleLogs.push(`[pageerror] ${err.message}`));
  page.on('requestfailed', (req) => failedRequests.push(`${req.url()} — ${req.failure()?.errorText}`));
  page.on('response', (res) => { if (res.status() >= 400) failedRequests.push(`${res.url()} — HTTP ${res.status()}`); });

  const startTime = Date.now();
  const remainingMs = () => TIMEOUT_MS - (Date.now() - startTime);

  async function fail(errMsg) {
    console.log(JSON.stringify({
      success: false,
      error: errMsg,
      console_logs: consoleLogs.slice(-50),
      failed_requests: failedRequests,
      ffmpeg_log: ffmpegLog.slice(-FFMPEG_LOG_CAP),
    }));
    try { await browser.close(); } catch {}
    try { server.close(); } catch {}
    process.exit(1);
  }

  try {
    await page.goto(`http://localhost:${port}/scene.html`);

    let prepStatus = null;
    while (true) {
      prepStatus = await page.evaluate(() => window.renderStatus);
      if (prepStatus === 'ready' || prepStatus === 'error') break;
      if (remainingMs() <= 0) { prepStatus = 'timeout'; break; }
      await page.waitForTimeout(200);
    }

    if (prepStatus !== 'ready') {
      const errMsg = prepStatus === 'timeout'
        ? `TimeoutError: لم يكتمل التحضير خلال المهلة المحددة`
        : await page.evaluate(() => window.__renderError);
      throw new Error(errMsg);
    }

    const audioBase64 = await page.evaluate(() => window.__ofoqAudioWavBase64);
    const hasAudio = !!audioBase64;
    if (hasAudio) fs.writeFileSync('audio.wav', Buffer.from(audioBase64, 'base64'));

    const totalFrames = await page.evaluate(() => window.__ofoqTotalFrames);
    const fps = await page.evaluate(() => window.__ofoqFps);
    const outputFilename = await page.evaluate(() => window.__ofoqOutputFilename);

    const ffmpegArgs = [
      '-y',
      '-loglevel', 'warning', '-hide_banner',
      '-thread_queue_size', '512',
      '-f', 'image2pipe',
      '-framerate', String(fps),
      '-i', '-',
      ...(hasAudio ? ['-i', 'audio.wav'] : []),
      '-c:v', 'libx264',
      '-pix_fmt', 'yuv420p',
      '-preset', 'veryfast',
      '-crf', '18',
      ...(hasAudio ? ['-c:a', 'aac', '-b:a', '192k', '-shortest'] : []),
      outputFilename,
    ];
    const ffmpeg = spawn('ffmpeg', ffmpegArgs);

    let ffmpegDied = null;
    ffmpeg.on('error', (err) => { ffmpegDied = `ffmpeg process error: ${err.message}`; });
    ffmpeg.stdin.on('error', (err) => { ffmpegDied = `ffmpeg stdin error: ${err.message}`; });
    ffmpeg.stderr.on('data', (d) => {
      ffmpegLog.push(d.toString());
      if (ffmpegLog.length > FFMPEG_LOG_CAP) ffmpegLog.shift();
    });

    let lastPercent = -1;
    for (let start = 0; start < totalFrames; start += FRAME_BATCH_SIZE) {
      if (ffmpegDied) throw new Error(ffmpegDied);
      if (remainingMs() <= 0) {
        ffmpeg.kill('SIGKILL');
        throw new Error(`TimeoutError: تجاوز الرندر المهلة المحددة`);
      }

      const count = Math.min(FRAME_BATCH_SIZE, totalFrames - start);
      const batch = await page.evaluate(([s, c]) => window.__ofoqGetFrameBatch(s, c), [start, count]);
      for (const base64Frame of batch) {
        if (ffmpegDied) throw new Error(ffmpegDied);
        await writeWithBackpressure(ffmpeg.stdin, Buffer.from(base64Frame, 'base64'));
      }

      const percent = Math.round(((start + count) / totalFrames) * 100);
      if (percent !== lastPercent) {
        const elapsed = ((Date.now() - startTime) / 1000).toFixed(1);
        console.log(`[+${elapsed}s] frames=${start + count}/${totalFrames} progress=${percent}%`);
        lastPercent = percent;
      }
    }

    if (ffmpegDied) throw new Error(ffmpegDied);
    ffmpeg.stdin.end();
    const ffmpegExitCode = await new Promise((resolve) => ffmpeg.on('close', resolve));
    if (ffmpegDied) throw new Error(ffmpegDied);

    await browser.close();
    server.close();

    const result = {
      success: ffmpegExitCode === 0,
      elapsed_seconds: Number(((Date.now() - startTime) / 1000).toFixed(1)),
      last_progress_percent: lastPercent,
      console_logs: consoleLogs.slice(-50),
      failed_requests: failedRequests,
      ffmpeg_log: ffmpegLog.slice(-FFMPEG_LOG_CAP),
    };

    if (result.success) {
      result.filename = outputFilename;
      result.size = fs.statSync(outputFilename).size;
    } else {
      result.error = `ffmpeg exited with code ${ffmpegExitCode}`;
    }

    console.log(JSON.stringify(result));
    process.exit(result.success ? 0 : 1);
  } catch (err) {
    await fail(err.message);
  }
})();
```

### 4.2 القواعد الصارمة للتسليم (Strict Production Rules)
1. **الامتثال التام لإرادة المستخدم**: أي تفصيلة يطلبها المستخدم تنفذ بحرفية تامة دون افتراضات خاطئة.
2. **منع البيانات الوهمية**: لا تلفق أي فحص؛ إذا فشل فحص الـ Headless، أصلح `scene.html` فوراً ولا تبدأ الرندر.
3. **التسليم والرفع الموثق**: ارفع الفيديو (`.mp4`)، ملف الوصف، وملف `scene.html` إلى الـ Release وأنشئ ملف علامة الإنجاز `video_<id>_done.json` وملف `TASK_COMPLETE.json` بعد اكتمال كافة المهام.
```
