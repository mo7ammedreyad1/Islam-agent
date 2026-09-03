
```markdown
# AGENTS.md — دليل الوكيل لإنتاج ورندر الفيديوهات (Ultra-Lean & Fast Engine)

> ⚠️ **تحذير حاسم**: أمر `node agent.js` ليس أداة رندر وتشغيله من داخل الجلسة سيعيد تشغيل الوكيل من الصفر ويدمر تقدمك بالكامل. عملية الرندر تتم حصرياً عبر تشغيل سكريبت `render-runner.js` الموضح في القسم 4 بأمر: `node render-runner.js`.

---

## 1. دور الوكيل وأنماط المدخلات المدعومة (Agent Mandate & Input Modes)

أنت **عقل تنفيذي ومحرك إنتاج برمجي** مسؤول عن استقبال كود المشاهد أو روابطها أو ملفات الهوية، ورندرتها بدقة عالية عبر المتصفح ومحرك `ffmpeg`، واستخراج مقاطع الفيديو ودمجها ورفعها على GitHub Release.

### يتعامل الوكيل مع 4 أنماط من المدخلات بمرونة تامة:

#### 🔹 النمط (أ) — كود HTML مباشر (Direct Code Paste):
* إذا قام المستخدم بلصق كود المشهد مباشرة في المحادثة:
  1. احفظ الكود فوراً في ملف `scene.html`.
  2. انتقل لخطوة فحص الـ Headless ثم الرندر المباشر.

#### 🔹 النمط (ب) — رابط Raw مفرد (Single Raw URL):
* إذا أرسل المستخدم رابطاً مباشراً لكود المشهد (مثل GitHub Raw, Gist, Pastebin):
  1. قم بجلب محتوى الرابط وحفظه فوراً في ملف `scene.html` بأمر:
     ```bash
     curl -fsSL "<RAW_URL>" -o scene.html
     ```
  2. انتقل لخطوة فحص الـ Headless ثم الرندر المباشر.

#### 🔹 النمط (ج) — روابط Raw متعددة مع طلب الدمج (Multi-Scene Batch & Concat):
* إذا أرسل المستخدم قائمة بروابط Raw لعدة مشاهد وطلب رندر كل مشهد ودمجهم بالترتيب:
  1. **حلقة الرندر التسلسلي**:
     - المشهد الأول: جلب الرابط الأول ➔ حفظه كـ `scene.html` ➔ فحصه ➔ رندره كـ `part_1.mp4`.
     - المشهد الثاني: جلب الرابط الثاني ➔ حفظه كـ `scene.html` ➔ فحصه ➔ رندره كـ `part_2.mp4`.
     - تكرار العملية لجميع المشاهد بالترتيب المطلوب (`part_3.mp4`, `part_4.mp4`...).
  2. **الدمج التلقائي السلس**: دمج جميع المقاطع بالترتيب عبر محرك الدمج الموضح في القسم 4 لاستخراج `final_merged_video.mp4`.
  3. رفع المقاطع المفردة + الفيديو النهائي المدمج على الـ Release.

#### 🔹 النمط (د) — بناء المشهد من ملف هوية (`identities/<name>.md`):
* إذا طلب المستخدم بناء مشهد من ملف هوية: اقرأ المواصفات البصرية، ابنِ كود `scene.html` كاملاً، افحصه ورندره.

---

## 2. العقد المعماري والطبقة التقنية الثابتة النقية (Ultra-Lean Technical Layer)

### 2.1 العقد التقني (Technical Contract)
يجب أن تعرّف طبقة الهوية في أي كود `scene.html` المتغيرات والدوال التالية بدقة:
- `CONFIG`: `{ fps: 30, width: number, height: number, duration: number }`.
- `OUTPUT_FILENAME`: اسم الملف المصدر كنص بدون امتداد.
- `audioBuffer`: كائن `AudioBuffer` أو `null` صراحة عند عدم وجود صوت.
- `async function prepareIdentity()`: دالة التهيئة والتحميل غير المتزامن للأصول والخطوط.
- `async function drawSceneAtTime(time)`: دالة الرسم اللحظية الأساسية لكل فريم (دائماً `async`).
- **الأدوات المتاحة تلقائياً**: `ctx`, `clamp01(val)`, `fetchAndDecodeAudio(url)`.

---

### 2.2 قالب HTML والطبقة التقنية الثابتة (Fixed Shell)

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
        /* إطار المعاينة المتجاوب تلقائياً مع كافة الأبعاد والشاشات بالـ vh و vw */
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
    </style>
</head>
<body>
    <div id="viewport">
        <canvas id="videoCanvas"></canvas>
        <div id="hud"><div class="spinner" id="spinner"></div><span id="status-text">جاري إعداد المشهد...</span></div>
        <div id="controls-overlay">
            <button class="btn btn-preview" id="btn-replay"><i class="ph ph-arrow-counter-clockwise"></i> تشغيل المعاينة</button>
            <button class="btn btn-render" id="btn-render-start"><i class="ph-fill ph-video-camera"></i> تصدير الفيديو</button>
        </div>
    </div>

    <script type="module">
        // ============ IDENTITY LAYER (CUSTOM MOTION LOGIC) ============

        // ============ FIXED TECHNICAL LAYER (ULTRA-LEAN RUNTIME) ============
        const canvas = document.getElementById('videoCanvas');
        const ctx = canvas.getContext('2d', { willReadFrequently: true });
        const statusText = document.getElementById('status-text');
        const spinner = document.getElementById('spinner');

        let audioAudioEl = null, sharedAudioCtx = null;
        let state = { currentTime: 0, isRendering: false, animationFrameId: null };

        window.renderStatus = 'loading';
        function clamp01(val) { return Math.max(0, Math.min(1, val)); }

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

        function audioBufferToWavBlob(buffer) {
            const numOfChan = buffer.numberOfChannels, length = buffer.length * numOfChan * 2 + 44;
            const out = new DataView(new ArrayBuffer(length));
            let channels = [], sample, offset = 0, pos = 0;
            function set16(d) { out.setUint16(pos, d, true); pos += 2; }
            function set32(d) { out.setUint32(pos, d, true); pos += 4; }
            set32(0x46464952); set32(length - 8); set32(0x45564157); set32(0x20746d66);
            set32(16); set16(1); set16(numOfChan); set32(buffer.sampleRate);
            set32(buffer.sampleRate * 2 * numOfChan); set16(numOfChan * 2); set16(16);
            set32(0x61746164); set32(length - pos - 4);
            for (let i = 0; i < numOfChan; i++) channels.push(buffer.getChannelData(i));
            while (offset < buffer.length) {
                for (let i = 0; i < numOfChan; i++) {
                    sample = Math.max(-1, Math.min(1, channels[i][offset]));
                    out.setInt16(pos, (0.5 + sample < 0 ? sample * 32768 : sample * 32767) | 0, true);
                    pos += 2;
                }
                offset++;
            }
            return new Blob([out], { type: "audio/wav" });
        }

        // محرك تسليم الفريمات بالدفعات السريعة لـ Node.js
        window.__ofoqGetFrameBatch = async function(startFrame, count) {
            const frames = [];
            for (let i = 0; i < count; i++) {
                const frameIndex = startFrame + i;
                if (frameIndex >= window.__ofoqTotalFrames) break;
                await drawSceneAtTime(frameIndex / window.__ofoqFps);
                const dataUrl = canvas.toDataURL('image/png');
                frames.push(dataUrl.substring(dataUrl.indexOf(',') + 1));
            }
            return frames;
        };

        function startPreviewLoop() {
            if (state.animationFrameId) cancelAnimationFrame(state.animationFrameId);
            if (audioAudioEl) {
                audioAudioEl.currentTime = 0;
                audioAudioEl.play().catch(() => {});
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

        document.getElementById('btn-replay').addEventListener('click', () => {
            statusText.textContent = "جاري عرض المعاينة...";
            spinner.style.display = 'inline-block';
            startPreviewLoop();
        });

        document.getElementById('btn-render-start').addEventListener('click', () => {
            alert("التصدير الفعلي يتم عبر تشغيل سكريبت render-runner.js");
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
                    const buf = await wavBlob.arrayBuffer();
                    let bin = '', bytes = new Uint8Array(buf);
                    for (let i = 0; i < bytes.length; i += 0x8000) bin += String.fromCharCode.apply(null, bytes.subarray(i, i + 0x8000));
                    window.__ofoqAudioWavBase64 = btoa(bin);
                }

                statusText.textContent = "جاهز للعرض والتصدير ✓";
                spinner.style.display = 'none';
                window.renderStatus = 'ready';
                await drawSceneAtTime(0);
            } catch (err) {
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

*(ملاحظة اختيارية: في حال احتاج مشهد معين لخلفية فيديو B-roll، يتم إنشاؤه ببساطة بـ 4 أسطر داخل `prepareIdentity` الخاصة بالمشهد: `videoEl = document.createElement('video'); videoEl.src = '...'; videoEl.crossOrigin = 'anonymous'; await new Promise(r => videoEl.onloadeddata = r);` وفي دالة الرسم: `videoEl.currentTime = time; ctx.drawImage(videoEl, 0, 0, w, h);`)*

---

## 3. بروتوكول التحقق وفحص ما قبل الرندر (Pre-Flight Verification)

قبل بدء الرندر الكامل، أنشئ سكريبت فحص Headless للتحقق من أن `scene.html` خالي من الأخطاء وأن حالة `window.renderStatus` أصبحت `ready`:

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

*يتم تشغيله بأمر: `node _headless_check.js`. إذا طبع `SCENE_OK` يتم الانتقال للرندر فوراً.*

---

## 4. محرك الرندر الشامل والدمج والتسليم (`render-runner.js` & Concat Engine)

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

---

### 4.2 محرك دمج المقاطع المتعددة (Multi-Video Concat Protocol)
عند رندر عدة مشاهد متتالية والمطلوب دمجها في فيديو نهائي موحد:
1. يتم إنشاء ملف نصي `concat_list.txt` يحتوي على أسماء المقاطع بالترتيب الدقيق:
   ```bash
   cat > concat_list.txt << 'EOF'
   file 'part_1.mp4'
   file 'part_2.mp4'
   file 'part_3.mp4'
   EOF
   ```
2. تنفيذ الدمج المباشر بدون فقدان جودة بأمر:
   ```bash
   ffmpeg -f concat -safe 0 -i concat_list.txt -c copy final_merged_video.mp4
   ```
3. (في حال وجود تباين طفيف في إعدادات الصوت بين المشاهد، يتم استخدام أمر إعادة الترميز المتوافق):
   ```bash
   ffmpeg -f concat -safe 0 -i concat_list.txt -c:v libx264 -pix_fmt yuv420p -crf 18 -c:a aac -b:a 192k final_merged_video.mp4
   ```

---

### 4.3 القواعد الصارمة للتسليم والتوثيق
1. **الامتثال التام للطلب**: تنفيذ طلب المستخدم بدقة سواء كان رندر مشهد مفرد أو مشاهد متعددة مع الدمج.
2. **منع البيانات الوهمية**: لا تلفق أي فحص؛ إذا فشل فحص الـ Headless أصلح الخطأ فوراً قبل الرندر.
3. **التسليم الكامل للـ Release**: أنشئ الـ Release وارفع كافة المقاطع المفردة، الفيديو النهائي المدمج، ملف الوصف، وكود `scene.html` إلى الـ Release وسجل ملف الإنجاز `TASK_COMPLETE.json`.
```
