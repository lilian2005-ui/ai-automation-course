<div dir="rtl">

# Recruiter Matching Agent

> AI Agent على Telegram يساعد مسؤولي التوظيف على جلب أحدث الوظائف عن بُعد ومطابقة المرشحين معها بتقييم من 100، ثم يقرر الخطوة التالية ضمن حدود واضحة.

---

## 🎯 Problem

### The Context
مسؤولو التوظيف (Recruiters) يتابعون باستمرار وظائف جديدة، ويقارنون سير المرشحين الذاتية بمتطلبات كل وظيفة.

### The Problem
تتبّع الوظائف الجديدة ومقارنة كل مرشح بكل وظيفة يدويًا يستهلك ساعات، ومعايير المقارنة قد تختلف من مرشح لآخر فتصبح النتائج غير متسقة.

### Why It Matters
تقييم ثابت وسريع يوفر وقت مسؤول التوظيف، ويجعل المقارنة بين المرشحين عادلة ومتسقة، مع إبقاء القرار النهائي بيد الإنسان.

---

## 💡 Solution

Agent يتواصل معه مسؤول التوظيف عبر Telegram، يجلب الوظائف مباشرة، ويقيّم المرشحين بمعيار ثابت، ثم يحدد الخطوة التالية دون أن يتجاوز صلاحياته.

**What does the system do?**

1. يطلب مسؤول التوظيف أحدث الوظائف في مجال معين، فيجلبها الـ Agent مباشرة من Himalayas Jobs API.
2. يستخرج متطلبات الوظيفة ويعرضها على مسؤول التوظيف ليؤكدها.
3. يقيّم كل مرشح من 100 وفق Rubric ثابت: المهارات، والخبرة، وملاءمة الدور، والتعليم.
4. يقرر الخطوة التالية: Shortlist، أو طلب توضيح، أو تحويل الحالة لإنسان، أو التوقف.

---

## 👤 Users

**Primary Users:**
مسؤولو التوظيف (Recruiters) الذين يطابقون مرشحيهم مع وظائف عن بُعد.

**User Need:**
معرفة الوظائف الجديدة بسرعة، والحصول على تقييم واضح ومتسق لمدى ملاءمة كل مرشح.

---

## 🧠 How I Designed the Solution

صممت المشروع وفق طبقات U5:

- **Choose:** اخترت Agent لأن طلبات مسؤول التوظيف تختلف في كل مرة، ويحتاج النظام إلى تحديد متى يجلب الوظائف ومتى يسأل ومتى يقيّم.
- **Define:** مسؤوليته جلب الوظائف وتقييم المرشحين واقتراح الخطوة التالية. خارج نطاقه: التواصل مع المرشحين، أو التقديم على الوظائف، أو اتخاذ قرار التوظيف النهائي.
- **Control:** الحالات الحدية (درجة من 60 إلى 74) تُحوّل لمسؤول التوظيف، والملفات الناقصة تُعلَّم `Needs Review`، ويتجاهل الـ Agent الصفات الشخصية مثل العمر والجنس.
- **Act:** أداة واحدة فقط لجلب الوظائف (HTTP Request)، لأنها ما تحتاجه المهمة.

</div>

<div dir="ltr">

```text
Telegram Trigger (recruiter message)
              ↓
AI Agent + Simple Memory (session per chat)
              ↓
HTTP Request → Himalayas Jobs API (latest remote jobs)
              ↓
Extract requirements → Recruiter confirms
              ↓
Score candidates /100 (fixed rubric)
              ↓
Shortlist · Ask · Hand off to human · Stop
              ↓
Telegram Send Message
```

</div>

<div dir="rtl">

---

## 🤖 Role of AI

**What does AI do in this solution?**

- يفهم طلب مسؤول التوظيف ويحدد متى يستخدم أداة جلب الوظائف.
- يستخرج متطلبات الوظيفة من وصفها.
- يقيّم المرشح وفق الـ Rubric ويقترح الخطوة التالية.

### What AI Should NOT Do

- التواصل مع المرشحين أو التقديم على الوظائف.
- اتخاذ قرار التوظيف النهائي.
- استخدام صفات شخصية مثل العمر أو الجنس في التقييم.

---

## 🔄 Automation

**What parts of the process are automated?**

- جلب أحدث الوظائف عن بُعد عند الطلب.
- استخراج المتطلبات وتقييم المرشحين.
- الرد على مسؤول التوظيف عبر Telegram.

---

## 🗂️ Data

### Input
رسائل مسؤول التوظيف (المجال المطلوب، وبيانات المرشحين)، ونتائج Himalayas Jobs API.

### Output
قائمة بأحدث الوظائف، ومتطلبات كل وظيفة، وتقييم كل مرشح من 100 مع الخطوة المقترحة.

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| n8n | بناء الـ Workflow وتشغيل الـ Agent |
| Telegram Bot | استقبال رسائل مسؤول التوظيف وإرسال الردود |
| AI Agent (n8n) | فهم الطلب واتخاذ قرار الخطوة التالية |
| OpenRouter Chat Model | نموذج اللغة لتحليل الطلبات والتقييم |
| Simple Memory | الاحتفاظ بسياق كل محادثة |
| HTTP Request (Himalayas Jobs API) | جلب أحدث الوظائف عن بُعد |

---

## 🔑 Key Design Decisions

### Decision 1
**Decision:**
تقييم المرشحين وفق Rubric ثابت من 100 (المهارات، والخبرة، وملاءمة الدور، والتعليم).

**Why:**
يجعل المقارنة متسقة وعادلة بين المرشحين بدل معايير تتغير من حالة لأخرى.

### Decision 2
**Decision:**
تحويل الحالات الحدية (60 إلى 74) لمسؤول التوظيف، وتعليم الملفات الناقصة `Needs Review`.

**Why:**
الحالات غير الواضحة تحتاج حكمًا بشريًا، والـ Agent لا يملك قرار التوظيف.

### Decision 3
**Decision:**
تجاهل الصفات الشخصية مثل العمر والجنس.

**Why:**
التقييم يجب أن يعتمد على ما يخص الوظيفة فقط، تجنبًا للتحيز.

---

## 🧪 Testing

### Test 1: First reply on Telegram
**Input:**
إرسال أول رسالة للبوت على Telegram.

**Expected Result:**
يرد الـ Agent برسالة ترحيب ويسأل عن المجال المطلوب.

**Actual Result:**
ولّد الـ Agent الرد، لكن عقدة الإرسال فشلت بخطأ `chat not found` لأن خانة Chat ID كانت `{{ $execution.id }}` ([Error](./Sceenshots/Chat-ID-Error.png)). بعد ربطها برقم المحادثة من Telegram Trigger وصل الرد بشكل صحيح.

**Status:** ✅ (بعد الإصلاح)

---

### Test 2: Fetch and score
**Input:**
طلب أحدث الوظائف في مجال معين، ثم تقييم مرشح.

**Expected Result:**
يجلب الـ Agent الوظائف من Himalayas، ويعرض المتطلبات للتأكيد، ثم يعطي درجة من 100 وخطوة مقترحة.

**Actual Result:**
لم يُختبر بعد؛ المشروع في مرحلة البناء.

**Status:** ⬜ Not Tested

---

## 📸 Evidence

- 🔗 [View n8n Workflow](./Sceenshots/Workflow%20%281%29.png)
- 🔗 [View Chat ID Error](./Sceenshots/Chat-ID-Error.png)

---

## ⚠️ Challenges & Failures

**What didn't work as expected?**

- فشلت عقدة Send a text message بخطأ `chat not found`.
- n8n Cloud يعمل بفترة تجريبية، فلا يوجد رابط عام دائم للـ Workflow.

**How did I solve it?**

- اكتشفت أن خانة Chat ID تستخدم رقم التشغيل (`$execution.id`) بدل رقم المحادثة، فربطتها بـ `message.chat.id` من Telegram Trigger، وربطت Session ID في Simple Memory بالقيمة نفسها حتى لا تختلط المحادثات.
- وثّقت الـ Workflow بالصور.

---

## 📚 What I Learned

### Technical Lesson
كل عقدة في n8n تحتاج القيمة الصحيحة من العقدة الصحيحة؛ خطأ في ربط حقل واحد يوقف الرد كله.

### Problem-Solving Lesson
قراءة رسالة الخطأ بدقة (`chat not found`) دلّتني مباشرة على الحقل المسبب بدل التخمين.

### AI Lesson
الـ Agent المفيد ليس الأكثر استقلالية؛ بل الذي يعرف ما يجب ألا يفعله ومتى يحوّل القرار لإنسان.

---

## 🔮 Future Improvements

If I continued developing this project, I would:

- اختبار جلب الوظائف والتقييم على حالات متنوعة، منها الحالات الحدية والملفات الناقصة.
- حفظ نتائج التقييم في Google Sheets بعد تأكيد مسؤول التوظيف.
- إضافة شرط في الـ Workflow يفرض حدود القرار على مستوى النظام وليس الـ Prompt فقط.

---

## 🔗 Project Links

**Live Demo:** غير متاح (n8n Cloud بفترة تجريبية). راجع قسم Evidence.

**Workflow:** [Workflow Screenshot](./Sceenshots/Workflow%20%281%29.png)

**Repository:** [github.com/lilian2005-ui/Lilian_aiAutomation](https://github.com/lilian2005-ui/Lilian_aiAutomation)

---

## 👩‍💻 Author

**Lilian Abu Ramadan**

[LinkedIn](https://linkedin.com/in/lilian-abu-ramadan) · [GitHub](https://github.com/lilian2005-ui)

</div>
