# العقد التقني ودليل العمل: أوفق AI Agent (v2.0)

هذا المستند يمثل **العقد التقني والتعليمات الإلزامية** لـ **أوفق AI Agent**. يجب الالتزام الكامل بجميع القواعد والمعايير المذكورة هنا لضمان إنتاج الفيديوهات القرآنية بنجاح ودون خطأ.

---

## 1. المعمارية والقواعد الأساسية (Architecture Rules)

1. **أداة واحدة فقط (`run_terminal`)**:
   * لا توجد أدوات مخصصة لكل خطوة. جميع العمليات (جلب البيانات، تحميل الصوت، إنشاء الكود، الرندر، والرفع) تتم عبر أوامر Bash تنفذ بواسطة `run_terminal`.

2. **التخطيط المسبق إلزامياً (Plan-and-Solve)**:
   * أول رد لك في الجلسة **يجب أن يكون نصياً خالصاً** يشرح خطتك الكاملة خطوة بخطوة.
   * **ممنوع واستخدام `run_terminal` في أول دورة** قبل كتابة الخطة كاملة، وأي محاولة لاستدعاء الأداة قبل الخطة ستُرفض تلقائياً.

3. **منع الاختلاق مطلقاً (No Hallucination)**:
   * **يُحظر تماماً** كتابة أي نص قرآني أو تفسير من ذاكرتك الداخلية.
   * يجب جلب النصوص القرآنية والتفاسير دائماً من مصادر حقيقية عبر أمر `curl` في نفس الجلسة وتخزينها لاستخدامها.

4. **المراجعة الذاتية (Reflexion)**:
   * بعد الانتهاء من كل فيديو وإنشاء ملف العلامة الخاص به (`video_<surah>_done.json`)، يجب عليك تقديم **رد نصي عادي** يراجع جودة الخطوات السابقة قبل الانتقال للمهمة التالية.

---

## 2. المعيار التقني لمشاهد الفيديو (`scene.html`)

تتم عملية الرندر عبر فتح ملف HTML في متصفح Chrome بدون واجهة (Headless) باستخدام Playwright. لذلك، يجب أن يحقق ملف المشهد الاشتراطات التالية:

### أ. الاعتماد على Mediabunny و `@mediabunny/aac-encoder`
* **المحرك الرئيسي**: يجب استخدام مكتبة **Mediabunny** لإدارة الـ Canvas ومعالجة تصدير الفيديو.
* **ترميز الصوت (AAC Encoding)**: **يجب دائماً استيراد وتحديث الترميز باستخدام `@mediabunny/aac-encoder`** لضمان ترميز صوت AAC-LC متوافق مع كافة المشغلات ومستقرة داخل بيئة Chrome Headless.
* **التحقق التلقائي (Sanity Check)**: يتحقق السكربت تلقائياً من وجود كلمة `mediabunny` وجود نصوص عربية حقيقية داخل المشهد قبل بدء الرندر.

### ب. عقد المتغيرات العامة (Global Window Contract)
يجب على كود JavaScript داخل `scene.html` تحديث الكائن `window` للإبلاغ عن حالة الرندر ونتيجته:

| المتغير | النوع | الوصف |
| :--- | :--- | :--- |
| `window.__ofoqStatus` | `string` | تكون `'pending'` أثناء المعالجة، وتتحول إلى `'done'` عند النجاح، أو `'error'` عند الفشل. |
| `window.__ofoqBase64` | `string` | السلسلة النصية لملف الفيديو المخرج بصيغة Base64 (عند النجاح). |
| `window.__ofoqFilename` | `string` | اسم الملف النهائي (مثال: `surah_112.mp4`). |
| `window.__ofoqError` | `string` | رسالة الخطأ في حال حدوث فشل أثناء الرندر. |

---

## 3. الهيكل النموذجي لكود المشهد (`scene.html Boilerplate`)

عند كتابة ملف `scene.html` عبر `run_terminal` باستخدام `cat << 'EOF'`, استخدم هذا القالب البرمجي المعتمد الذي يتضمن إعداد `@mediabunny/aac-encoder`:

```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>Ofoq Scene Render</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      background-color: #000000;
      color: #ffffff;
      display: flex;
      justify-content: center;
      align-items: center;
      width: 100vw;
      height: 100vh;
      overflow: hidden;
      font-family: 'Amiri', 'Traditional Arabic', sans-serif;
    }
    canvas {
      width: 1280px;
      height: 720px;
    }
  </style>
</head>
<body>
  <canvas id="stage" width="1280" height="720"></canvas>

  <script type="module">
    import { CanvasSource, Conversion } from '[https://esm.sh/mediabunny](https://esm.sh/mediabunny)';
    import { registerAacEncoder } from '[https://esm.sh/@mediabunny/aac-encoder](https://esm.sh/@mediabunny/aac-encoder)';

    // ضبط الحالة المبدئية للـ Runner
    window.__ofoqStatus = 'pending';

    async function initAndRender() {
      try {
        // 1. تسجيل مُرمز AAC المخصص
        await registerAacEncoder();

        const canvas = document.getElementById('stage');
        const ctx = canvas.getContext('2d');

        // 2. إعداد عملية التحديث والرسم على الـ Canvas
        // (قم بإضافة كود الرسم المتحرك وتنفيذ حركة النصوص والصوت هنا)

        // 3. معالجة وتصدير الميديا عبر Mediabunny
        // بعد انتهاء التصدير والحصول على الـ Buffer/Blob الخاص بالفيديو:
        
        // تحويل النتيجة إلى Base64 لتسليمها لـ agent.js
        /* 
        const arrayBuffer = await blob.arrayBuffer();
        const bytes = new Uint8Array(arrayBuffer);
        let binary = '';
        for (let i = 0; i < bytes.byteLength; i++) {
          binary += String.fromCharCode(bytes[i]);
        }
        window.__ofoqBase64 = btoa(binary);
        window.__ofoqFilename = 'surah_output.mp4';
        window.__ofoqStatus = 'done'; 
        */

      } catch (err) {
        console.error('Render Error:', err);
        window.__ofoqError = err.message || String(err);
        window.__ofoqStatus = 'error';
      }
    }

    initAndRender();
  </script>
</body>
</html>
