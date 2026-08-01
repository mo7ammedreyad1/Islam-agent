# هوية الـ Agent + العقد التقني الإلزامي لفيديوهات القرآن الكريم

هذا الملف هو كل اللي محتاجه الـ Agent قبل ما يبدأ أي مهمة. فيه هوية الـ Agent
نفسه وطريقة تعامله العامة، وفيه أيضًا **العقد التقني الحرفي** اللي لازم يلتزم بيه
كل ملف `scene.html` تكتبه، وسكريبت الرندر اللي هتكتبه إنت بنفسك (Playwright) عشان
يشتغل من غير أخطاء من أول محاولة.

> ⚠️ **تنبيه حاسم**: `agent.js` **مفيهوش أي subcommand اسمه "render"**. الأداة
> الوحيدة المتاحة ليك هي `run_terminal` بس. **ممنوع منعًا باتًا تنفّذ "node agent.js"
> (بأي شكل، بأي args) كأمر terminal من جوه جلستك** — ده مش أداة رندر، ده نفس
> العقل اللي بيكلمك دلوقتي، وتشغيله هيبدأ جلسة Agent كاملة تانية من الصفر فوق
> نفس الريبو ونفس الـ Release، وهيضيع تقدمك الحالي بالكامل. الرندر سكريبت
> Node.js منفصل **إنت اللي بتكتبه** وتشغّله بـ `node اسم-السكريبت.js` — الوصفة
> الكاملة والمُختبَرة موجودة في "دليل كتابة سكريبت الرندر" بالأسفل، انسخها زي
> ما هي.

> 📄 **ملف مرجعي مهم**: `docs/mediabunny-llms-full.txt` هو التوثيق الرسمي الكامل
> لمكتبة Mediabunny (المحرك الوحيد المستخدم لتصدير الفيديو في المشروع ده). أي
> كود يخص Mediabunny (بناء `Output`، الكودكس، الـ AAC encoder، قراءة فيديوهات
> B-roll) **مصدره الوحيد هو الملف ده** — **افتحه واقرأه بالكامل** وقت الحاجة
> (مش بحث جزئي بكلمة مفتاحية)، مش من الذاكرة ومش من نسخة قديمة متخيلة. تفاصيل
> أكتر في القسم 2.

---

## القسم 1: هوية الـ Agent

### هوية الفيديو — مستويين مختلفين تمامًا
**1. الإحساس العام** (نوع الفيديو، طابعه العاطفي/الدعوي، بدون أي كود) موصوف في
ملفات `.md` جوه [`video-identities/`](./video-identities/) — كل ملف بيوصف نوع
فيديو معيّن بالكلام بس.

**2. الهوية الكاملة القابلة للتنفيذ** — ملف `.md` واحد **self-contained بالكامل**
جوه [`identities/`](./identities/)، فيه كل حاجة الفيديو ده محتاجها: شكل المحتوى
(نص آيات؟ حديث؟ صوت خارجي بس؟)، طريقة جلب أي أصول (صوت/صورة/فيديو B-roll)،
والتصميم البصري (CSS + منطق الرسم على الـ canvas). **مفيش تقسيم فرعي جوه الملف
ده لـ"طبقات" — هو وحدة واحدة متكاملة**، لأن شكل المحتوى وطريقة جلب الأصول
بيختلفوا جذريًا من هوية للتانية (هوية ممكن تكون آيات + تفسير من غير نص، وهوية
تانية حديث نبوي مالوش أي علاقة بالقرآن، وهوية تالتة صوت بس من غير نص خالص،
ورابعة محتاجة فيديو B-roll وخامسة مش محتاجة أي أصل خارجي أصلًا) — فمحاولة فرض
شكل موحّد عليهم كانت بتقيّد الهويات الجديدة من غير أي داعي حقيقي.

- **اسم ملف الهوية المطلوب يُحدَّد صراحة في وصف المهمة نفسها** (مثلًا: "اعمل
  فيديو بناءً على هوية `identities/<اسم-الملف>.md`"). افتح الملف المذكور
  بالحرف قبل ما تكتب أي حرف في `scene.html` — **ميصحش تفترض أو "تتذكر" هوية
  استخدمتها قبل كده لو المهمة الحالية سمّت ملف تاني أو محددتش اسم**؛ في الحالة
  التانية دي ارجع للمستخدم واسأله أي ملف يستخدم.
- ملفات `video-identities/*.md` وصف بالكلام — التزم بروحه، مش نسخ حرفي.

### `scene.html` = ملف الهوية + ملحق تقني ثابت صغير جدًا
راجع "القسم 2" قبل أول مهمة تعملها. عمليًا `scene.html` بيتبني من حاجتين بس:

1. **كود ملف الهوية بالكامل** (`identities/<اسم>.md`) — **يُنسخ حرفيًا**، مش
   بيتعاد صياغته أو "تحسينه". فيه محتوى المهمة (تعدّله لكل فيديو)، جلب أي أصول
   الهوية دي محتاجاها، ومنطق الرسم.
2. **ملحق تقني ثابت صغير جدًا** (~30-40 سطر: عقد الـ render hooks + هيكل الـ
   DOM الأساسي + جسر الـ base64) — **موجود كامل في القسم 2، يُنسخ حرفيًا من
   غير أي تعديل** — بالإضافة لكود تصدير Mediabunny **تكتبه إنت بالاستعانة
   الكاملة بـ `docs/mediabunny-llms-full.txt`** (مش نسخة ثابتة محفوظة هنا؛
   التفاصيل والسبب في القسم 2).

**فرّق بين حاجتين طول الوقت:**
- **الملحق التقني الثابت + عقد الـ hooks**: مايتفاوضش فيه أبدًا. أي مخالفة ليه
  = فشل الأتمتة (حتى لو الفيديو نفسه اتصدّر صح).
- **كل حاجة تانية في ملف الهوية** (المحتوى، الأصول، التصميم): بتتغيّر بحرية
  حسب المهمة والهوية المختارة.

### تحقق إلزامي بعد كتابة `scene.html`، قبل ما تكمّل للرندر
- **اطبع القيم الفعلية لمنطقة تعديل المحتوى** (`grep`/`cat` على `scene.html`
  نفسه، مش من الكود اللي كتبته في رأسك) وقارنها بالمطلوب فعليًا في المهمة —
  أسماء المتغيرات هنا بتختلف من هوية لهوية (مفيش أسماء موحّدة مفروضة)، فدوّر
  على "منطقة تعديل المحتوى" جوه الهوية نفسها.
- **تأكد إن الدالتين الإلزاميتين (`prepareIdentity`, `drawSceneAtTime`) وثابت
  `CONFIG`/`OUTPUT_FILENAME` موجودين فعليًا في `scene.html`**
  (`grep -n "function prepareIdentity\|function drawSceneAtTime\|const CONFIG\|OUTPUT_FILENAME" scene.html`)
  — نسيان نسخهم من ملف الهوية بيدّي خطأ `is not defined` وقت التشغيل.
- **فحص إلزامي لصحة `scene.html` قبل تشغيل سكريبت الرندر الكامل** (مش بعده):
  افتح الصفحة headless لكام ثانية بس (من غير `?autorender=true`) والتقط أي
  `pageerror`، زي كده:
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
  لو طبع `SYNTAX_ERROR`، **متكملش على سكريبت الرندر الكامل خالص** — رجع اصلح
  `scene.html` الأول.

---

## القسم 1.5: استيعاب هوية بصرية من ملف خارجي غير موثّق التوافق

أحيانًا هتوصلك مهمة مرفق معاها ملف `.html` (من المستخدم، أو ناتج من أداة تصميم
تانية، أو من محادثة سابقة) بيوصف "عايز فيديو شكله كده" — **من غير أي ضمان إنه
مكتوب أصلًا لهذا الرانر**. **الخطوة دي إلزامية قبل ما تستخدم أي ملف من النوع ده
بأي شكل — سواء كمرجع مباشر أو كإلهام بصري.**

### الخطوة 1: فحص تصنيف صريح — طبّقه بند بند، مش انطباع عام
افتح الملف وابحث فعليًا (مش تخمين من شكله العام) عن كل بند من دول:

1. **الـ render hooks كاملة وشغّالة فعليًا**: `window.renderStatus` بكل قيمه،
   `window.renderProgress`، دالة اسمها بالظبط `window.startVideoRender()`،
   دعم `?autorender=true`، حدث `video-render-complete` بيتطلق فعليًا، وجود
   `window.__renderFilename` و `window.__renderBase64`.
2. **محرك الفيديو Mediabunny حصرًا** — مش `ffmpeg`، مش `MediaRecorder`/
   `canvas.captureStream`، مش أي مكتبة تانية.
3. **polyfill الـ AAC encoder** موجود فعليًا (`registerAacEncoder` +
   `canEncodeAudio('aac')`) — من غيره التصدير هيفشل على الـ CI runner.
4. **أي أصل صوتي/بصري خارجي يتجاب بـ `fetch()` وقت التشغيل**، مش تحميل يدوي
   مسبق ومسار محلي مُشار له.
5. **التوقيت محسوب من الأصل الحقيقي**: أوقات المشاهد طالعة فعليًا من مدة
   الصوت الحقيقية (`AudioBuffer.duration`) بعد فك التشفير — مش أرقام ثواني
   مكتوبة يدويًا بالتخمين.
6. **الخلفية/الصور**: إما مرسومة بالكانفاس بالكامل، أو متجابة بـ
   `crossOrigin="anonymous"` من مصدر بيدعم CORS فعليًا.

### الخطوة 2: الناتج دايمًا ملف هوية `.md` جديد self-contained — الفحص بيحدد مستوى الثقة بس
**الملف الخارجي — عدّى الفحص ولا لأ — ملهوش أي سيناريو تستخدم فيه منطقه
التنفيذي (render/hooks/جلب أصول/تصدير) حرفيًا أبدًا.** الفحص فوق بيحدد **مدى
الثقة في الفصل بين تصميمه وتنفيذه** وقت الاستخراج:

- **عدّى بكل البنود الستة** → غالبًا الملف مبني أصلًا بفصل واضح بين الرسم
  والتنفيذ، فاستخراج كود التصميم/المحتوى/جلب الأصول منه غالبًا مباشر.
- **فشل في بند واحد أو أكتر (الحالة الشائعة)** → خد وقتك أكتر في الفصل،
  متفترضش إن أي دالة "شكلها رسم" خالية من قرارات تنفيذية متسرّبة جواها (زي
  أرقام توقيت ثابتة، أو رابط مصدر مكتوب داخل دالة الرسم نفسها).
- **في الحالتين**: يُستخرج **كل حاجة تخص الهوية** (المحتوى، طريقة جلب الأصول،
  التصميم) ويُبنى منها ملف **`identities/<اسم-جديد>.md` جديد self-contained
  ومستقل**، بصيغة "عقد ملف هوية .md" في القسم 2 — **بدون أي جزء من منطق
  render/hooks/تصدير**، لأن دي بتيجي من الملحق التقني الثابت + توثيق
  Mediabunny بس، مهما كان الملف الأصلي شايلها أو لأ.
  - لا تعديل مباشر على الملف الخارجي غير الموثوق، ولا استبدال لملف قائم.
  - لو المهمة بس عايزة فيديو واحد بالستايل ده (من غير إضافة دائمة للمجلد)،
    اكتب كود الهوية المستخرج مباشرة جوه `scene.html` من غير ما تضيف ملف هوية
    جديد، إلا لو المستخدم طلب صراحة إضافته كهوية دائمة.
- **حالة غير واضحة** → افتراضيًا خد وقتك في الفصل (الخيار الأكثر حذرًا)، مش
  تفترض ثقة أعلى مما تستحق.

**لتنفيذ الخطوة دي بشكل احترافي وموثّق، فيه Skill جاهزة** (`skill.md` في جذر
هذا الريبو، أو أي نسخة منه اتديت لأجينت تاني) بتعمل بالظبط الاستخراج ده —
راجعها لو متاحة قبل ما تعمل الاستخراج يدويًا من الصفر.

---

## القسم 2: العقد التقني — ملحق ثابت صغير + الاعتماد المباشر على توثيق Mediabunny

### لماذا الشكل ده بالذات
كل حاجة تخص Mediabunny (بناء `Output`، الكودكس، الـ AAC encoder، قراءة فيديو
B-roll) **موثّقة رسميًا وبتفصيل ممتاز في `docs/mediabunny-llms-full.txt`**،
ومحافظة على نسخة مكررة منها هنا معناها مصدرين للحقيقة ممكن يتعارضوا مع الوقت
(خصوصًا مع تحديثات المكتبة). فبدل كده: **الملف الوحيد اللي بيتغيّر من مهمة
لمهمة هو ملف الهوية نفسه، والملحق التقني هنا أصغر ما يمكن** (بس الحاجتين اللي
هما اختراعنا احنا ومش هتلاقيهم في أي توثيق خارجي: عقد الـ hooks وجسر الـ
base64)، وكل حاجة Mediabunny بتتكتب بالاستعانة المباشرة بالتوثيق وقت الحاجة.

### 📄 قاعدة القراءة: بالكامل، مش بحث جزئي
لما تحتاج أي حاجة من Mediabunny (تصدير، كودك، AAC، B-roll)، **افتح
`docs/mediabunny-llms-full.txt` واقرأه بالكامل** (أو على الأقل الأقسام
المتعلقة بالكامل من أولها لآخرها) — مش `grep` على كلمة مفتاحية واحدة وبناء
افتراضات من سطرين مقطوعين من سياقهم. الملف منظّم بعناوين واضحة (`Media
sources`, `Media sinks`, `Writing media files`, `Converting media files`,
تسجيلات الكودكس المختلفة...) — استخدمها للتنقل، لكن اقرأ القسم اللي هتستخدمه
كامل قبل ما تكتب كود بناءً عليه.

### تلميحات مُختبرة عندنا بس (مش موجودة في التوثيق العام، من تجربة فعلية على الـ CI)
- **AAC encoder polyfill إلزامي دايمًا لو الفيديو فيه صوت** (`canEncodeAudio('aac')`
  ثم `registerAacEncoder()` من `@mediabunny/aac-encoder` لو مش مدعوم أصليًا)
  — Chrome على Linux (بيئة الـ CI) مفيهوش AAC encoder أصلي في WebCodecs، بغض
  النظر عن مصدر الصوت.
- **ترتيب محاولات الكودك المُختبر عندنا** (جرّب بالترتيب ده وارجع للتالي لو
  فشل): `avc` عادي (`QUALITY_HIGH`) ← `avc` ببت ريت أقل (~3.5Mbps) ←
  `avc` بـ `fullCodecString: 'avc1.42001f'` ← `vp9`/WebM ← `vp8`/WebM (مع
  `opus` بدل `aac` لو الحاوية WebM). كل محاولة داخل حاوية MP4 لازم تكون AVC،
  وكل محاولة WebM لازم تكون VP9/VP8 — الحاوية والكودك مرتبطين.
- `'avc'`/`'aac'`/`'opus'` نصوص عادية (strings)، مش قيم مستوردة من أي مكان —
  لا يوجد export اسمه `VideoCodec`/`AudioCodec` وقت التشغيل.
- **لو محتاج B-roll فيديو خلفية**: `Input` + `UrlSource` (أو `BlobSource`)
  + `CanvasSink` بيدّوك فريم كانفاس جاهز في أي وقت (`sink.getCanvas(t)`) —
  اقرأ قسم "Media sinks" → "Video data sinks" بالتوثيق للتفاصيل الكاملة
  (خيارات `fit`/`crop`/`poolSize`). **مهم**: قراءة فريم فيديو async، فـ
  `drawSceneAtTime` لازم تكون `async` وتتـ`await` (تفاصيل في عقد ملف الهوية
  تحت) عشان تقدر تستخدم `await sink.getCanvas(t)` جواها.

### الملحق التقني الثابت — انسخه حرفيًا زي ما هو

**هيكل الـ `<head>`/`<body>` الثابت** (الأماكن المعلّمة بـ 🎨 بتتملى من ملف الهوية):

```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Video Clip (مع دعم Agent Auto-Render)</title>

    <!-- 🎨 IDENTITY: روابط الخطوط -->

    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/regular/style.css" />
    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/fill/style.css" />

    <!-- 🎨 IDENTITY: كتلة الـ CSS كاملة -->
    <style>
    </style>
</head>
<body>
    <div id="viewport">
        <!-- 🎨 IDENTITY: width/height -->
        <canvas id="shortsCanvas" width="1080" height="1920"></canvas>
        <div id="hud"><div class="spinner" id="spinner"></div><span id="status-text">جاري التحضير...</span></div>
        <div id="controls-overlay">
            <button class="btn btn-preview" id="btn-replay"><i class="ph ph-arrow-counter-clockwise"></i> تشغيل المعاينة</button>
            <button class="btn btn-preview" id="btn-toggle-console"><i class="ph ph-terminal-window"></i> سجل الأخطاء</button>
            <button class="btn btn-render" id="btn-render-start"><i class="ph-fill ph-video-camera"></i> تصدير الفيديو</button>
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
        import { Output, Mp4OutputFormat, WebMOutputFormat, BufferTarget, CanvasSource, AudioBufferSource, QUALITY_HIGH, canEncodeAudio } from 'mediabunny';

        // ============ 🎨 كود ملف الهوية بالكامل هنا (منسوخ حرفيًا من identities/<اسم>.md) ============
        // لازم يعرّف: CONFIG, OUTPUT_FILENAME, audioBuffer (أو null),
        // async function prepareIdentity(), async function drawSceneAtTime(time)

        // ============ ⚙️ الملحق التقني الثابت — من هنا تحت، انسخه حرفيًا ============
    </script>
</body>
</html>
```

**الملحق التقني الثابت بالكامل** (يحل محل التعليق الأخير فوق):

```js
const canvas = document.getElementById('shortsCanvas');
const ctx = canvas.getContext('2d', { willReadFrequently: true });
const statusText = document.getElementById('status-text');
const spinner = document.getElementById('spinner');

let previewAudioEl = null;
let isRendering = false;
let animationFrameId = null;

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

// --- أدوات عامة صغيرة، متاحة لكود الهوية لو حابة تستخدمها (اختيارية، مش إلزامية) ---
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

// خوارزمية تخطيط نص عربي RTL عامة — استخدمها لو الهوية محتاجة نص، من غير
// إعادة تعريفها. لو محتاجة خوارزمية مختلفة تمامًا (أو مش محتاجة نص خالص، زي
// هوية B-roll بحتة)، تجاهلها.
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

// تحويل AudioBuffer إلى WAV Blob — للمعاينة الحية بس (مش جزء من الفيديو
// المُصدَّر، ده Mediabunny بياخد الـ AudioBuffer مباشرة زي ما هو موضح في
// docs/mediabunny-llms-full.txt)
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

// --- حلقة المعاينة الحية — بتنادي drawSceneAtTime بس، مفيهاش أي منطق تصميم.
// بتشتغل بصوت (متزامنة مع previewAudioEl) أو من غير صوت خالص (ساعة داخلية) ---
function startPreviewLoop() {
    if (animationFrameId) cancelAnimationFrame(animationFrameId);
    let silentClockStart = null;

    if (previewAudioEl) {
        previewAudioEl.currentTime = 0;
        previewAudioEl.play().catch(e => logToConsole("تنبيه الصوت: " + e.message, 'warn'));
    } else {
        silentClockStart = performance.now();
    }

    async function loop() {
        if (isRendering) return;
        const currTime = previewAudioEl
            ? previewAudioEl.currentTime
            : (performance.now() - silentClockStart) / 1000;

        await drawSceneAtTime(currTime);

        if (currTime < CONFIG.duration) {
            animationFrameId = requestAnimationFrame(loop);
        } else {
            statusText.textContent = "جاهز للعرض والتصدير ✓";
            spinner.style.display = 'none';
        }
    }
    animationFrameId = requestAnimationFrame(loop);
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

// --- exportWithFallback: الدالة الوحيدة اللي مفيش نسخة كود ثابتة منها هنا
// عمدًا — اكتبها بالاستعانة الكاملة بـ docs/mediabunny-llms-full.txt (افتحه
// بالكامل) + التلميحات المُختبرة فوق. لازم تحقق:
//   1. تفعيل AAC polyfill لو الفيديو فيه صوت (audioBuffer !== null).
//   2. window.renderStatus = 'rendering' + إيقاف المعاينة (isRendering = true).
//   3. بناء Output (Mp4OutputFormat/BufferTarget) + CanvasSource للفيديو +
//      (لو فيه صوت) AudioBufferSource — التفاصيل الكاملة في قسم "Media
//      sources"/"Writing media files" بالتوثيق.
//   4. لف على الفريمات من 0 لـ CONFIG.duration بخطوة 1/CONFIG.fps، نادي
//      await drawSceneAtTime(timestamp) ثم await videoSource.add(...).
//   5. عند فشل محاولة، جرّب القائمة الاحتياطية المُختبرة فوق بالترتيب.
//   6. عند النجاح: window.__renderFilename/__renderBase64 (بـ
//      arrayBufferToBase64)، window.renderStatus = 'completed',
//      window.renderProgress = 1.0, وأطلق حدث video-render-complete.
//   7. عند فشل كل المحاولات: window.renderStatus = 'error' +
//      window.__renderError.
// async function exportWithFallback() { ... }

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
        await prepareIdentity(); // من كود الهوية — يجيب أي أصول محتاجها ويجهز أي بيانات مشاهد

        if (audioBuffer) {
            // لو الهوية فيها صوت، مدته الحقيقية هي مصدر الحقيقة الوحيد لمدة
            // الفيديو (راجع القسم 4، البند 2) — بتتفرض هنا تلقائيًا.
            const wavBlob = audioBufferToWavBlob(audioBuffer);
            previewAudioEl = new Audio(URL.createObjectURL(wavBlob));
            CONFIG.duration = audioBuffer.duration;
        }
        // لو مفيش صوت (audioBuffer === null)، CONFIG.duration لازم يكون
        // اتحدد فعلًا جوه prepareIdentity() أو في تعريف CONFIG نفسه — دي
        // مسؤولية الهوية بالكامل في الحالة دي.

        statusText.textContent = "جاهز للعرض والتصدير ✓";
        spinner.style.display = 'none';
        window.renderStatus = 'ready';
        await drawSceneAtTime(0);

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

### 🎨 عقد ملف هوية `.md` — إيه اللي لازم يوفّره
ملف `identities/<اسم>.md` هو **مواصفة كاملة self-contained قابلة للنسخ الحرفي**
(كود، مش وصف عام). كل ملف هوية `.md` لازم يحتوي على الأقسام دي بالترتيب:

1. **فقرة وصف** (الاسم، الطابع البصري/الروح العامة، الأبعاد ونسبتها، حالة
   الاستخدام، **وأهم حاجة: شكل المحتوى اللي الهوية دي بتستخدمه** — آيات فقط؟
   آيات+تفسير؟ حديث؟ صوت خارجي بس بدون نص؟ فيديو B-roll؟ أي تركيبة).
2. **روابط الخطوط** (لو محتاجة نص عربي).
3. **كتلة CSS كاملة** — تستهدف الـ IDs/classes الثابتة الموجودة في الملحق
   التقني (`body`, `#viewport`, `canvas`/`#shortsCanvas`, `#hud`, `.spinner`
   + `@keyframes spin`, `#status-text`, `#controls-overlay`, `.btn`,
   `.btn-preview`, `.btn-render`, `#console-modal`, `#console-header`,
   `#console-output`, `.log-line`, `.log-info`, `.log-warn`, `.log-error`).
   لازم تبدأ بـ `* { box-sizing: border-box; margin: 0; padding: 0; }`.
4. **أبعاد الكانفاس** (`width`/`height` اللي هتتحط في `<canvas>`).
5. **منطقة تعديل المحتوى** — ثوابت حرة الشكل تمامًا، بأي أسماء تناسب الهوية
   دي بالذات (مفيش أسماء موحّدة مفروضة عبر الهويات). لازم تكون معلّمة بتعليق
   واضح `// ⬛ منطقة قابلة للتعديل لكل فيديو جديد` عشان تتلاقى بسهولة.
6. **كود JS الهوية بالكامل**، لازم يعرّف في الآخر:
   - `let CONFIG = { fps, width, height, duration }` — لو فيه صوت، `duration`
     هنا placeholder هيتفرض عليه القيمة الحقيقية تلقائيًا (راجع الملحق
     التقني)؛ لو مفيش صوت، القيمة هنا لازم تكون هي الصح فعلًا من غير أي تعديل
     تلقائي بعد كده.
   - `const OUTPUT_FILENAME`
   - `let audioBuffer = null;` — يفضل `null` لو الهوية دي بصرية بحتة، أو
     بيتحدد جوه `prepareIdentity()` لو فيها صوت.
   - أي متغيرات/دوال داخلية تانية الهوية محتاجاها (حرة تمامًا).
   - **دالتين إلزاميتين، الاتنين `async`**:
     - `async function prepareIdentity()`: بتتنادى مرة واحدة قبل أول رسمة.
       فيها **كل** منطق جلب/تجهيز الأصول الخاص بالهوية دي (لو موجود): جلب صوت
       (بـ `fetch()` من أي مصدر، مش شرط everyayah.com)، تحميل صورة
       (`crossOrigin="anonymous"`)، تجهيز فيديو B-roll (`Input`+`UrlSource`+
       `CanvasSink` من Mediabunny — راجع التلميحات فوق)، بناء أي بيانات مشاهد
       لازمة للرسم. لو الهوية مش محتاجة أي أصل خارجي، ممكن تكون شبه فاضية.
     - `async function drawSceneAtTime(time)`: نقطة الدخول الوحيدة اللي
       الملحق التقني بيناديها كل فريم (بـ `await`)، سواء في المعاينة أو
       التصدير. بترسم كل حاجة بناءً على الوقت الحالي.
7. **أدوات جاهزة اختيارية من الملحق التقني**، متاحة تلقائيًا من غير استيراد:
   `layoutArabicParagraph`, `Easing`, `clamp01`, `toArabicDigits`, `ctx`.
8. **ملاحظات معروفة** خاصة بالهوية دي بس (لو فيه).

### الأصول: كل هوية مسؤولة عن أصولها بنفسها، جوه `prepareIdentity()`
مفيش دالة جلب أصول عامة مفروضة على كل الهويات (زي `preloadEveryAyahQuranAudio`
قديمًا) — كل هوية بتجيب اللي هي محتاجاه بس، بنفس المبادئ العامة دي:

- **fetch مباشر جوه المتصفح وقت التشغيل، من غير تحميل محلي بـ `curl`** —
  القاعدة دي بلا استثناء لأي أصل (صوت/صورة/فيديو)، حتى لو غرضك تجربة سريعة.
  استخدم `curl -sI` (رأس الطلب بس) لو عايز تتأكد إن رابط موجود.
- **الصور**: `crossOrigin = 'anonymous'` إلزامي لأي صورة هتترسم على الـ
  canvas، وإلا خطأ `VideoFrames can't be created from tainted sources`.
- **فيديو B-roll**: عبر `Input`+`UrlSource`+`CanvasSink` من Mediabunny —
  التفاصيل الكاملة في `docs/mediabunny-llms-full.txt`.
- **قاعدة fallback إلزامية**: لو أصل اختياري فشل يتحمل (صورة، مثلًا)،
  `prepareIdentity()` لازم تتعامل مع الفشل بـ `try/catch` وترجع لبديل مرسوم
  بالكانفاس — **الفيديو ميفشلش يتصدّر بس لأن أصل اختياري مكانش موجود**. أصل
  **إلزامي** (زي الصوت الأساسي لتلاوة قرآنية) لو فشل، يبقى صح إن `prepareIdentity()`
  ترمي error فعلي (بيتلقفه `init()` ويسجل `renderStatus = 'error'`).
- **قاعدة تجنّب "canvas tainted"**: `scene.html` لازم يتفتح دايمًا عن طريق
  سيرفر HTTP محلي (زي اللي في وصفة الرندر تحت)، **مش** بمسار `file://` مباشر.

### عقد الـ render hooks الإلزامي — مطابق تمامًا لما هو مكتوب في الملحق التقني فوق
كل `scene.html` لازم يحتوي فعليًا على الـ hooks دي شغّالة (موجودة كاملة في
الملحق التقني، منعًا باتًا تتغيّر أسماؤها):

- `window.renderStatus`: `'loading'` → `'ready'` → `'rendering'` → `'completed'` أو `'error'`.
- `window.renderProgress`: رقم من 0 لـ 1 بيتحدّث أثناء الرندر.
- `window.startVideoRender()`: دالة بتبدأ عملية التصدير الفعلية.
- **دعم `?autorender=true` في رابط الصفحة**: لما موجود، الصفحة تستدعي
  `startVideoRender()` لوحدها بعد التحميل، من غير أي تفاعل يدوي.
- **حدث `video-render-complete`**: بيتطلق على `window` بعد نجاح التصدير.
- `window.__renderFilename` و `window.__renderBase64`: بعد نجاح التصدير،
  اسم الملف الناتج ومحتواه كـ base64 — جسر لازم لسكريبت الـ Playwright.

### دليل كتابة سكريبت الرندر — انسخه حرفيًا، محدّث وشغّال فعليًا
اكتبه بأمر `run_terminal` (heredoc) في ملف زي `render-runner.js`، وشغّله بعد كده
بأمر `run_terminal` تاني: `node render-runner.js`. **مهم**: workflow الـ CI بيثبّت
قناة `chrome` بس (مش `chromium` الافتراضي) — لازم تحدد `channel: 'chrome'` صراحة
في `launch()` وإلا الرندر هيفشل بـ "Executable doesn't exist".

> ⚠️ **`{ timeout: 8 * 60 * 1000 }` في `page.waitForFunction` تحت ده إلزامي، مش
> اختياري ومش قابل للحذف أو التقليل.** الـ default بتاع Playwright هو 30 ثانية
> بس، ورندر فيديو فعلي بياخد دقايق — لو كتبت `page.waitForFunction` من غير الـ
> `timeout` ده صراحة (أو بأي قيمة أقل)، السكريبت هيكراش بـ `TimeoutError` قبل
> ما الرندر يخلص أصلًا، حتى لو باقي الكود كله صح 100%. انسخ السكريبت اللي تحت
> **حرفيًا بالكامل** من غير ما تعيد كتابته من الصفر أو تختصر فيه.
>
> **لو اضطريت تعيد كتابة `render-runner.js` أكتر من مرة في نفس المهمة** (مثلًا
> عشان تصلح حاجة تانية فيه)، اعمل `grep -n "waitForFunction" render-runner.js`
> **قبل** ما تشغّله في كل مرة (مش بعد ما يفشل) وشوف **كل الأسطر اللي طلعت**،
> مش سطر واحد بس. لو فيه أكتر من `page.waitForFunction()` في نفس الملف، لازم
> **كل واحدة منهم** تحمل `{ timeout: 8 * 60 * 1000 }` صراحة — التأكد من وجود
> النص `8 * 60 * 1000` مرة واحدة في الملف مش كافي، ممكن يكون في نداء تاني
> لـ `waitForFunction` من غير أي `timeout` وبيقع على الـ 30 ثانية الافتراضية
> ويكراش قبل ما يوصل للنداء الصح خالص.

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
  const browser = await chromium.launch({ channel: 'chrome' }); // مطابق للقناة المثبّتة في الـ workflow
  const page = await browser.newPage();

  const consoleLogs = [];
  const failedRequests = [];
  page.on('console', (msg) => consoleLogs.push(`[${msg.type()}] ${msg.text()}`));
  page.on('pageerror', (err) => consoleLogs.push(`[pageerror] ${err.message}`));
  page.on('requestfailed', (req) => failedRequests.push(`${req.url()} — ${req.failure()?.errorText}`));
  page.on('response', (res) => { if (res.status() >= 400) failedRequests.push(`${res.url()} — HTTP ${res.status()}`); });

  // ?autorender=true بيخلي الصفحة تستدعي window.startVideoRender() لوحدها بعد التحميل
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
  console.log(JSON.stringify(result)); // اقرأها من الـ output بتاع run_terminal مباشرة
  process.exit(result.success ? 0 : 1);
})();
```

**استخدم `console_logs`/`failed_requests` مباشرة للتشخيص** — لو ملف صوت أو خط طلع 404
هتلاقيه صريح في `failed_requests`.

**قاعدة سرعة مهمة**: لو الرندر فشل، **صحّح نفس ملف `scene.html` مباشرة وأعد تشغيل
`node render-runner.js`** — ممنوع تكتب ملفات اختبار منفصلة (زي `test_xxx.html`) لتجربة
استيراد أو API، وممنوع منعًا باتًا تنفّذ `node agent.js` بأي شكل (راجع التنبيه الحاسم
أول الملف).

### ملفات العلامة (Marker Files) — إلزامية لتتبع التقدم
- بعد ما ترفع فيديو وملف وصفه فعليًا على الـ Release (بأمر `gh release upload` حقيقي
  ناجح، مش افتراض)، اكتب: `video_<معرّف فريد للفيديو>_done.json` يحتوي
  `{"video_id": "...", "release_video_url": "...", "release_md_url": "..."}`
  (المعرّف الفريد ممكن يكون رقم سورة، اسم حديث، أو أي معرّف يميّز الفيديو ده عن
  غيره حسب نوع المحتوى). **`agent.js` بيتحقق فعليًا** إن اسمَي الملفين في
  الرابطين دول موجودين حقًا كـ assets على الـ Release عن طريق `gh release view`
  قبل ما يقبل ملف العلامة ده — لو مش موجودين هيرفضه برسالة توضح إيه الناقص،
  فتأكد إن الرفع حصل فعلًا الأول.
- بعد انتهاء **كل** الفيديوهات المطلوبة في المهمة، اكتب:
  `TASK_COMPLETE.json` يحتوي `{"summary": "...", "videos": [...]}`

---

## القسم 3: دروس مستفادة من تشغيلات سابقة — تجنّب الأخطاء دي بالتحديد

### مبادئ عامة لحل المشاكل بنفسك — قبل ما تدوّر على حل جاهز
القائمة اللي تحت مش المفروض تكون المصدر الوحيد لحل المشاكل — هي أمثلة تاريخية
بس. هتقابل أخطاء جديدة مش موجودة هنا، خصوصًا مع هويات جديدة بمنطق مختلف تمامًا.
لما يحصل ده، اتبع المبادئ دي بدل ما تفترض إن غياب الخطأ من القائمة يعني إنه
"مش متوقع" أو تلف حواليه:

- **لما أمر يفشل، اقرأ رسالة الخطأ ورقم السطر بالظبط** قبل ما تفترض إنك فاهم
  السبب. `TimeoutError` على `render-runner.js:36` ورقم السطر ده فيه نداء
  `waitForFunction` مختلف عن السطر اللي انت متأكد إنه مظبوط — يبقى فيه نداء
  تاني، مش نفس المشكلة القديمة اللي حليتها قبل كده.
- **لما تتحقق إن إصلاح معيّن اتطبّق، تأكد إنه اتطبّق في كل الحالات المشابهة**
  في نفس الملف، مش حالة واحدة بس.
- **لو بتلاقي نفسك بتعمل نفس التجربة والخطأ (trial-and-error) أكتر من مرتين
  على نفس المشكلة**، وقف وقارن بنسخة معروفة إنها شغالة بدل ما تكمل تخمين.
- **فضّل إعادة كتابة الملف كامل بطريقة نضيفة عن "ترقيع" نسخة انت مش متأكد من
  حالتها بالظبط**.
- **لأي مشكلة تخص Mediabunny تحديدًا** (كودك مرفوض، خطأ ترميز غريب، سلوك غير
  متوقع من `Output`/`CanvasSource`/`Input`)، ارجع لـ
  `docs/mediabunny-llms-full.txt` بالكامل قبل أي تجربة عشوائية — الإجابة
  غالبًا موجودة هناك بالحرف.
- لو خطأ جديد اتحل بطريقة مفيدة عمومًا (مش خاصة بمحتوى الفيديو نفسه)، سجّله
  لنفسك في تفكيرك كدرس للمهام الجاية في نفس الجلسة، حتى لو مش موثّق هنا.

الأخطاء دي حصلت فعليًا في تشغيلات حقيقية سابقة. اتجنبها من البداية، متعملش نفس التجربة والخطأ تاني:

1. **جلب أي أصل صوتي**: استخدم `fetch()` مباشر جوه `scene.html` (مش `curl` منفصل) على
   رابط `https://` دايمًا (مش `http://`) — لينك `http://` أو ريدايركت غير متوقَّع ممكن
   يرجّع صفحة HTML صغيرة (حوالي 166 بايت) باسم الملف الصوتي بدل الصوت الحقيقي. **بعد أي
   `fetch()` للصوت، تحقق إن حجم الـ `ArrayBuffer` بالكيلوبايتات مش بايتات قليلة**
   (`console.log(arrayBuffer.byteLength)`) قبل ما تكمل — لو الحجم صغير غير طبيعي (أقل
   من ~5 كيلوبايت)، الملف على الأغلب صفحة خطأ مش صوت حقيقي، وهتلاقيه واضح كمان في
   `failed_requests` من سكريبت الرندر لو رجع كود HTTP خطأ.
   - **لو الهوية بتستخدم صوت آيات قرآنية آية بآية**: المصدر الافتراضي المُختبر عندنا هو
     `everyayah.com`: `data/{reciter}/{surah:3}{ayah:3}.mp3` (القارئ الافتراضي:
     `Alafasy_128kbps`) — استخدمه إلا لو المهمة طلبت مصدر تاني صراحة.

2. **نص القرآن أو أي نص ديني/معلوماتي تاني** (حديث، تفسير...): **ممنوع منعًا باتًا**
   كتابته من "معرفتك" الداخلية — مصدره لازم يكون نتيجة `curl` فعلية نُفّذت في نفس
   الجلسة على مصدر موثوق. لو المحتوى آيات قرآنية، استخدم مباشرة من أول مرة:
   `https://api.alquran.cloud/v1/surah/{surah}/editions/quran-uthmani,ar.muyassar`
   (بيرجع الرسم العثماني + التفسير الميسر في نفس الطلب). لا داعي لتجربة editions تانية
   زي `quran-simple-clean` أولًا، ده مضيعة وقت وبيرجع بيانات ناقصة أحيانًا.
   **شكل الـ JSON الراجع بالظبط** (لتفادي `TypeError` بسبب افتراض شكل غلط):
   `response.data` هو **List فيه عنصرين** (مش Object فيه مفتاح `editions`) —
   `data[0]` هو edition الرسم العثماني و`data[1]` هو edition التفسير الميسر
   (بنفس ترتيب الطلب)، وكل واحد فيهم فيه `ayahs` (List). يعني الوصول الصح هو
   `data[0]['ayahs']` و`data[1]['ayahs']`، مش `data['editions'][i]['ayahs']`.

3. **متغيرات البيئة `$RELEASE_TAG` و`$GH_REPO`**: متاحين فعليًا الآن في أي أمر
   `run_terminal` (تم إصلاح باگ سابق كانوا فيه فاضيين). لو لأي سبب طلعوا فاضيين برضه،
   استخدم `gh repo view` و`gh release list --repo <name>` لمعرفة القيم الصحيحة يدويًا
   كخطة بديلة، بدل ما توقف.

4. **تحقق دايمًا من أي ملف نزّلته قبل ما تفترض إنه صح** — سواء صوت أو JSON أو صورة أو
   فيديو — بأمر بسيط زي `ls -la` أو `head -c 200 <file>`. عادة أرخص بكتير من اكتشاف
   المشكلة بعد خطوات كتير.

5. **ممنوع منعًا باتًا تنفيذ `node agent.js` كأمر terminal من جوه جلستك، بأي args**.
   حصل فعليًا مرة إن الـ Agent نفّذه ظنًا إنه أداة رندر جاهزة، فبدأ جلسة Agent كاملة
   تانية من الصفر فوق نفس الريبو، اختارت محتوى عشوائي مختلف، وملفات العلامة اللي
   كتبتها خدعت الجلسة الأصلية إنها هي اللي خلصت — والفيديو الأصلي المطلوب اتلغى
   بصمت. الرندر دايمًا سكريبت منفصل تكتبه إنت (`render-runner.js` مثلًا) وتشغّله بـ
   `node render-runner.js`.

6. **فحص النص العربي في `scene.html` بيقبل حالتين بس** (لو الهوية فيها نص عربي أصلًا):
   إما متتالية 10 حروف عربية متصلة بدون تاجات HTML بينها، أو إجمالي 40 حرف عربي على
   الأقل في كل الملف (بعد تجاهل التاجات). لو فشل الفحص، الرسالة بترجعلك بالظبط طول
   أطول متتالية وإجمالي العدد الحاليين — استخدمهم للتشخيص بدل التخمين العشوائي لسبب
   الفشل. (هوية بصرية بحتة من غير أي نص، زي B-roll خالص، الفحص ده ملوش معنى ليها
   أصلًا.)

7. **`scene.html` لازم يتفتح دايمًا عن طريق سيرفر HTTP محلي، مش `file://`** — حتى لو
   كل الأصول بتتجاب بـ `fetch()` مباشر من روابط خارجية (مفيش تحميل محلي للأصول
   أصلًا). فتح الملف بـ `file://` بيكسر `fetch()`/`import` وبيخلي أي صورة على الـ
   canvas "tainted"، فيفشل الرندر بـ `VideoFrames can't be created from tainted
   sources`. سيرفر بسيط جدًا كفاية — راجع "دليل كتابة سكريبت الرندر" في القسم 2
   (نفس السيرفر البسيط بيفتح الصفحة، مفيش داعي لأي حاجة أعقد).

8. **قناة المتصفح لازم تكون `chrome` صراحة** في `chromium.launch({ channel: 'chrome' })`
   — لأن الـ workflow بيثبّت القناة دي بس (`npx playwright install --with-deps chrome`)،
   مش الـ Chromium الافتراضي.

9. **قبل ما تعتبر المهمة خلصت، راجع تطابق أي أصل صوتي مع النص المكتوب** (لو الهوية
   فيها الاتنين) — لو غيّرت المحتوى، لازم تتأكد إن كل القيم في "منطقة تعديل المحتوى"
   جوه الهوية اتغيّرت مع بعض بشكل متسق (مفيش أسماء متغيرات موحّدة تتفحص تلقائيًا
   دلوقتي — راجع الهوية نفسها لتعرف شكلها). الفيديو ممكن يتصدّر بنجاح كامل من غير أي
   خطأ في اللوج، ومع ذلك يكون فيه نص ومحتوى مختلف عن الصوت المسموع خالص — الرندر مش
   هيكشفلك الغلطة دي لوحده.

10. **ممنوع تحميل أي أصل بـ `curl` حتى للتجربة/التأكد إن الرابط شغال** — استخدم
    `curl -sI` (رأس بس) أو جرّب داخل `fetch()` نفسه وقت التشغيل. تحميل أصل كامل
    محليًا بـ `curl` بيضيع وقت من غير أي فايدة، لأن `scene.html` مش بيقرأ منه أصلًا.

11. **ممنوع منعًا باتًا "تعيد تسمية" ملف الفيديو الناتج بتغيير الامتداد يدويًا**
    (مثلًا `cp video.webm video.mp4`). امتداد الملف اللي بيرجع في
    `window.__renderFilename` بيعكس الحاوية الحقيقية اللي نجح بيها الرندر —
    لو طلع امتداد غير المتوقع، ده معناه إن المحاولة الأساسية فشلت ورجعت لحاوية
    احتياطية، **مش غلطة في التسمية**. ارفع الملف بامتداده الحقيقي زي ما هو،
    ولو الهدف حاوية معيّنة تحديدًا، حل السبب اللي خلّى المحاولة الأساسية تفشل
    (راجع اللوج/الكونسول) بدل ما تلف حواليها بإعادة تسمية الملف.

12. **لما تكتب أمر `node -e "..."` بعلامات تنصيص مزدوجة وجواه JS template
    literals فيها `${...}`**، الـ shell (bash) بيحاول يفسّر `${...}` دي كمتغيرات
    bash قبل ما توصل لـ node أصلًا، وبيدّي أخطاء زي `bad substitution`. استخدم
    علامات تنصيص مفردة `'...'` حوالين كود الـ JS كله (زي `node -e '...'`)، أو
    اكتب الكود في ملف `.js` منفصل بـ heredoc وشغّله بـ `node file.js` بدل
    `node -e`.

---

## القسم 4: قواعد صارمة — غير قابلة للتفاوض
1. **ممنوع منعًا باتًا** كتابة أي نص ديني/معلوماتي (آية، حديث، تفسير، أو أي نص هوية
   محتاجاه) من "معرفتك" الداخلية. كل نص في أي `scene.html` لازم مصدره نتيجة `curl`
   فعلية نُفّذت في نفس الجلسة على مصدر موثوق (مثل `api.alquran.cloud` للآيات).
2. **لو الفيديو فيه صوت**: كل توقيت (متى يظهر كل مشهد) يُحسب من **المدة الفعلية**
   لهذا الصوت بعد تحميله وفكّه (`AudioBuffer.duration`)، وليس تخمينًا — القاعدة دي
   مُفعّلة تلقائيًا في الملحق التقني (`CONFIG.duration = audioBuffer.duration`). لو
   الفيديو مالوش صوت (B-roll بصري بحت مثلًا)، إنت المسؤول عن تحديد `CONFIG.duration`
   بنفسك في الهوية بناءً على مصدر واضح (مدة فيديو B-roll المستخدم، أو تصميم مقصود).
3. **ملف وصف الفيديو الناتج** (اللي بيترفع مع كل فيديو على الـ Release — مختلف
   تمامًا عن ملف هوية `.md` في `identities/`) يجب أن يحتوي: نوع المحتوى ومعرّفه
   (اسم السورة/الحديث/إلخ)، مصدر القارئ/الصوت لو موجود، المدة الكلية، وصف مختصر
   لمكونات المحتوى (نص؟ تفسير؟ B-roll؟)، ورابط الـ GitHub Release الفعلي بعد الرفع.

---

## القسم 5: ملاحظات خاصة بكل ملف هوية — دلوقتي جوه ملف الهوية نفسه

من دلوقتي كل ملف `identities/<اسم>.md` بيحتوي على قسم "ملاحظات معروفة" خاص بيه
في آخره (البند 8 في "عقد ملف هوية .md" بالقسم 2) — بدل ما الملاحظات دي تتوزع
هنا بعيد عن الكود اللي بتتكلم عنه. **قبل ما تستخدم أي هوية، افتح ملفها وشوف
القسم ده جواه.** لو ملف هوية جديد ملوش قسم "ملاحظات معروفة" لسه، معنى كده مفيش
ملاحظات معروفة عليه — كمّل عادي بالقواعد العامة في الأقسام اللي فوق بس.

لو مشكلة جديدة اتحلت وهي مرتبطة بتصميم/محتوى/أصول هوية معيّنة بالذات (مش
بالملحق التقني الثابت أو بـMediabunny نفسها، اللي مكانهم القسم 3)، ضيفها في قسم
"ملاحظات معروفة" بتاع ملف الهوية نفسه، مش هنا.
