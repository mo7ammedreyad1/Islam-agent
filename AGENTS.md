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

---

## القسم 1: هوية الـ Agent

الـ Agent مسؤول عن إنتاج فيديوهات قرآنية (تلاوة، وأحيانًا مع تفسير مبسّط) بشكل
كامل من الصفر: جلب النص والصوت من مصادر موثوقة، كتابة `scene.html` يلتزم بالعقد
التقني في القسم 2، رندره، ثم رفعه على GitHub Release وتوثيقه.

### هوية الفيديو — قسمين منفصلين

**1. الإحساس والمحتوى** (نوع الفيديو، هل فيه تفسير ولا لأ، الطابع العام) موصوف في
ملفات `.md` جوه [`video-identities/`](./video-identities/)، كل ملف يوصف نوع فيديو
معيّن بالكلام.

**2. الشكل البصري والتقني** (الخطوط، الألوان، التخطيط، منطق الرسم على الـ canvas)
موصوف في ملفات `.md` جوه [`identities/`](./identities/) — **مواصفة تفصيلية
قابلة للنسخ الحرفي (فيها كود JS/CSS فعلي، مش وصف كلامي عام)، لكن بدون أي جزء من
منطق التصدير/الرندر/جلب الأصول**، لأن ده كله ثابت وموجود مرة واحدة بس في "الطبقة
التقنية الثابتة" بالقسم 2. **المجلد ده فيه — وهيفضل يتزود فيه مع الوقت — أكتر من
ملف هوية بصرية مختلفة (استايلات متعددة)، كل واحد بستايله الخاص.** مفيش هوية
"افتراضية" أو "أساسية" بينهم — كل ملف فيه مرجع حرفي مستقل بذاته.

- **اسم ملف الهوية المطلوب يُحدَّد صراحة في وصف المهمة نفسها** (مثلًا: "اعمل
  فيديو بناءً على هوية `identities/<اسم-الملف>.md`"). افتح الملف المذكور
  بالحرف قبل ما تكتب أي حرف في `scene.html` — **ميصحش تفترض أو "تتذكر" هوية
  استخدمتها قبل كده لو المهمة الحالية سمّت ملف تاني أو محددتش اسم**؛ في الحالة
  التانية دي ارجع للمستخدم واسأله أي ملف يستخدم.
- ملفات `video-identities/*.md` وصف بالكلام — التزم بروحه، مش نسخ حرفي.

### `scene.html` بيتبني، مش بينُسخ — 3 خطوات بالترتيب
راجع "نظرة عامة: كل `scene.html` = 3 طبقات" في القسم 2 قبل أول مهمة تعملها. عمليًا:

1. **افهم طلب المستخدم** وحوّله لبنود واضحة (السورة، مع/من غير تفسير، أي تخصيص
   شكلي مطلوب صراحة...) — وحدد أي ملف هوية في `identities/` مطلوب استخدامه.
2. **اقرأ ملف الهوية المطلوب بالكامل** — مش قراءة سريعة، اقرأه أكتر من مرة لو
   محتاج (مرة عامة تفهم بيها البنية، ومرة تانية مركّزة على أي نقطة طلبها
   المستخدم صراحة تخالف الافتراضي). لاحظ خصوصًا: أبعاد الكانفاس، الدوال
   الإلزامية (`buildParsedScenes`, `drawSceneAtTime`)، وأي حقول إضافية مطلوبة
   في `SURAH_VERSES` (زي `tafseer`) موثّقة في وصف الملف.
3. **اكتب `scene.html` من الصفر** بدمج الطبقات التلاتة بالترتيب في القسم 2:
   طبقة محتوى المهمة (تكتبها إنت، مصدرها نتيجة `curl` فعلية — راجع القسم 4)،
   ثم كود تصميم الهوية (**منسوخ حرفيًا** من ملف الـ `.md`، مش معاد صياغته أو
   "تحسينه")، ثم الطبقة التقنية الثابتة (**منسوخة حرفيًا** من القسم 2، بدون
   أي تعديل).

**فرّق بين حاجتين واضح طول الوقت:**
- **العقد التقني الإلزامي — مايتفاوضش فيه أبدًا، أيًا كانت الهوية**: كل حاجة في
  "الطبقة التقنية الثابتة" بالقسم 2 (Mediabunny، الـ render hooks، جلب الأصول،
  التصدير). أي مخالفة ليه = فشل الرندر.
- **تفاصيل الهوية البصرية نفسها** (الألوان، الخطوط، تفاصيل التخطيط، الحركات،
  وجود تفسير من عدمه...): دي جزء من "الستايل"، ومسموح تعدّل فيها **بناءً على
  طلب صريح من المستخدم في المهمة** (مثلًا "بنفس هوية X بس خلي الخلفية أغمق
  شوية"). الهدف إنك تكون مرن مع طلب المستخدم فوق أساس الهوية، مش إنك تنسخها
  100% حرفيًا في كل تفصيلة صغيرة من غير أي وعي بالسياق، ومش إنك تغيّر فيها من
  عندك من غير ما المستخدم يطلب.
- **المحتوى الخاص بكل فيديو** (نص الآيات، التفسير لو موجود، اسم السورة، القارئ،
  رقم السورة في رابط الصوت، اسم ملف الإخراج) دايمًا بتكتبه من الصفر لكل مهمة في
  طبقة محتوى المهمة — ده مش جزء من ملف الهوية أصلًا، ومفيش فيه حاجة "تتنسخ".
- **قبل ما تبدأ، شوف كمان لو فيه قسم "ملاحظات معروفة" جوه ملف الهوية نفسه
  (آخره عادةً)** — لو موجود، فيه ملاحظات وتنبيهات خاصة بالتصميم ده بالذات، مش
  عامة لكل الهويات.

### تحقق إلزامي بعد كتابة `scene.html`، قبل ما تكمّل للرندر
- **اطبع القيم الفعلية لمتغيرات طبقة محتوى المهمة** (`SURAH_NUMBER`,
  `RECITER_ID`, `SURAH_DISPLAY_NAME`, `RECITER_DISPLAY_NAME`) **من الملف
  الناتج فعليًا** (`grep`/`cat` على `scene.html` نفسه، مش من الكود اللي كتبته
  في رأسك) وقارنها بالسورة/المحتوى المطلوب فعليًا في المهمة.
- **تأكد إن الدالتين الإلزاميتين (`buildParsedScenes`, `drawSceneAtTime`)
  موجودتين فعليًا في `scene.html`** (`grep -n "function buildParsedScenes\|function drawSceneAtTime" scene.html`)
  — نسيان نسخهم من ملف الهوية بيدّي خطأ `is not defined` وقت التشغيل، مش فشل
  صامت، فهيظهر واضح في `console_logs`.
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
  `scene.html` الأول. الهدف إنك تكتشف أخطاء الـ syntax في ثواني بدل ما تكتشفها
  بعد `TimeoutError` غامض بعد دقايق من انتظار الرندر الكامل.

---

## القسم 1.5: استيعاب هوية بصرية من ملف خارجي غير موثّق التوافق

أحيانًا هتوصلك مهمة مرفق معاها ملف `.html` (من المستخدم، أو ناتج من أداة تصميم
تانية، أو من محادثة سابقة) بيوصف "عايز فيديو شكله كده" — **من غير أي ضمان إنه
مكتوب أصلًا لهذا الرانر**. الملف ده ممكن يكون فيه هوية بصرية جميلة وموحية، لكن
لو اتعامل معاه زي أي ملف في `identities/` (نسخ حرفي كامل)، ده بيجيب فشل رندر
مضمون تقريبًا، لأن أغلب الملفات من النوع ده اتكتبت أصلًا كـ "معاينة/تصدير يدوي
في المتصفح" مش كـ "أوتوميشن رانر". **الخطوة دي إلزامية قبل ما تستخدم أي ملف من
النوع ده بأي شكل — سواء كمرجع مباشر أو كإلهام بصري.**

### الخطوة 1: فحص تصنيف صريح — طبّقه بند بند، مش انطباع عام
افتح الملف وابحث فعليًا (مش تخمين من شكله العام) عن كل بند من دول:

1. **الـ render hooks كاملة وشغّالة فعليًا**: `window.renderStatus` بكل قيمه
   (`loading`/`ready`/`rendering`/`completed`/`error`)، `window.renderProgress`،
   دالة اسمها بالظبط `window.startVideoRender()`، دعم `?autorender=true` بيستدعيها
   لوحدها، حدث `video-render-complete` بيتطلق فعليًا، وجود
   `window.__renderFilename` و `window.__renderBase64` (الجسر لسكريبت الـ
   Playwright). غياب **أي واحد** منهم = فشل الفحص.
2. **محرك الفيديو Mediabunny حصرًا** (`Output`/`CanvasSource`/`AudioBufferSource`
   من `'mediabunny'`) — مش `ffmpeg`، مش `MediaRecorder`/`canvas.captureStream`،
   مش أي مكتبة تانية.
3. **polyfill الـ AAC encoder** موجود فعليًا (`registerAacEncoder` +
   `canEncodeAudio('aac')`) — من غيره التصدير هيفشل على الـ CI runner حتى لو
   شغال على جهاز المستخدم اللي عنده encoder أصلي.
4. **مصدر الصوت**: `fetch()` مباشر من `everyayah.com` بنفس باترن الآية-بآية —
   مش موقع تاني، ومش ملف mp3 واحد للسورة كاملة.
5. **التوقيت محسوب من الصوت الحقيقي**: أوقات بداية/نهاية كل آية طالعة فعليًا من
   `AudioBuffer.duration` بعد `decodeAudioData()` — مش أرقام ثواني مكتوبة يدويًا
   بالتخمين في مصفوفة الـ cues.
6. **الخلفية/الصور**: إما مرسومة بالكانفاس بالكامل، أو متجابة بـ
   `crossOrigin="anonymous"` من مصدر بيدعم CORS فعليًا — مش صورة من رابط عشوائي
   (زي Unsplash أو أي CDN عام) من غير تحقق.

### الخطوة 2: الناتج دايمًا ملف هوية `.md` جديد — الفحص بيحدد مستوى الثقة بس
**مهم يتغيّر هنا عن الإصدارات الأقدم من الملف ده**: بما إن `scene.html` بقى
بيتبني من طبقة تقنية ثابتة موجودة مرة واحدة بس في القسم 2 (مش منسوخة من كل ملف
هوية)، فـ **الملف الخارجي — عدّى الفحص ولا لأ — ملهوش أي سيناريو تستخدم فيه
منطقه التنفيذي (render/hooks/جلب أصول/تصدير) حرفيًا أبدًا**. الفحص فوق مش
بيحدد "استخدمه كامل ولا استخرج منه بس"، هو بيحدد **مدى الثقة في الفصل بين
تصميمه وتنفيذه** وقت الاستخراج:

- **عدّى بكل البنود الستة** → غالبًا الملف مبني أصلًا بفصل واضح بين الرسم
  والتنفيذ، فاستخراج كود التصميم منه (دوال الرسم، الألوان، الخطوط، التخطيط)
  غالبًا مباشر وواضح الحدود.
- **فشل في بند واحد أو أكتر (الحالة الشائعة مع ملفات خارجية)** → المنطق
  التنفيذي والتصميمي غالبًا متداخلين في نفس الدوال (مثال شائع: دالة رسم بتاخد
  توقيت مكتوب يدويًا بدل ما تستقبله كباراميتر من طبقة محسوبة فعليًا) — خد وقتك
  أكتر في الفصل، ومتفترضش إن أي دالة "شكلها رسم" خالية من قرارات تنفيذية
  متسرّبة جواها (زي أرقام توقيت ثابتة، أو رابط مصدر مكتوب داخل دالة الرسم
  نفسها).
- **في الحالتين**: يُستخرج الستايل البصري بس (يتوثّق بوضوح لنفسك قبل ما تكتب
  كود إيه بالظبط المأخوذ: أسماء الخطوط وأوزانها، لوحة الألوان، أبعاد الكانفاس
  ونسبته، خوارزمية توزيع النص لو مختلفة عن `layoutArabicParagraph` العامة
  الموجودة في الطبقة التقنية، فكرة الحركة، وشكل عناصر الواجهة لو له طابع مميز)،
  ويُبنى منه ملف **`identities/<اسم-جديد>.md` جديد ومستقل**، بنفس الصيغة
  الموصوفة في "عقد ملف هوية `.md`" بالقسم 2 — **بدون أي جزء من منطق
  render/hooks/جلب الأصول/التصدير**، لأن دي كلها بتيجي من الطبقة التقنية
  الثابتة في القسم 2 بس، مهما كان الملف الأصلي شايلها أو لأ.
  - لا تعديل مباشر على الملف الخارجي غير الموثوق، ولا استبدال لملف قائم في
    `identities/`.
  - لو المهمة بس عايزة فيديو واحد بالستايل ده (من غير إضافة دائمة للمجلد)،
    اكتب كود التصميم المستخرج مباشرة في طبقة تصميم الهوية جوه `scene.html`
    (الخطوة 3 في "منهجية بناء `scene.html`" بالقسم 1) من غير ما تضيف ملف هوية
    جديد، إلا لو المستخدم طلب صراحة إضافته كهوية دائمة.
- **حالة غير واضحة (بعض البنود مش أكيدة)** → افتراضيًا عامل الاستخراج كأنه فشل
  الفحص (الخيار الأكثر حذرًا في الفصل بين التصميم والتنفيذ)، مش تفترض ثقة أعلى
  مما تستحق.

---

## القسم 2: العقد التقني الإلزامي — **مهم جدًا، مخالفته = فشل الرندر بالكامل**

### نظرة عامة: كل `scene.html` = 3 طبقات مدموجة جوه `<script type="module">` واحد
من دلوقتي، `scene.html` **مش بيتنسخ من ملف واحد جاهز** — إنت (الـ Agent) بتجمّعه
بنفسك من 3 طبقات مصدرها مختلف، بالترتيب ده بالظبط:

1. **📋 محتوى المهمة (Task Content)** — إنت بتكتبها من الصفر لكل فيديو (السورة،
   القارئ، نص الآيات...). مصدرها: طلب المستخدم + `curl` فعلي على مصدر موثوق.
2. **🎨 تصميم الهوية (Identity Design)** — بتُنسخ **حرفيًا** (كود، مش وصف) من ملف
   `identities/<اسم-الهوية>.md` اللي المهمة حددته. فيها الألوان، الخطوط، الـ CSS،
   ومنطق الرسم/الحركة على الـ canvas.
3. **⚙️ الطبقة التقنية الثابتة (Fixed Technical Layer)** — **موجودة كاملة تحت في
   القسم ده، ثابتة حرفيًا في كل `scene.html` تكتبه أيًا كانت الهوية أو المهمة.**
   فيها Mediabunny، الـ render hooks، جلب الصوت، وحلقة التصدير. **تُنسخ زي ما هي
   من غير أي تعديل** — دي بالظبط النقطة اللي بتضمن إن أي هوية جديدة (حتى لو
   منطقها في الرسم مختلف تمامًا) هتشتغل مع الرانر من غير مفاجآت.

**قاعدة الدمج**: JS كله (الطبقات التلاتة) بيتحط في `<script type="module">` واحد
بالترتيب فوق (محتوى ← تصميم ← تقني). ترتيب التعريفات مش بيأثر على التشغيل فعليًا
(الدوال بتتنفذ بعد ما الملف كله يتحمّل)، لكن الترتيب ده بيخلي الملف مقروء ومنظّم.

---

### 🎨 عقد ملف هوية `.md` — إيه اللي لازم يوفّره لطبقة التصميم
ملف `identities/<اسم>.md` هو **مواصفة كاملة قابلة للنسخ الحرفي**، مش وصف كلامي
عام. **ميحتويش على أي حاجة من الطبقة التقنية تحت (مفيش Mediabunny، مفيش hooks،
مفيش جلب صوت، مفيش تصدير)** — دول موجودين مرة واحدة بس في الطبقة التقنية الثابتة،
وتكرارهم جوه ملف الهوية خطأ في حد ذاته (تكرار كود بيصعّب الصيانة، ولو حصل تعارض
بينه وبين النسخة الثابتة هيبقى مصدر باگ غامض).

كل ملف هوية `.md` لازم يحتوي على الأقسام دي بالترتيب:

1. **فقرة وصف قصيرة** (الاسم، الطابع البصري/الروح العامة، الأبعاد ونسبتها،
   حالة الاستخدام المناسبة ليها).
2. **روابط الخطوط** (`<link>` كاملة لـ Google Fonts أو أي مصدر خطوط).
3. **كتلة CSS كاملة** — تستهدف الـ IDs/classes الثابتة دي بالظبط (موجودة في هيكل
   الـ HTML الثابت تحت): `body`, `#viewport`, `canvas` (أو `#shortsCanvas`),
   `#hud`, `.spinner` + `@keyframes spin`, `#status-text`, `#controls-overlay`,
   `.btn`, `.btn-preview`, `.btn-render`, `#console-modal`, `#console-header`,
   `#console-output`, `.log-line`, `.log-info`, `.log-warn`, `.log-error`. لازم
   تبدأ بـ `* { box-sizing: border-box; margin: 0; padding: 0; }`.
4. **أبعاد الكانفاس** (`width`/`height` اللي هتتحط في `<canvas>` في الـ HTML).
5. **كود JS تصميم الهوية** — الكتلة دي بالظبط، بنفس الترتيب:
   - `CONFIG` (`{ fps, width, height, duration: <أي قيمة placeholder> }` —
     الـ `duration` هتتكتب فوق تلقائيًا من الطبقة التقنية لما الصوت يتحمّل).
   - `PALETTE` (كل الألوان المستخدمة، بأسماء واضحة).
   - ثوابت الخطوط (زي `QURAN_FONT`, `HEADER_FONT`) وأي ثوابت تصميم تانية
     (زي مركز Y للنص، أو نسب الـ stagger في الحركة).
   - أي دوال رسم مساعدة داخلية تحتاجها الهوية (خلفية، هيدر، بطاقات، عدادات...).
   - **دالتين إلزاميتين لازم يتعرّفوا في الآخر** (العقد بين طبقة التصميم والطبقة
     التقنية بيقوم عليهم بس):
     - `function buildParsedScenes()`: بتتنادى مرة واحدة بعد ما الصوت يتحمّل.
       بتاخد `RAW_CUES` (متاحة من الطبقة التقنية، فيها `{id, start, end, text,
       ...}` — أي حقول إضافية زي `tafseer` أو `surah` كتبتها في `SURAH_VERSES`
       بتوصل هنا زي ما هي) وتحوّلها لـ `parsedScenes` (متاحة برضه من الطبقة
       التقنية) بعد حساب الخط والتخطيط.
     - `function drawSceneAtTime(time)`: **نقطة الدخول الوحيدة اللي الطبقة
       التقنية بتناديها كل فريم**، سواء في المعاينة الحية أو التصدير الفعلي.
       أول سطر فيها لازم يكون `state.currentTime = time;` (احتياطي لو الصوت
       فشل يتحمّل)، وبعدين ترسم الخلفية والمشهد بناءً على `parsedScenes`.
6. **أدوات جاهزة من الطبقة التقنية، متاحة تلقائيًا لكود الهوية من غير استيراد أو
   إعادة تعريف** (لو عرّفتها تاني جوه ملف الهوية، ده تكرار غير مطلوب):
   - `layoutArabicParagraph(text, font, maxWidth, wordGap, lineHeight, centerY)`
     — خوارزمية تخطيط نص عربي RTL عامة. لو الهوية محتاجة خوارزمية تخطيط مختلفة
     تمامًا، عرّف دالة بديلة باسم مختلف جوه كود الهوية نفسه.
   - `Easing.linear / .easeOutCubic / .easeInOutCubic`.
   - `clamp01(val)`, `toArabicDigits(input)`.
   - `ctx` (الـ 2D context)، و `CONFIG.width`/`CONFIG.height` (بعد ما تتعرّف).
7. **متغيرات محتوى المهمة المتاحة من الطبقة الأولى** (اتفق عليها كأسماء موحّدة
   لكل الهويات، عشان أي هوية تقدر تستخدم أي منها من غير ما تخترع اسم جديد):
   `SURAH_NUMBER`, `RECITER_ID`, `RECITER_DISPLAY_NAME`, `SURAH_DISPLAY_NAME`,
   `OUTPUT_FILENAME`, `SURAH_VERSES`. لو الهوية محتاجة حقل إضافي في كل عنصر من
   `SURAH_VERSES` (زي `tafseer`)، لازم يتوثّق صراحة في فقرة الوصف (البند 1)
   عشان أي حد يكتب محتوى المهمة يعرف يوفّره.
8. **ملاحظات/تنبيهات خاصة بالهوية دي بس** (لو فيه)، زي قسم "القسم 5" القديم —
   دلوقتي بقت جوه ملف الهوية نفسه بدل ما تتوزع في `AGENTS.md`.

---

### 📋 طبقة محتوى المهمة — قواعدها
تُكتب مباشرة في أول `<script type="module">` جوه `scene.html`، **من غير أي جزء
منها في ملف هوية `.md`**:

```js
const SURAH_NUMBER = '112';                 // رقم السورة، 3 أرقام، مستخدم في رابط الصوت
const RECITER_ID = 'Alafasy_128kbps';       // مجلد القارئ في everyayah.com
const RECITER_DISPLAY_NAME = 'تلاوة الشيخ مشاري راشد العفاسي';
const SURAH_DISPLAY_NAME = 'سُورَةُ الْإِخْلَاصِ';
const OUTPUT_FILENAME = 'Quran_Shorts_Al_Ikhlas';
const SURAH_VERSES = [
    { text: '...' /* + أي حقول إضافية موثّقة في ملف الهوية، زي tafseer */ },
    // عنصر واحد لكل آية، بنفس ترتيبها الحقيقي
];
```

- كل قيمة هنا نص القرآن، اسم القارئ، رقم السورة، وأي تفسير لازم مصدرها **نتيجة
  `curl` فعلية على `api.alquran.cloud`** (تفاصيل الرابط والشكل بالظبط في القسم 4
  والقسم 3) — **ممنوع منعًا باتًا تُكتب من الذاكرة**.
- `RECITER_DISPLAY_NAME` و `SURAH_DISPLAY_NAME` نصوص عرض بس (تظهر في الفيديو لو
  الهوية بترسم هيدر) — لازم تتطابق فعليًا مع `RECITER_ID`/`SURAH_NUMBER`، وإلا
  هيظهر اسم قارئ أو سورة غلط في الفيديو حتى لو الصوت والنص صح (راجع القسم 3).
- طول مصفوفة `SURAH_VERSES` لازم يطابق عدد آيات السورة الحقيقي — الطبقة التقنية
  بتلف على `SURAH_VERSES.length` تلقائيًا لجلب نفس العدد من ملفات الصوت.

---

### ⚙️ الطبقة التقنية الثابتة — الهيكل الكامل، انسخه حرفيًا زي ما هو
ده الجزء اللي **ثابت 100%** في كل `scene.html`. أي تعديل فيه (حتى لو شكله بسيط
أو "تحسين") يعتبر مخالفة للعقد التقني.

**هيكل الـ `<head>`/`<body>` الثابت** (الأماكن المعلّمة بـ 🎨 بتتملى من ملف الهوية):

```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Video Clip — القرآن الكريم (مع دعم Agent Auto-Render)</title>

    <!-- 🎨 IDENTITY: روابط الخطوط (من البند 2 في ملف identities/<اسم>.md) -->

    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/regular/style.css" />
    <link rel="stylesheet" type="text/css" href="https://unpkg.com/@phosphor-icons/web@2.1.1/src/fill/style.css" />

    <!-- 🎨 IDENTITY: كتلة الـ CSS كاملة (من البند 3 في ملف identities/<اسم>.md) -->
    <style>
    </style>
</head>
<body>
    <div id="viewport">
        <!-- 🎨 IDENTITY: width/height من البند 4 في ملف الهوية -->
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
        import { Output, Mp4OutputFormat, WebMOutputFormat, BufferTarget, CanvasSource, AudioBufferSource, QUALITY_HIGH, canEncodeAudio } from 'mediabunny';

        // ============ 📋 محتوى المهمة — هنا (راجع القسم فوق) ============

        // ============ 🎨 تصميم الهوية — هنا (منسوخ من ملف identities/<اسم>.md) ============

        // ============ ⚙️ الطبقة التقنية الثابتة — من هنا تحت، تُنسخ حرفيًا ============
    </script>
</body>
</html>
```

**كود الطبقة التقنية الثابتة بالكامل** (يحل محل التعليق الأخير فوق):

```js
const canvas = document.getElementById('shortsCanvas');
const ctx = canvas.getContext('2d', { willReadFrequently: true });
const statusText = document.getElementById('status-text');
const spinner = document.getElementById('spinner');

let audioBuffer = null;
let audioAudioEl = null;
let parsedScenes = [];
let RAW_CUES = [];
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

// --- جلب الصوت آية بآية من everyayah.com + حساب التوقيت من المدة الحقيقية ---
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

async function preloadEveryAyahQuranAudio() {
    logToConsole(`جاري تحميل صوت آيات ${SURAH_DISPLAY_NAME} آية بآية من EveryAyah.com (${RECITER_DISPLAY_NAME})...`);
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    const ayahBuffers = [];
    let totalSamples = 0;

    for (let i = 1; i <= SURAH_VERSES.length; i++) {
        const ayahNum = String(i).padStart(3, '0');
        const url = `https://www.everyayah.com/data/${RECITER_ID}/${SURAH_NUMBER}${ayahNum}.mp3`;
        try {
            const res = await fetch(url);
            const arrayBuf = await res.arrayBuffer();
            const decodedBuf = await audioCtx.decodeAudioData(arrayBuf);
            ayahBuffers.push(decodedBuf);
            totalSamples += decodedBuf.length;
            logToConsole(`تم تحميل الآية ${i} بنجاح ✓`);
        } catch (err) {
            logToConsole(`تنبيه تحميل الآية ${i}: ${err.message}`, 'warn');
        }
    }

    if (ayahBuffers.length === 0) throw new Error("تعذر جلب ملفات الصوت من EveryAyah");

    const sampleRate = ayahBuffers[0].sampleRate;
    const channelsCount = ayahBuffers[0].numberOfChannels;
    audioBuffer = audioCtx.createBuffer(channelsCount, totalSamples, sampleRate);

    let sampleOffset = 0;
    let timeOffset = 0.0;
    RAW_CUES = [];

    for (let i = 0; i < ayahBuffers.length; i++) {
        const buf = ayahBuffers[i];
        for (let ch = 0; ch < channelsCount; ch++) {
            audioBuffer.getChannelData(ch).set(buf.getChannelData(ch), sampleOffset);
        }

        const duration = buf.duration;
        RAW_CUES.push({
            id: i + 1,
            start: timeOffset,
            end: timeOffset + duration,
            ...SURAH_VERSES[i]   // بينقل text وأي حقول إضافية (زي tafseer, surah) زي ما هي
        });

        sampleOffset += buf.length;
        timeOffset += duration;
    }

    CONFIG.duration = audioBuffer.duration;

    const wavBlob = audioBufferToWavBlob(audioBuffer);
    const blobUrl = URL.createObjectURL(wavBlob);
    audioAudioEl = new Audio(blobUrl);

    logToConsole(`تم دمج تلاوة الآيات بنجاح! مدة الشورتس: ${CONFIG.duration.toFixed(2)} ثانية ✓`);
}

// --- حلقة المعاينة الحية — بتنادي drawSceneAtTime بس، من غير أي منطق تصميم ---
function startPreviewLoop() {
    if (state.animationFrameId) cancelAnimationFrame(state.animationFrameId);
    if (audioAudioEl) {
        audioAudioEl.currentTime = 0;
        audioAudioEl.play().catch(e => logToConsole("تنبيه الصوت: " + e.message, 'warn'));
    }

    function loop() {
        if (state.isRendering) return;
        const currTime = audioAudioEl ? audioAudioEl.currentTime : state.currentTime;
        drawSceneAtTime(currTime);

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
        drawSceneAtTime(timestamp);
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
    logToConsole(`بدء عملية التصدير لمكافئ MP4 (${CONFIG.width}x${CONFIG.height}) + صوت ${SURAH_DISPLAY_NAME}...`);

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
            // لأي سكريبت خارجي يغيّره لاحقًا (راجع القسم 3، الدرس رقم 11).
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
        await preloadEveryAyahQuranAudio();
        buildParsedScenes(); // من طبقة تصميم الهوية
        statusText.textContent = "جاهز للعرض والتصدير ✓";
        spinner.style.display = 'none';
        window.renderStatus = 'ready';
        drawSceneAtTime(0); // من طبقة تصميم الهوية

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

### الأصول (صوت/صورة): fetch مباشر بالرابط جوه المتصفح — من غير تحميل محلي بـ curl
**ممنوع تحميل أي أصل (صوت أو صورة) بـ `curl` جوه `run_terminal` وحفظه في `assets/`
والإشارة له بمسار محلي — القاعدة دي بلا استثناء، حتى لو غرضك تجربة سريعة أو
"تأكد إن الرابط شغال" قبل ما تكتب الكود.** استخدم `curl -sI` (رأس الطلب بس، من
غير تحميل الملف كامل) لو عايز تتأكد إن رابط موجود، أو جرّب الـ `fetch()` نفسه
جوه المتصفح وقت التشغيل الفعلي. أي ملف صوت/صورة اتحمّل محليًا بـ `curl` في
مجلد المهمة يعتبر خطأ في سير العمل حتى لو الفيديو النهائي طلع صح، لأنه بيضيع
وقت وبيوّه لمصدر مش هو اللي فعليًا هيتقرأ وقت الرندر.

- **الصوت**: `preloadEveryAyahQuranAudio()` في الطبقة التقنية فوق بتعمل ده
  تلقائيًا — `fetch()` مباشر من `everyayah.com` آية بآية، من غير أي خطوة تحميل
  مسبقة على القرص. مفيش داعي تكتبها تاني.
- **الصورة/الخلفية**: مش كل الهويات محتاجة صورة خارجية — كتير من الهويات بترسم
  الخلفية بالكامل بتدرجات الـ canvas من غير أي صورة خارجية أصلًا (راجع كود
  الهوية نفسها في ملف الـ `.md` بتاعها). لو الهوية فعلًا محتاجة صورة خارجية،
  اجلبها بـ `<img crossOrigin="anonymous">` أو `fetch()` من رابط مباشر بيدعم
  CORS — من غير أي API key سري. لو المصدر مش بيدعم CORS، ارجع لخلفية مرسومة
  بالكانفاس بدل ما تحاول تحمّلها.
- **قاعدة تجنّب "canvas tainted"**: لازم `scene.html` يتفتح دايمًا عن طريق سيرفر
  HTTP محلي (زي اللي في وصفة الرندر تحت)، **مش** بمسار `file://` مباشر. أي صورة
  بتترسم على الـ canvas، حتى لو من مصدر خارجي، لازم تتحمّل بـ
  `crossOrigin = 'anonymous'` وإلا خرق القاعدة دي بيدّي خطأ `VideoFrames can't be
  created from tainted sources`. الصوت مالوش المشكلة دي أصلاً لأنه مش بيترسم على
  الـ canvas. طبقة تعتيم (overlay) غامقة فوق أي خلفية حقيقية دايمًا إلزامية عشان
  النص يفضل واضح.

### عقد الـ render hooks الإلزامي — مطابق تمامًا لما هو مكتوب في الطبقة التقنية الثابتة فوق
كل `scene.html` لازم يحتوي فعليًا على الـ hooks دي شغّالة (موجودة كاملة في الكود
فوق، منعًا باتًا تتغيّر أسماؤها):

- `window.renderStatus`: يبدأ `'loading'`، يبقى `'ready'` لما كل الأصول تتجهز،
  `'rendering'` أثناء التصدير، وفي النهاية `'completed'` أو `'error'`.
- `window.renderProgress`: رقم من `0.0` إلى `1.0` بيتحدّث أثناء `'rendering'`.
- `window.startVideoRender()`: `async function` تبدأ التصدير فورًا وترجع `Promise`.
- دعم `?autorender=true` في رابط الصفحة: لو موجود، الصفحة تستدعي
  `window.startVideoRender()` لوحدها بعد التحميل من غير أي تفاعل يدوي (زرار).
- حدث `video-render-complete` يتطلق على الـ `window`
  (`window.dispatchEvent(new CustomEvent('video-render-complete', {...}))`) فور
  اكتمال التصدير بنجاح.
- عند النجاح، النتيجة تتخزن في `window.renderResult` (زي الهوية الأصلية) **بالإضافة**
  لمتغيرين لازمين عشان سكريبت الـ Playwright يقدر ياخد الفيديو فعليًا (الـ `Blob`
  object مينفعش يترجع مباشرة من `page.evaluate`):
```js
  const finalBuffer = output.target.buffer; // ArrayBuffer من Mediabunny (القسم اللي فوق)
  window.__renderFilename = "اسم-الملف.mp4";
  window.__renderBase64 = arrayBufferToBase64(finalBuffer); // دالة base64 قياسية
  window.renderStatus = 'completed';
  window.dispatchEvent(new CustomEvent('video-render-complete', { detail: { filename: window.__renderFilename } }));
```
- عند الفشل (جوه try/catch حوالين كل حاجة):
```js
  window.renderStatus = 'error';
  window.__renderError = err.message;
```

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
أول الملف). كل المعلومات اللي محتاجها موجودة في `console_logs`/`failed_requests` أو
في القسم ده من `AGENTS.md` نفسه.

### ملفات العلامة (Marker Files) — إلزامية لتتبع التقدم
- بعد ما ترفع فيديو وملف وصفه فعليًا على الـ Release (بأمر `gh release upload` حقيقي
  ناجح، مش افتراض)، اكتب: `video_<رقم السورة>_done.json` يحتوي
  `{"surah": <رقم>, "release_video_url": "...", "release_md_url": "..."}`.
  **`agent.js` بيتحقق فعليًا** إن اسمَي الملفين في الرابطين دول موجودين حقًا كـ assets
  على الـ Release عن طريق `gh release view` قبل ما يقبل ملف العلامة ده — لو مش موجودين
  هيرفضه برسالة توضح إيه الناقص، فتأكد إن الرفع حصل فعلًا الأول.
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
  في نفس الملف، مش حالة واحدة بس. `grep` بيرجع "لقيت النص ده" — ده مختلف عن
  "كل مكان المفروض يكون فيه النص ده، فيه فعلًا".
- **لو بتلاقي نفسك بتعمل نفس التجربة والخطأ (trial-and-error) أكتر من مرتين
  على نفس المشكلة**، وقف وقارن بنسخة معروفة إنها شغالة (زي الملف الأصلي قبل أي
  تعديل) بدل ما تكمل تخمين. المقارنة المباشرة أسرع من التخمين المتكرر.
- **فضّل إعادة كتابة الملف كامل بطريقة نضيفة عن "ترقيع" نسخة انت مش متأكد من
  حالتها بالظبط** — خصوصًا لو حصل أكتر من تعديل يدوي على نفس الملف في نفس
  المهمة وبقيت مش متابع كل تغيير حصل فيه.
- لو خطأ جديد اتحل بطريقة مفيدة عمومًا (مش خاصة بمحتوى الفيديو نفسه)، سجّله
  لنفسك في تفكيرك كدرس للمهام الجاية في نفس الجلسة، حتى لو مش موثّق هنا.

الأخطاء دي حصلت فعليًا في تشغيلات حقيقية سابقة. اتجنبها من البداية، متعملش نفس التجربة والخطأ تاني:

1. **جلب الصوت**: استخدم `fetch()` مباشر جوه `scene.html` (مش `curl` منفصل) على رابط
   `https://` من `everyayah.com` دايمًا (مش `http://`) — لينك `http://` أو ريدايركت
   غير متوقَّع ممكن يرجّع صفحة HTML صغيرة (حوالي 166 بايت) باسم `.mp3` بدل الصوت
   الحقيقي. **بعد أي `fetch()` للصوت، تحقق إن حجم الـ `ArrayBuffer` بالكيلوبايتات مش
   بايتات قليلة** (`console.log(arrayBuffer.byteLength)`) قبل ما تكمل — لو الحجم صغير
   غير طبيعي (أقل من ~5 كيلوبايت)، الملف على الأغلب صفحة خطأ مش صوت حقيقي، وهتلاقيه
   واضح كمان في `failed_requests` من سكريبت الرندر لو رجع كود HTTP خطأ.

2. **نص القرآن**: استخدم مباشرة من أول مرة:
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

4. **تحقق دايمًا من أي ملف نزّلته قبل ما تفترض إنه صح** — سواء صوت أو JSON أو صورة —
   بأمر بسيط زي `ls -la` أو `head -c 200 <file>`. عادة أرخص بكتير من اكتشاف المشكلة
   بعد خطوات كتير.

5. **ممنوع منعًا باتًا تنفيذ `node agent.js` كأمر terminal من جوه جلستك، بأي args**.
   حصل فعليًا مرة إن الـ Agent نفّذه ظنًا إنه أداة رندر جاهزة، فبدأ جلسة Agent كاملة
   تانية من الصفر فوق نفس الريبو، اختارت سورة عشوائية مختلفة، وملفات العلامة اللي
   كتبتها خدعت الجلسة الأصلية إنها هي اللي خلصت — والفيديو الأصلي المطلوب اتلغى
   بصمت. الرندر دايمًا سكريبت منفصل تكتبه إنت (`render-runner.js` مثلًا) وتشغّله بـ
   `node render-runner.js`.

6. **فحص النص العربي في `scene.html` بيقبل حالتين بس**: إما متتالية 10 حروف عربية
   متصلة بدون تاجات HTML بينها، أو إجمالي 40 حرف عربي على الأقل في كل الملف (بعد
   تجاهل التاجات). لو فشل الفحص، الرسالة بترجعلك بالظبط طول أطول متتالية وإجمالي
   العدد الحاليين — استخدمهم للتشخيص بدل التخمين العشوائي لسبب الفشل.

7. **`scene.html` لازم يتفتح دايمًا عن طريق سيرفر HTTP محلي، مش `file://`** — حتى لو
   كل الأصول بتتجاب بـ `fetch()` مباشر من روابط خارجية (مفيش تحميل محلي للأصول
   أصلًا). فتح الملف بـ `file://` بيكسر `fetch()`/`import` وبيخلي أي صورة على الـ
   canvas "tainted"، فيفشل الرندر بـ `VideoFrames can't be created from tainted
   sources`. سيرفر بسيط جدًا كفاية — راجع "دليل كتابة سكريبت الرندر" في القسم 2
   (نفس السيرفر البسيط بيفتح الصفحة، مفيش داعي لأي حاجة أعقد).

8. **قناة المتصفح لازم تكون `chrome` صراحة** في `chromium.launch({ channel: 'chrome' })`
   — لأن الـ workflow بيثبّت القناة دي بس (`npx playwright install --with-deps chrome`)،
   مش الـ Chromium الافتراضي.

9. **قبل ما تعتبر المهمة خلصت، سمّع/راجع تطابق الصوت مع النص المكتوب** — لو غيّرت
   المحتوى لسورة جديدة، لازم تتأكد إن `SURAH_NUMBER` و `RECITER_ID` في طبقة
   محتوى المهمة (أول `<script type="module">`) اتغيّروا مع بعض بنفس القيمة، وإن
   `SURAH_DISPLAY_NAME`/`RECITER_DISPLAY_NAME` بيعكسوا نفس السورة والقارئ فعليًا
   (الأسماء دي موحّدة الآن عبر كل الهويات — راجع القسم 2). الفيديو ممكن يتصدّر
   بنجاح كامل من غير أي خطأ في اللوج، ومع ذلك يكون فيه نص وتفسير سورة، وتلاوة
   صوتية سورة تانية خالص — الرندر مش هيكشفلك الغلطة دي لوحده.

10. **ممنوع تحميل أي صوت بـ `curl` حتى للتجربة/التأكد إن الرابط شغال** — استخدم
    `curl -sI` (رأس بس) أو جرّب داخل `fetch()` نفسه وقت التشغيل. تحميل ملف صوت
    كامل محليًا بـ `curl` بيضيع وقت من غير أي فايدة، لأن `scene.html` مش بيقرأ
    منه أصلًا.

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
1. **ممنوع منعًا باتًا** كتابة نص آية أو تفسير من "معرفتك" الداخلية. كل نص عربي في أي
   `scene.html` لازم مصدره نتيجة `curl` فعلية نُفّذت في نفس الجلسة على مصدر موثوق
   (مثل `api.alquran.cloud`).
2. كل توقيت (متى تظهر كل آية، متى تظهر بطاقة التفسير) يُحسب من **المدة الفعلية**
   للصوت بعد تحميله وفكّه (`AudioBuffer.duration`)، وليس تخمينًا.
3. الصوت دائمًا من `everyayah.com`: `data/{reciter}/{surah:3}{ayah:3}.mp3`
   (القارئ الافتراضي: `Alafasy_128kbps`).
4. **ملف وصف الفيديو الناتج** (اللي بيترفع مع كل فيديو على الـ Release — مختلف
   تمامًا عن ملف هوية `.md` في `identities/`) يجب أن يحتوي: اسم السورة، عدد
   الآيات، القارئ، المدة الكلية، هل فيه تفسير أم لا، ورابط الـ GitHub Release
   الفعلي بعد الرفع.

---

## القسم 5: ملاحظات خاصة بكل ملف هوية — دلوقتي جوه ملف الهوية نفسه

من دلوقتي كل ملف `identities/<اسم>.md` بيحتوي على قسم "ملاحظات معروفة" خاص بيه
في آخره (البند 8 في "عقد ملف هوية .md" بالقسم 2) — بدل ما الملاحظات دي تتوزع
هنا بعيد عن الكود اللي بتتكلم عنه. **قبل ما تستخدم أي هوية، افتح ملفها وشوف
القسم ده جواه.** لو ملف هوية جديد ملوش قسم "ملاحظات معروفة" لسه، معنى كده مفيش
ملاحظات معروفة عليه — كمّل عادي بالقواعد العامة في الأقسام اللي فوق بس.

لو مشكلة جديدة اتحلت وهي مرتبطة بتصميم هوية معيّنة بالذات (مش بالطبقة التقنية
الثابتة، اللي مكانها القسم 3)، ضيفها في قسم "ملاحظات معروفة" بتاع ملف الهوية
نفسه، مش هنا.
