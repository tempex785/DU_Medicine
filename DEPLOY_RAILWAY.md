# رفع البوت على Railway

## 1) ارفع الكود على GitHub
- اعمل ريبو جديد (Private) وارفعله كل الملفات **ما عدا `.env`** (محتفظ بيه في .gitignore أصلاً).
- تأكد إن `mediDent.db` القديم مش هترفعه هو كمان — هنعمل نسخة جديدة فاضية على السيرفر، أو لو عايز تبدأ بالبيانات اللي عندك دلوقتي، ارفعه مرة واحدة يدويًا بعد أول deploy (خطوة 4).

## 2) على Railway
1. https://railway.app → New Project → Deploy from GitHub repo → اختار الريبو.
2. Railway هيكتشف `requirements.txt` و `Procfile` تلقائي ويعرف إنه Python worker.

## 3) ضيف Environment Variables (من تبويب Variables)
```
BOT_TOKEN=التوكن بتاعك من BotFather
ADMIN_IDS=123456789,987654321
DB_FILE=/data/mediDent.db
```
⚠️ متحطش التوكن في أي ملف بترفعه لـ GitHub — هنا بس في Railway Variables.

## 4) ضيف Volume (عشان الداتابيز متتمسحش)
1. من صفحة السيرفس بتاعك في Railway → تبويب **Settings** → **Volumes** → **Add Volume**.
2. اكتب Mount Path: `/data`
3. كده أي حاجة تتكتب في `/data` (زي الداتابيز والباكاب) هتفضل موجودة حتى لو عملت redeploy.

### لو عايز تبدأ بالداتابيز اللي عندك دلوقتي (فيها بيانات موجودة):
بعد إضافة الـ Volume، من تبويب الـ service اعمل "Shell" (أو استخدم Railway CLI):
```
railway run bash
```
وبعدين ارفع ملف `mediDent.db` بتاعك لمسار `/data/mediDent.db` (عن طريق `railway volume` أو `scp`/`railway up` حسب الخيارات المتاحة في حسابك). لو الطريقة دي معقدة، أبسط حل: خليه ينشئ قاعدة بيانات جديدة فاضية أول تشغيل، وبعدين استورد بياناتك القديمة يدويًا.

## 5) شغّل
Railway هيشغل تلقائي بعد أول push. تابع الـ Logs من تبويب Deployments للتأكد إن البوت اشتغل من غير أخطاء.

## ملاحظات مهمة
- البوت شغال بـ `run_polling()` يعني لازم يفضل عملية شغالة طول الوقت (مش Serverless) — Railway مناسب لده لأنه بيشغل الـ Worker باستمرار.
- كل تحديث للكود (push جديد) = redeploy، والداتابيز هتفضل سليمة طول ما هي جوه `/data` (الـ Volume).
