# هوية أوفق البصرية + العقد التقني الإلزامي لفيديوهات القرآن الكريم

هذا الملف هو المرجع والموجه الإلزامي للـ Agent قبل وأثناء تنفيذ أي مهمة توليد مشاهد HTML/JS.
يحتوي على الهوية البصرية والعقد التقني الذي تعتمد عليه أداة الرندر (node agent.js render).

---

## القسم 1: الهوية البصرية

### الخطوط (Web Fonts عبر Google Fonts)
- **خط الآيات:** Amiri (افتراضي للأسلوب الكلاسيكي)، أو IBM Plex Sans Arabic (للمقاطع الحديثة/Shorts).
- **خط العناوين والهيدر:** Reem Kufi.
- **طريقة التحميل والإجبار:**
  تحميل الخطوط عبر <link> مباشر في <head>، والانتظار الإجباري في JS قبل بدء أي رسم على الكانفاس:
  
  await document.fonts.load("700 58px 'Amiri'");
  await document.fonts.ready;

### نظام الألوان والتخطيط
- **الخلفية:** تدرج داكن فخم من #0c0c0e إلى #0e0e11.
- **طبقة التعتيم (Overlay):** تدرج ذو شفافية من rgba(10,12,28,0.45) أعلى إلى rgba(10,12,28,0.75) أسفل.
- **التفاصيل والإطارات:** لون ذهبي ناعم #d4af37.
- **نص الآية:** أبيض ناصع #ffffff مع ظلال غامقة حادة مموهة للوضوح (text-shadow أو shadowBlur).
- **العناصر:**
  - إطار (Frame) ذهبي رفيع بداخل حواف الكانفاس بمسافة هامش مناسبة.
  - **الهيدر:** اسم السورة + اسم القارئ في الجزء العلوي.
  - **المنتصف:** نص الآية (مكتوبة بتفاف يدوي مانع للخروج عن الإطار).
  - **البطاقة السفلية (إن وجدت):** بطاقة تفسير متزامنة بدقة مع الآية.
  - **شريط التقدم:** شريط ذهبي رفيع في القاع يتقدم مع زمن الصوت الفعلي.

### الخلفية
- **الافتراضي:** منظر طبيعي/هندسي مرسوم برمجياً بـ SVG أو HTML Canvas داخل الملف نفسه بدون أصول خارجية (Zero external assets).
- **يمنع تماماً:** استخدام صور فوتوغرافية من الإنترنت إلا بوجود روابط ومصادر موثوقة ومصرحة.

---

## القسم 2: العقد التقني الإلزامي — **تخطي أي بند = فشل الرندر**

### 1. التهيئة الابتدائية وإلغاء السباق (Crucial Setup)
لا بد أن يحتوي الملف في أول الـ <body> أو <head> على سكريبت عادي (Synchronous) يضبط الحالة المبدئية وجميع مستمعات الأخطاء قبل تحميل أي استيرادات أسنكرونوس:

<script>
  window.__ofoqStatus = 'pending';
  window.__ofoqError = null;

  // التقاط الأخطاء غير المعالجة لمنع الـ Timeout
  window.addEventListener('error', (e) => {
    window.__ofoqStatus = 'error';
    window.__ofoqError = e.message || 'Uncaught Error';
  });
  window.addEventListener('unhandledrejection', (e) => {
    window.__ofoqStatus = 'error';
    window.__ofoqError = e.reason ? (e.reason.message || String(e.reason)) : 'Unhandled Rejection';
  });
</script>

### 2. محرك الفيديو: Mediabunny
يتم استخدام مكتبة Mediabunny حصراً للترميز والدمج. النمط الإجباري للاستيراد والـ Polyfill:

<script type="importmap">
{
  "imports": {
    "mediabunny": "https://esm.sh/mediabunny@1.50.8"
  }
}
</script>

<script type="module">
import {
    Output, Mp4OutputFormat, BufferTarget, CanvasSource,
    AudioBufferSource, QUALITY_HIGH, canEncodeAudio
} from 'mediabunny';

import { registerAacEncoder } from 'https://esm.sh/@mediabunny/aac-encoder?external=mediabunny';

// تفعيل ترميز الصوت على بيئات Linux/Chrome
if (!(await canEncodeAudio('aac'))) { 
    await registerAacEncoder(); 
}
</script>

### 3. الدالة الآمنة لتحويل الـ Base64 (إلزامية لمنع Call Stack Overflow)
يمنع تماماً استخدام String.fromCharCode(...array) لتحويل أحجام الفيديو الكبيرة. يجب استخدام هذه الدالة حصراً:

function bufferToBase64(arrayBuffer) {
  return new Promise((resolve, reject) => {
    const blob = new Blob([arrayBuffer], { type: 'video/mp4' });
    const reader = new FileReader();
    reader.onloadend = () => {
      // إزالة الـ Prefix الخاص بـ Data URL للحصول على Base64 الصافي
      const base64 = reader.result.split(',')[1];
      resolve(base64);
    };
    reader.onerror = reject;
    reader.readAsDataURL(blob);
  });
}

### 4. عقد النتيجة النهائية (The Completion Contract)
عند انتهاء معالجة وتصدير الفيديو بـ Mediabunny، يجب تنفيذ الآتي بالضبط:

try {
  // ... خروج الـ ArrayBuffer من Mediabunny (مثال: const buffer = await target.buffer;)
  const base64Data = await bufferToBase64(buffer);
  
  window.__ofoqFilename = "surah_name.mp4";
  window.__ofoqBase64 = base64Data;
  window.__ofoqStatus = 'done';
} catch (err) {
  window.__ofoqStatus = 'error';
  window.__ofoqError = err.message || String(err);
}

---

## القسم 3: القالب النموذجي الإلزامي للملف (scene.html)

أي ملف scene.html يكتبه الـ Agent يجب أن يتتبع هذا الهيكل البرمجي دون حيد:

<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>Ofoq Quran Scene</title>
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Amiri:wght@700&family=Reem+Kufi:wght@700&display=swap">
  <style>
    body { margin: 0; padding: 0; background-color: #0c0c0e; overflow: hidden; }
    canvas { display: block; width: 100vw; height: 100vh; }
  </style>
</head>
<body>
  <canvas id="stage" width="1080" height="1920"></canvas>

  <script>
    window.__ofoqStatus = 'pending';
    window.__ofoqError = null;
    window.addEventListener('error', e => { window.__ofoqStatus = 'error'; window.__ofoqError = e.message; });
    window.addEventListener('unhandledrejection', e => { window.__ofoqStatus = 'error'; window.__ofoqError = e.reason?.message || String(e.reason); });
  </script>

  <script type="importmap">
  { "imports": { "mediabunny": "https://esm.sh/mediabunny@1.50.8" } }
  </script>

  <script type="module">
    import { Output, Mp4OutputFormat, BufferTarget, CanvasSource, AudioBufferSource, QUALITY_HIGH, canEncodeAudio } from 'mediabunny';
    import { registerAacEncoder } from 'https://esm.sh/@mediabunny/aac-encoder?external=mediabunny';

    function bufferToBase64(arrayBuffer) {
      return new Promise((resolve, reject) => {
        const blob = new Blob([arrayBuffer], { type: 'video/mp4' });
        const reader = new FileReader();
        reader.onloadend = () => resolve(reader.result.split(',')[1]);
        reader.onerror = reject;
        reader.readAsDataURL(blob);
      });
    }

    async function render() {
      if (!(await canEncodeAudio('aac'))) { await registerAacEncoder(); }
      await document.fonts.load("700 58px 'Amiri'");
      await document.fonts.ready;

      const canvas = document.getElementById('stage');
      const ctx = canvas.getContext('2d');

      // 1. جلب وفك تشفير الصوت لـ AudioBuffer
      const audioCtx = new (window.AudioContext || window.webkitAudioContext)({ sampleRate: 44100 });
      const audioRes = await fetch("https://everyayah.com/data/Alafasy_128kbps/001001.mp3");
      const audioArrayBuf = await audioRes.arrayBuffer();
      const audioBuffer = await audioCtx.decodeAudioData(audioArrayBuf);

      const fps = 30;
      const duration = audioBuffer.duration;
      const totalFrames = Math.ceil(duration * fps);

      // 2. إعداد Mediabunny Target & Output
      const target = new BufferTarget();
      const output = new Output({
        target,
        format: new Mp4OutputFormat(),
        quality: QUALITY_HIGH
      });

      const videoSource = new CanvasSource(canvas, { fps });
      const audioSource = new AudioBufferSource(audioBuffer);

      output.addVideoTrack(videoSource);
      output.addAudioTrack(audioSource);

      await output.start();

      // 3. حلقة الرسم المباشر وتصدير الفريمات
      for (let i = 0; i < totalFrames; i++) {
        const currentTime = i / fps;

        // --- عملية الرسم على الكانفاس بناءً على currentTime ---
        ctx.fillStyle = '#0c0c0e';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        
        ctx.font = "700 58px 'Amiri'";
        ctx.fillStyle = "#ffffff";
        ctx.textAlign = "center";
        ctx.fillText("بِسْمِ اللَّهِ الرَّحْمَٰنِ الرَّحِيمِ", canvas.width / 2, canvas.height / 2);
        // ----------------------------------------------------

        await videoSource.addFrame();
      }

      await output.finalize();

      // 4. تسليم الناتج طبقاً للعقد التقني
      const finalBuffer = target.buffer;
      window.__ofoqFilename = "surah_1.mp4";
      window.__ofoqBase64 = await bufferToBase64(finalBuffer);
      window.__ofoqStatus = 'done';
    }

    render().catch(err => {
      window.__ofoqStatus = 'error';
      window.__ofoqError = err.message || String(err);
    });
  </script>
</body>
</html>

---

## القسم 4: قواعد صارمة وطرق التشغيل — غير قابلة للتفاوض

1. **مصدر النصوص القاطع:** ممنوع منعاً باتاً كتابة نص آية أو تفسير من ذاكرة الـ Agent الداخلية. أي نص يجب أن يأتي عبر طلب curl ينفذ حياً أثناء الجلسة من API موثوق (مثل api.alquran.cloud).
2. **الزمن والتقطيع:** مدة الفيديو وكل التوقيتات الانتقالية تحسب من audioBuffer.duration الفعلي للصوت المكتنز بعد فكه وليس اعتباطاً.
3. **مصدر الصوت:** دائمًا من everyayah.com: https://everyayah.com/data/{reciter}/{surah:3}{num:3}.mp3 (القارئ الافتراضي: Alafasy_128kbps).
4. **تشغيل الرندر:** عن طريق الأمر التالي فقط:
   node agent.js render path/to/scene.html
   ويجب استقبال الرد بصيغة JSON مفردة:
   {"success":true,"local_path":"output/xxx.mp4","filename":"xxx.mp4","size_bytes":123456}
5. **ملفات التتبع العلامية (Marker Files):**
   - بعد رفع الفيديو والوصف الخاص به على GitHub Release: اكتب video_<رقم السورة>_done.json.
   - عند إنهاء كل الفيديوهات المطلوبة في الدفعة: اكتب TASK_COMPLETE.json.

