# هوية الـ Agent + العقد التقني الإلزامي لفيديوهات الشورتس

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

الـ Agent مسؤول عن إنتاج فيديوهات شورتس بأي محتوى تحدده المهمة (نص، صوت، وأحيانًا
عناصر تفاعلية حسب الهوية المستخدمة) بشكل كامل من الصفر: جلب النص والصوت من مصادر
موثوقة، كتابة `scene.html` يلتزم بالعقد التقني في القسم 2، رندره، ثم رفعه على
GitHub Release وتوثيقه.

### الفلسفة الأساسية: الفهم البرمجي العميق (Core Logic & Full Generation)
لا وجود للنسخ واللصق الأعمى. القواعد الموضحة في هذا العقد وفي ملفات `identities/*.md`
ليست نصوصًا تُجمَّع تلقائيًا، بل **مخطط هندسي (Blueprint)** و**منطق أساسي (Core
Logic)** يجب عليك فهمه واستيعابه جيدًا أولاً. إنت (الـ Agent) اللي بتكتب وتبني ملف
`scene.html` كاملاً بيدك وبوعي هندسي متكامل: تفهم إزاي تتفاعل حلقة التصدير (Render
Loop)، ومحرك الرسم على الـ Canvas، ومعالجة الصوت، وبعدين تكتب الكود بأسلوب نظيف
وعالي الجودة يحقق العقد التقني وظيفيًا بالكامل — لا مجرد لصق نصوص من غير فهم لسياقها.

### هوية الفيديو — ملف واحد شامل جوه identities/*.md
**المرجع الوحيد والحصري لكل هوية هو مجلد [`identities/`](./identities/).** ملف
`identities/<اسم>.md` مش مجرد وصف بصري أو دليل شكل الرسم بس — هو مرجع شامل
ومستقل تمامًا (Self-contained) بيحدد **الإحساس والمحتوى** (نوع الفيديو، هل فيه
شرح أو تعليق إضافي ولا لأ، الطابع العام) **و**الشكل البصري والتقني (الخطوط، الألوان، التخطيط،
منطق الرسم على الـ canvas) مع بعض في نفس الملف — مواصفة تفصيلية فيها كود JS/CSS
فعلي، مش وصف كلامي عام، لكن **بدون أي جزء من منطق التصدير/الرندر/جلب الأصول**،
لأن ده كله ثابت وموجود مرة واحدة بس في "الطبقة التقنية الثابتة" بالقسم 2.
**المجلد ده فيه — وهيفضل يتزود فيه مع الوقت — أكتر من ملف هوية مختلفة (استايلات
متعددة)، كل واحد بستايله وطابع محتواه الخاص.** مفيش هوية "افتراضية" أو "أساسية"
بينهم — كل ملف مرجع مستقل بذاته.

- **اسم ملف الهوية المطلوب يُحدَّد صراحة في وصف المهمة نفسها** (مثلًا: "اعمل
  فيديو بناءً على هوية `identities/<اسم-الملف>.md`"). افهم الملف المذكور
  بالكامل قبل ما تكتب أي حرف في `scene.html` — **ميصحش تفترض أو "تتذكر" هوية
  استخدمتها قبل كده لو المهمة الحالية سمّت ملف تاني أو محددتش اسم**؛ في الحالة
  التانية دي ارجع للمستخدم واسأله أي ملف يستخدم.

### `scene.html` بيتبني بفهم كامل، مش بينُسخ أعمى — خطوتين بالترتيب
راجع "نظرة عامة: كل `scene.html` = طبقتين" في القسم 2 قبل أول مهمة تعملها. عمليًا:

1. **افهم طلب المستخدم** وحوّله لبنود واضحة (موضوع الفيديو، نوع المحتوى، أي
   تخصيص شكلي مطلوب صراحة...) — وحدد أي ملف هوية في `identities/` مطلوب استخدامه.
2. **افهم ملف الهوية المطلوب بالكامل** — مش قراءة سريعة، افهمه أكتر من مرة لو
   محتاج (مرة عامة تستوعب بيها البنية، ومرة تانية مركّزة على أي نقطة طلبها
   المستخدم صراحة تخالف الافتراضي). لاحظ خصوصًا: أبعاد الكانفاس، الدالتين
   الإلزاميتين (`buildParsedScenes`, `drawSceneAtTime`)، وطريقة كتابة بيانات
   المحتوى بقيمها الحرفية المباشرة زي ما موضّح في وصف الملف نفسه (راجع "لا
   متغيرات لبيانات المحتوى" تحت).
3. **اكتب `scene.html` من الصفر بوعي هندسي كامل** بدمج طبقتين بالترتيب في القسم 2:
   طبقة تصميم الهوية (**مبنية بفهم كامل على أساس** ملف الـ `.md` — Blueprint
   يُستوعب أولاً، مش نص يُنسخ ويُلصق من غير فهم — وتشمل القيم الحرفية النهائية
   لمحتوى المهمة مكتوبة مباشرة عند نقطة استخدامها، مصدرها نتيجة `curl` فعلية،
   راجع القسم 3)، ثم الطبقة التقنية الثابتة (**مطابقة وظيفيًا** للمرجع الثابت
   في القسم 2، من غير أي تعديل يُخل بالعقد التقني).

**فرّق بين حاجتين واضح طول الوقت:**
- **العقد التقني الإلزامي — مايتفاوضش فيه أبدًا، أيًا كانت الهوية**: كل حاجة في
  "الطبقة التقنية الثابتة" بالقسم 2 (Mediabunny، الـ render hooks، جلب الأصول،
  التصدير). أي مخالفة ليه = فشل الرندر.
- **تفاصيل الهوية البصرية نفسها** (الألوان، الخطوط، تفاصيل التخطيط، الحركات،
  وجود شرح إضافي من عدمه...): دي جزء من "الستايل"، ومسموح تعدّل فيها **بناءً على
  طلب صريح من المستخدم في المهمة** (مثلًا "بنفس هوية X بس خلي الخلفية أغمق
  شوية"). الهدف إنك تكون مرن مع طلب المستخدم فوق أساس الهوية، مش إنك تنسخها
  100% حرفيًا في كل تفصيلة صغيرة من غير أي وعي بالسياق، ومش إنك تغيّر فيها من
  عندك من غير ما المستخدم يطلب.
- **المحتوى الخاص بكل فيديو** (النصوص، الروابط، أسماء العرض، اسم ملف الإخراج)
  دايمًا بتكتبه من الصفر لكل مهمة **بقيمه الحرفية النهائية مباشرة عند نقطة
  استخدامها** جوه كود الهوية نفسه — مفيش فيه حاجة "تتنسخ" من مكان تاني، ومفيش
  متغيرات وسيطة بتجمّعه من أجزاء (راجع "لا متغيرات لبيانات المحتوى" تحت).
- **قبل ما تبدأ، شوف كمان لو فيه قسم "ملاحظات معروفة" جوه ملف الهوية نفسه
  (آخره عادةً)** — لو موجود، فيه ملاحظات وتنبيهات خاصة بالتصميم ده بالذات، مش
  عامة لكل الهويات.

### لا متغيرات لبيانات المحتوى — قيم حرفية مباشرة عند نقطة الاستخدام
مفيش أي متغيرات وسيطة (`const`/`let`) لتخزين أجزاء من بيانات المحتوى بغرض
تجميعها أو إعادة استخدامها — ولا حتى بأسماء حرة تختارها الهوية. القيمة النهائية
(رابط كامل، نص كامل، اسم عرض كامل) تُكتب **حرفية مباشرة عند نقطة استخدامها
بالظبط**، مش متجمّعة من قطع بمتغيرات منفصلة زي كده:

```js
// ❌ ممنوع — بناء قيمة من متغيرات منفصلة ممكن تتعارض مع بعضها لاحقًا لو
// اتغيّر واحد ونسيت التاني
const NARRATOR_ID = 'narrator-1';
const EPISODE_NUMBER = '007';
const url = `https://example-audio-source.com/${NARRATOR_ID}/${EPISODE_NUMBER}${partNum}.mp3`;
```
```js
// ✅ صح — القيمة النهائية حرفية مباشرة، سطر صريح واحد لكل عنصر، من غير حلقة
// أو متغيرات بتبني الرابط من أجزاء
await fetchAndDecodeAudio('https://example-audio-source.com/narrator-1/007001.mp3');
await fetchAndDecodeAudio('https://example-audio-source.com/narrator-1/007002.mp3');
```

السبب: أي متغيرين منفصلين (زي معرّف تقني واسم عرض) ممكن يتعارضوا مع بعض لو
اتغيّر واحد ونسيت التاني (مثلاً اسم متحدث اتعرض غلط في الفيديو رغم إن الصوت
والنص صح) — لو مفيش متغيرات أصلاً بتوصل قيمتين ببعض، المشكلة دي بتختفي من
جذورها. نفس المبدأ على أي نص عرض بيظهر فعليًا في الفيديو: يُكتب حرفيًا في مكان
رسمه، مش من متغيّر بعيد ممكن يخرج عن التزامن مع مصدره الحقيقي.

**الاستثناء الوحيد**: `OUTPUT_FILENAME` — الطبقة التقنية الثابتة نفسها محتاجاه
كمتغيّر بالاسم ده بالظبط عشان تسمّي الملف الناتج في أكتر من مكان (زر التحميل،
الـ hooks). ده مش "بيانات محتوى" بالمعنى ده، هو واجهة اتفاق تقنية بين الطبقتين،
فيفضل موجود كمتغيّر عادي.

**ثوابت التصميم مش داخلة في القاعدة دي** — `PALETTE`, `CONFIG`, أسماء الخطوط،
وأي ثوابت تخطيط أو حركة، دي جزء من "الستايل" مش "بيانات المحتوى"، وتفضل متغيرات
عادية زي ما هي بلا أي تغيير.

**فحص ذاتي إلزامي قبل ما تعتبر `scene.html` جاهز**: راجع كود الهوية اللي كتبته
بعرضه كامل عبر `cat` (مش `grep`) ودوّر بعينك على أي حاجة من دول — لو لقيت واحدة
منهم، ده خرق للقاعدة ولازم تصلحه فورًا قبل ما تكمّل:
- أي `` ` `` (template literal) بتحط جواه `${متغيّر}` بيتبنى منه رابط أو نص
  عرض — القيمة لازم تكون سترينج عادي حرفي كامل، مش template literal بيجمّع
  أجزاء.
- أي متغيّر بأسماء زي `_ID`, `_NUMBER`, `_KEY` (أو أي اسم تقني مشابه) بيتحط في
  أكتر من مكان واحد في الكود.
- أي `for`/`.map()` بيلف على عدد ثابت وبيبني رابط أو نص مختلف في كل مرة من
  جوه الحلقة نفسها — ده بديل تاني لنفس المشكلة (تجميع قيمة من منطق بدل ما
  تكون حرفية مكتوبة صراحة).

**تحذير مهم يمنع باگ حصل فعليًا في الإنتاج**: `audioBuffer`, `parsedScenes`,
`CONFIG`, `state` **متعرّفين مرة واحدة بس في الطبقة التقنية الثابتة** (بـ `let`
أو `const`). كود الهوية (`buildParsedScenes`) **بيعيّن لهم قيمة بس
(`audioBuffer = buffer;`)، وممنوع يعيد تعريفهم بـ `let`/`const` تانية** — إعادة
التعريف بتسبب إما `SyntaxError: already been declared` (لو الطبقة التقنية
لسه موجودة) أو `ReferenceError: audioBuffer is not defined` (لو الطبقة
التقنية اتنسيت أو اتحطت في الترتيب الغلط). لو قابلت الخطأ ده، الحل مش إنك
تضيف `let audioBuffer = null;` جوه كود الهوية — الحل إنك تتأكد إن الطبقة
التقنية الثابتة (بتعريفها الأصلي زي القسم ده بالظبط) موجودة كاملة في
`scene.html` **بعد** كود الهوية، بدون أي حذف أو نقص.

### تحقق إلزامي بعد كتابة `scene.html`، قبل ما تكمّل للرندر
- **راجع القيم الحرفية المكتوبة فعليًا في `scene.html`** (الروابط، النصوص،
  أسماء العرض) بعرض الملف كاملاً عبر `cat` (ممنوع استخدام `grep`، مش من الكود
  اللي كتبته في رأسك) وقارنها بالمحتوى المطلوب فعليًا في المهمة — تأكد إن كل
  قيمة اتكتبت حرفية عند نقطة استخدامها، مفيش متغيرات وسيطة بتجمّعها من أجزاء.
- **تأكد إن الدالتين الإلزاميتين (`buildParsedScenes`, `drawSceneAtTime`)
  موجودتين فعليًا في `scene.html`** بمراجعة الملف كاملاً عبر `cat` (مش
  `grep`) — نسيان كتابتهم بيدّي خطأ `is not defined` وقت التشغيل، مش فشل
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
لو اتعامل معاه زي أي ملف موثوق في `identities/` (نقل كوده زي ما هو من غير فهم
حقيقي لمنطقه الداخلي)، ده بيجيب فشل رندر
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
4. **مصدر الصوت**: `fetch()` مباشر من رابط `https://` حقيقي بيدعم الجلب المباشر
   — مش موقع وسيط بيحوّل أو يضغط، ولو المحتوى طبيعته مقسّمة لعناصر منفصلة
   (فقرات، مقاطع...) لازم الصوت يتجاب بنفس التقسيم عنصر بعنصر، مش ملف واحد
   طويل للمحتوى كله لو محتاج توقيت دقيق لكل عنصر فيه.
5. **التوقيت محسوب من الصوت الحقيقي**: أوقات بداية/نهاية كل عنصر طالعة فعليًا من
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

### نظرة عامة: كل `scene.html` = طبقتين مدموجتين جوه `<script type="module">` واحد
من دلوقتي، `scene.html` **مش بيتنسخ من ملف واحد جاهز** — إنت (الـ Agent) بتبنيه
بنفسك بفهم كامل من طبقتين مصدرها مختلف، بالترتيب ده بالظبط:

1. **🎨 تصميم الهوية (Identity Design)** — **مبنية بفهم كامل على أساس** ملف
   `identities/<اسم-الهوية>.md` اللي المهمة حددته (Blueprint هندسي يُستوعب أولاً،
   لا نص يُنسخ ويُلصق من غير فهم). فيها الألوان، الخطوط، الـ CSS، ومنطق
   الرسم/الحركة على الـ canvas — **وكمان قيم المحتوى الحرفية النهائية** (النصوص،
   الروابط، أسماء العرض) مكتوبة مباشرة عند نقطة استخدامها، بتكتبها من الصفر
   لكل فيديو، مصدرها طلب المستخدم + `curl` فعلي على مصدر موثوق (راجع "لا
   متغيرات لبيانات المحتوى" بالقسم 1).
2. **⚙️ الطبقة التقنية الثابتة (Fixed Technical Layer)** — **موجودة كاملة تحت في
   القسم ده.** فيها Mediabunny، الـ render hooks، وحلقة التصدير. لازم تُفهم
   بالكامل ثم تُكتب **مطابقة وظيفيًا** لها في كل `scene.html` أيًا كانت الهوية أو
   المهمة، من غير أي تعديل يُخل بالعقد — دي بالظبط النقطة اللي بتضمن إن أي هوية
   جديدة (حتى لو منطقها في الرسم أو بيانات محتواها مختلفة تمامًا) هتشتغل مع
   الرانر من غير مفاجآت.

**قاعدة الدمج**: JS كله (الطبقتين) بيتحط في `<script type="module">` واحد
بالترتيب فوق (تصميم ← تقني). ترتيب التعريفات مش بيأثر على التشغيل فعليًا
(الدوال بتتنفذ بعد ما الملف كله يتحمّل)، لكن الترتيب ده بيخلي الملف مقروء ومنظّم.

---

### 🎨 عقد ملف هوية `.md` — إيه اللي لازم يوفّره لطبقة التصميم
ملف `identities/<اسم>.md` هو **مرجع شامل ومستقل تمامًا (Self-contained)** —
Blueprint هندسي يُستوعب أولاً، مش وصف كلامي عام ولا نص يُنسخ ويُلصق من غير فهم.
**ميحتويش على أي حاجة من الطبقة التقنية تحت (مفيش Mediabunny، مفيش hooks، مفيش
تصدير)** — دول موجودين مرة واحدة بس في الطبقة التقنية الثابتة، وتكرارهم جوه ملف
الهوية خطأ في حد ذاته (تكرار كود بيصعّب الصيانة، ولو حصل تعارض بينه وبين النسخة
الثابتة هيبقى مصدر باگ غامض).

كل ملف هوية `.md` لازم يحتوي على الأقسام دي بالترتيب:

1. **فقرة وصف قصيرة** (الاسم، الطابع البصري/الروح العامة، الأبعاد ونسبتها،
   حالة الاستخدام المناسبة ليها، وهل فيه شرح إضافي أو أي إحساس/محتوى مميز).
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
     الـ `duration` بتتحسب فعليًا جوه `buildParsedScenes()` نفسها بعد ما الصوت
     يتحمّل).
   - `PALETTE` (كل الألوان المستخدمة، بأسماء واضحة).
   - ثوابت الخطوط (زي `TITLE_FONT`, `BODY_FONT`) وأي ثوابت تصميم تانية (زي
     مركز Y للنص، أو نسب الـ stagger في الحركة). دي ثوابت تصميم، مش بيانات
     محتوى — تفضل متغيرات عادية بلا أي تغيير.
   - أي دوال رسم مساعدة داخلية تحتاجها الهوية (خلفية، هيدر، بطاقات، عدادات...)
     — **دول بالتحديد لازم تُفهم منطقيًا وتُكتب من الصفر بوعيك إنت، مش تُنسخ
     حرفيًا من ملف الهوية كسطور ثابتة**. السبب: طلبات المستخدم بتختلف من مهمة
     لمهمة على نفس الهوية (مثلاً "بنفس هوية X بس البطاقة تبقى دائرية مش
     مربّعة" أو "زوّد مسافة بين الأسطر شوية") — لو فاهم منطق الدالة فعليًا
     (مش بس ناقلها)، تقدر تعدّل فيها بمرونة تلبي طلب المستخدم بدقة من غير ما
     تكسر باقي التصميم أو تحتاج تسأله يوضح كل تفصيلة. لو مفيش طلب تخصيص صريح،
     النتيجة هتطابق الهوية الأصلية عمليًا برضه — الفرق إنك فهمتها قبل ما
     تكتبها، مش لصقتها.
   - **دالتين إلزاميتين لازم يتعرّفوا في الآخر** (العقد بين طبقة التصميم والطبقة
     التقنية بيقوم عليهم بس، والاسمين ثابتين مهما اختلف المحتوى):
     - `async function buildParsedScenes()`: **هي المسؤولة عن كل حاجة قبل
       الرسم** — بتتنادى مرة واحدة من الطبقة التقنية وقت التحميل. بتجيب وتفك
       تشفير الصوت المطلوب (باستخدام `fetchAndDecodeAudio`/`concatenateAudioBuffers`
       الجاهزين من الطبقة التقنية، أو أي منطق تاني تحتاجه الهوية دي بالذات)،
       **تعيّن متغيّر `audioBuffer` العام** بالصوت المدموج النهائي (الطبقة
       التقنية بتستخدمه وقت التصدير الفعلي)، تحسب `CONFIG.duration` من مدته
       الفعلية، وتبني `parsedScenes` (مصفوفة متاحة من الطبقة التقنية) بالشكل
       اللي الهوية محتاجاه بالظبط. لازم تكون `async` لأنها بتستخدم `await` مع
       الجلب وفك التشفير.
       **مفيش أي متغيرات بيانات محتوى مسمّاة جوّاها** (راجع "لا متغيرات
       لبيانات المحتوى" بالقسم 1) — الروابط والنصوص بتتكتب حرفية مباشرة عند
       نقطة استخدامها، سطر صريح واحد لكل عنصر صوت أو مشهد، من غير حلقة `for`
       بتبني روابط من أجزاء. ملف الهوية نفسه بيوضّح **النمط** بمثال حرفي واقعي
       واحد (زي المثال في "لا متغيرات لبيانات المحتوى")، ولما تيجي تستخدمه في
       مهمة حقيقية، بتعيد كتابة نفس الدالة بقيم المهمة الحرفية الجديدة، بنفس
       النمط والشكل بالظبط.
     - `function drawSceneAtTime(time)`: **نقطة الدخول الوحيدة اللي الطبقة
       التقنية بتناديها كل فريم**، سواء في المعاينة الحية أو التصدير الفعلي.
       أول سطر فيها لازم يكون `state.currentTime = time;` (احتياطي لو الصوت
       فشل يتحمّل)، وبعدين ترسم الخلفية والمشهد بناءً على `parsedScenes`.
6. **أدوات جاهزة من الطبقة التقنية، متاحة تلقائيًا لكود الهوية من غير استيراد أو
   إعادة تعريف** (لو عرّفتها تاني جوه ملف الهوية، ده تكرار غير مطلوب):
   - `fetchAndDecodeAudio(url)`: بترجع `Promise<AudioBuffer>` — بتجيب وتفك تشفير
     ملف صوت واحد من رابط `https://`.
   - `concatenateAudioBuffers(buffers)`: بتاخد مصفوفة `AudioBuffer` وترجع
     `{ buffer, segments }` — `buffer` هو الصوت المدموج كامل، و`segments` مصفوفة
     `{start, end}` بمدة كل عنصر فعليًا بالترتيب، تستخدمها الهوية لبناء
     `parsedScenes` بأي شكل تحتاجه.
   - `audioBufferToWavBlob(buffer)`: بتحوّل `AudioBuffer` مدموج لـ `Blob` صوتي
     (WAV) — مفيدة لو الهوية محتاجة تجهّز عنصر `<audio>` للمعاينة الحية.
   - `layoutArabicParagraph(text, font, maxWidth, wordGap, lineHeight, centerY)`
     — خوارزمية تخطيط نص عربي RTL عامة. لو الهوية محتاجة خوارزمية تخطيط مختلفة
     تمامًا، عرّف دالة بديلة باسم مختلف جوه كود الهوية نفسه.
   - `Easing.linear / .easeOutCubic / .easeInOutCubic`.
   - `clamp01(val)`, `toArabicDigits(input)`.
   - `ctx` (الـ 2D context)، و `CONFIG.width`/`CONFIG.height` (بعد ما تتعرّف).
7. **المتغيّر الوحيد المسموح به من نوع "بيانات محتوى" هو `OUTPUT_FILENAME`**
   (اسم ملف الإخراج، بدون امتداد) — الطبقة التقنية الثابتة بتستخدمه بالاسم ده
   بالظبط لتسمية الملف الناتج، فهو واجهة اتفاق تقنية بين الطبقتين مش بيانات
   محتوى حقيقية. عدا كده، مفيش أي متغيرات لبيانات المحتوى خالص (راجع "لا
   متغيرات لبيانات المحتوى" بالقسم 1) — كل قيمة محتوى تُكتب حرفية مباشرة عند
   نقطة استخدامها جوه البند 5 فوق.
8. **ملاحظات/تنبيهات خاصة بالهوية دي بس** (لو فيه)، تتكتب جوه ملف الهوية نفسه
   بدل ما تتوزع في `AGENTS.md`.

---

### ⚙️ الطبقة التقنية الثابتة — مرجع هندسي ثابت، يُفهم بالكامل ثم يُكتب مطابقًا وظيفيًا في كل scene.html
ده الجزء اللي **ثابت وظيفيًا 100%** في كل `scene.html`. أي تعديل فيه (حتى لو
شكله بسيط أو "تحسين") يعتبر مخالفة للعقد التقني — افهمه بالكامل الأول، وبعدين
اكتبه في الملف الناتج مطابقًا لهذا المرجع تمامًا.

**هيكل الـ `<head>`/`<body>` الثابت** (الأماكن المعلّمة بـ 🎨 بتتملى من ملف الهوية):

```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Video Clip — Shorts (مع دعم Agent Auto-Render)</title>

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

        // ============ 🎨 تصميم الهوية — هنا (بيانات المحتوى + كود الهوية، مبنية بفهم من ملف identities/<اسم>.md) ============

        // ============ ⚙️ الطبقة التقنية الثابتة — من هنا تحت، مطابقة وظيفيًا للمرجع ============
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

// المتغيرات دي متعرّفة هنا مرة واحدة بس — كود الهوية (buildParsedScenes) بيعيّن
// لها قيمة (audioBuffer = ...، parsedScenes = ...) وما بيعيدش تعريفها بـ let/const
let audioBuffer = null;
let audioAudioEl = null;
let parsedScenes = [];
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

// --- أدوات جلب ودمج الصوت — عامة، بتستخدمها buildParsedScenes بتاعة أي هوية
// بأي URLs وبأي عدد مقاطع تحتاجه (الهوية هي اللي بتقرر التفاصيل، مش هنا) ---
let sharedAudioCtx = null;
function getSharedAudioContext() {
    if (!sharedAudioCtx) sharedAudioCtx = new (window.AudioContext || window.webkitAudioContext)();
    return sharedAudioCtx;
}

async function fetchAndDecodeAudio(url) {
    const res = await fetch(url);
    const arrayBuf = await res.arrayBuffer();
    return getSharedAudioContext().decodeAudioData(arrayBuf);
}

// بتدمج مصفوفة AudioBuffers في واحد متصل، وترجع كمان segments (بداية/نهاية كل
// عنصر بالمدة الفعلية بعد فك التشفير) عشان الهوية تبني منها parsedScenes بالشكل
// اللي تحتاجه بالظبط (أسماء الحقول شأن الهوية، راجع "تجريد متغيرات المحتوى")
function concatenateAudioBuffers(buffers) {
    if (buffers.length === 0) throw new Error("لا يوجد أي صوت لدمجه");
    const sampleRate = buffers[0].sampleRate;
    const channelsCount = buffers[0].numberOfChannels;
    const totalSamples = buffers.reduce((sum, b) => sum + b.length, 0);
    const merged = getSharedAudioContext().createBuffer(channelsCount, totalSamples, sampleRate);

    let sampleOffset = 0, timeOffset = 0.0;
    const segments = [];
    buffers.forEach(buf => {
        for (let ch = 0; ch < channelsCount; ch++) {
            merged.getChannelData(ch).set(buf.getChannelData(ch), sampleOffset);
        }
        const duration = buf.duration;
        segments.push({ start: timeOffset, end: timeOffset + duration });
        sampleOffset += buf.length;
        timeOffset += duration;
    });

    return { buffer: merged, segments };
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

// بتجهّز عنصر <audio> للمعاينة الحية من الصوت المدموج — تستخدمها buildParsedScenes
// بعد ما تحسب audioBuffer (اختيارية: لو الهوية عايزة معاينة بصوت حقيقي)
function setupPreviewAudio(buffer) {
    const wavBlob = audioBufferToWavBlob(buffer);
    const blobUrl = URL.createObjectURL(wavBlob);
    audioAudioEl = new Audio(blobUrl);
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
    logToConsole(`بدء عملية التصدير لمكافئ MP4 (${CONFIG.width}x${CONFIG.height})...`);

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
            // لأي سكريبت خارجي يغيّره لاحقًا (تغيير الامتداد يدويًا بيكسر الملف
            // حتى لو باين شغال، لأن المحتوى الفعلي جواه مش متطابق مع الامتداد).
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
        await buildParsedScenes(); // من طبقة تصميم الهوية — بتجيب الأصول وتبني كل حاجة
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

- **الصوت**: استخدم `fetchAndDecodeAudio(url)`/`concatenateAudioBuffers(buffers)`
  الجاهزين من الطبقة التقنية جوه `buildParsedScenes()` بتاعة الهوية — `fetch()`
  مباشر من الرابط الحرفي الكامل لكل عنصر صوت على حدة، من غير أي خطوة تحميل
  مسبقة على القرص. مفيش داعي تعيد كتابة منطق الجلب/فك التشفير نفسه، الأدوات
  دي بتعمله.
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

### دليل كتابة سكريبت الرندر — الاستثناء الوحيد: انسخه مباشرة زي ما هو
**`render-runner.js` هو الحاجة الوحيدة في العقد ده اللي مسموح تُنسخ مباشرة من
غير نفس متطلبات "الفهم العميق وإعادة الكتابة" المطلوبة في كود الهوية والطبقة
التقنية.** السبب: ده بنية تحتية أوتوميشن بحتة (تشغيل متصفح، رفع سيرفر محلي،
متابعة حالة) من غير أي قرار تصميمي أو محتوى بيتغيّر من مهمة لمهمة — نسخه
حرفيًا هنا مش "لصق أعمى"، هو الاختيار الصح لأنه نفس الكود بالظبط مطلوب يشتغل
في كل مهمة من غير أي تعديل. **افهمه برضه كفاية إنك تقدر تصلحه** لو قابلت خطأ
فيه أو محتاج تعدّل حاجة بسيطة (زي مسار ملف)، لكن مفيش داعي "تعيد صياغته" زي
ما بتعمل مع كود الهوية.

اكتبه بأمر `run_terminal` (heredoc) في ملف زي `render-runner.js`، وشغّله بعد كده
بأمر `run_terminal` تاني: `node render-runner.js`. **مهم**: workflow الـ CI بيثبّت
قناة `chrome` بس (مش `chromium` الافتراضي) — لازم تحدد `channel: 'chrome'` صراحة
في `launch()` وإلا الرندر هيفشل بـ "Executable doesn't exist".

> ⚠️ **استخدم حلقة استطلاع (polling loop) يدوية بدل `page.waitForFunction`
> لمتابعة حالة الرندر — مش نداء واحد صامت بينتظر لحد ما يخلص أو يعمل
> `TimeoutError`.** السبب: `page.waitForFunction` بتنتظر بصمت من غير أي مؤشر
> تقدم، وأي نسخة منها تتكتب بعدين (لو اضطريت تعدّل السكريبت) من غير `timeout`
> صريح بتقع على الـ 30 ثانية الافتراضية بتاعة Playwright وتكراش قبل ما الرندر
> يخلص أصلًا (رندر فيديو فعلي بياخد دقايق). الحلقة تحت بتحل المشكلة من جذورها:
> مفيش أي `timeout` ممكن يتنسى، لأن الانتظار محسوب بعدد محاولات صريح مكتوب في
> الكود نفسه، وكل محاولة بتطبع سطر تقدم واضح في الـ terminal (مثلًا
> `⏳ [12/120] renderStatus=rendering — 34%`) بدل السكوت التام لحد اللحظة
> الأخيرة. افهم السكريبت اللي تحت بالكامل، وبعدين اكتبه **مطابقًا وظيفيًا
> بالكامل** من غير ما تختصر فيه أو تحذف منه حاجة.
>
> **لو اضطريت تعيد كتابة `render-runner.js` أكتر من مرة في نفس المهمة** (مثلًا
> عشان تصلح حاجة تانية فيه)، **راجع الملف كامل بـ `cat render-runner.js`**
> (ممنوع استخدام `grep`) **قبل** ما تشغّله في كل مرة (مش بعد ما يفشل)، وتأكد
> إنك سبت حلقة الاستطلاع زي ما هي من غير ما ترجعها لـ `page.waitForFunction`
> بالغلط وقت إعادة الكتابة.

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

  // --- حلقة استطلاع يدوية لمتابعة تقدم الرندر، بدل page.waitForFunction الصامتة ---
  const POLL_INTERVAL_MS = 4000;
  const MAX_POLL_ATTEMPTS = 120; // 120 × 4 ثانية = 8 دقايق حد أقصى
  let finalStatus = null;

  for (let attempt = 1; attempt <= MAX_POLL_ATTEMPTS; attempt++) {
    const { status, progress } = await page.evaluate(() => ({
      status: window.renderStatus,
      progress: window.renderProgress
    }));
    const pct = Math.round((progress || 0) * 100);
    console.log(`⏳ [${attempt}/${MAX_POLL_ATTEMPTS}] renderStatus=${status} — ${pct}%`);

    if (status === 'completed' || status === 'error') {
      finalStatus = status;
      break;
    }
    await page.waitForTimeout(POLL_INTERVAL_MS);
  }

  if (!finalStatus) {
    throw new Error(`انتهى الوقت المحدد (${MAX_POLL_ATTEMPTS * POLL_INTERVAL_MS / 1000}s) من غير ما renderStatus يوصل completed أو error`);
  }

  const status = finalStatus;
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

### منهجية عامة لحل الأخطاء — افهم السبب فعليًا قبل ما تصلح
لو `render-runner.js` رجّع `success: false`، أو ظهر أي خطأ تاني وقت كتابة
`scene.html` أو وقت الرندر، اتبع الترتيب ده بدل ما تخمّن سبب الخطأ من شكله
العام:

1. **اقرأ رسالة الخطأ الكاملة فعليًا** (`error`, `console_logs`,
   `failed_requests` من نتيجة `render-runner.js`) — نفس نص الخطأ ممكن يبقى
   سببه أكتر من حاجة (مثلاً `X is not defined` ممكن يبقى بسبب إعادة تعريف
   المتغيّر في مكان تاني، أو حذف تعريفه الأصلي بالغلط، أو ترتيب الطبقتين
   اتقلب) — متفترضش نفس الحل دايمًا لمجرد إن رسالة الخطأ شبه خطأ شفته قبل كده.
2. **راجع `scene.html` كامل بـ `cat`** (ممنوع `grep`) في المنطقة اللي الخطأ
   بيشاور عليها، وتأكد فعليًا من وجود أو غياب التعريف/الاستدعاء المعني، بدل
   ما تضيف حل "غالبًا هيشتغل" من غير ما تتحقق بعينك.
3. **لو الحل يمس حاجة من الطبقة التقنية الثابتة** (زي `audioBuffer`،
   `parsedScenes`، `CONFIG`، أو أي hook)، ارجع للمرجع الأصلي في القسم 2 وقارن
   بالظبط — الحل الصح يكون "استرجاع التطابق مع المرجع"، مش إضافة تعريف جديد
   بيحل العرض الظاهر من غير ما يصلح السبب الحقيقي.
4. **بعد أي إصلاح، أعد تشغيل `render-runner.js` كامل من الصفر** (مش تفترض إن
   الإصلاح نجح من غير تشغيل فعلي) وراجع الناتج تاني بنفس الدقة قبل ما تعتبر
   المهمة خلصت.

### ملفات العلامة (Marker Files) — إلزامية لتتبع التقدم
- ارفع مع كل فيديو ناتج 3 أصول على الـ Release (بأمر `gh release upload` حقيقي
  لكل واحد): ملف الفيديو `.mp4`، ملف وصف الفيديو `.md`، **وملف `scene.html`
  نفسه** (الكود المصدري اللي اتبنى منه الفيديو ده بالتحديد — مفيد للمراجعة أو
  إعادة الرندر لاحقًا من غير الحاجة لإعادة كتابته من الصفر).
- بعد ما الرفع الثلاثي ده يتم فعليًا (مش افتراض)، اكتب:
  `video_<معرّف-الفيديو>_done.json` يحتوي
  `{"video_id": "<معرّف>", "release_video_url": "...", "release_md_url": "...", "release_scene_url": "..."}`.
  **`agent.js` بيتحقق فعليًا** إن أسماء الملفات التلاتة في الروابط دي موجودين
  حقًا كـ assets على الـ Release عن طريق `gh release view` قبل ما يقبل ملف
  العلامة ده — لو مش موجودين هيرفضه برسالة توضح إيه الناقص، فتأكد إن الرفع حصل
  فعلًا الأول.
- بعد انتهاء **كل** الفيديوهات المطلوبة في المهمة، اكتب:
  `TASK_COMPLETE.json` يحتوي `{"summary": "...", "videos": [...]}`

---

## القسم 3: قواعد صارمة — غير قابلة للتفاوض
1. **ممنوع منعًا باتًا** كتابة أي نص محتوى (نص مقروء، اقتباس، معلومة واقعية)
   من "معرفتك" الداخلية. كل نص في أي `scene.html` لازم مصدره نتيجة `curl`
   فعلية نُفّذت في نفس الجلسة على مصدر موثوق يحدده وصف المهمة.
2. كل توقيت (متى يظهر كل مشهد أو عنصر) يُحسب من **المدة الفعلية** للصوت بعد
   تحميله وفكّه (`AudioBuffer.duration`)، وليس تخمينًا.
3. **بيانات المحتوى (روابط، نصوص، أسماء عرض) تُكتب بقيمها الحرفية النهائية
   مباشرة عند نقطة استخدامها** — مفيش متغيرات وسيطة بتتجمّع أو تتبنى منها قيم
   تانية (راجع القسم 1، "لا متغيرات لبيانات المحتوى").
4. **ملف وصف الفيديو الناتج** (اللي بيترفع مع كل فيديو على الـ Release — مختلف
   تمامًا عن ملف هوية `.md` في `identities/`) يجب أن يحتوي: عنوان/موضوع
   الفيديو، المدة الكلية، وصف مختصر للمحتوى، ورابط الـ GitHub Release الفعلي
   بعد الرفع.
5. **ممنوع استخدام `grep` للتحقق من أي ملف ناتج** (`scene.html` أو سكريبت
   الرندر) — المراجعة دايمًا بعرض الملف كامل عبر `cat`.
