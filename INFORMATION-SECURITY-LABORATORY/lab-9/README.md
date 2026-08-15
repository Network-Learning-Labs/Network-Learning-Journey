# Cross-Site Scripting (XSS) Attack Lab - Elgg Setup Guide
دليل تجهيز معمل هجمات البرمجة عبر مواقع الويب (تطبيق Elgg)
## 1. Overview & Core Concept | نظرة عامة وفكرة العمل

 Cross-Site Scripting (XSS) is a vulnerability where an attacker injects malicious JavaScript code into a vulnerable web application (like the Elgg social network). When victims visit the infected profile, the malicious code executes inside their browser. This allows the attacker to steal session cookies or force the victim's account to automatically self-propagate the malicious code to other users, forming an XSS Worm.

العربية: ثغرة XSS تمكن المهاجم من حقن كود جافا سكريبت خبيث في تطبيق الويب (مثل شبكة Elgg الاجتماعية). عندما يزور الضحايا الملف الشخصي المصاب، يُنفذ الكود داخل متصفحاتهم، مما يسمح بسرقة ملفات الجلسة (Cookies) أو إجبار حساب الضحية على نشر الكود تلقائياً لزوار آخرين، لتتكون دودة XSS (XSS Worm) المتنتشرة ذاتياً.

## 2. Lab Environment Setup | تجهيز بيئة العمل

 Configure the local DNS entries and start the lab containers using Docker Compose.

العربية: إعداد أسماء النطاقات المحلية (DNS) وتشغيل حاويات المعمل باستخدام Docker Compose.
### --- Step 1: Configure DNS / Hosts ---
#### Adding local domain mappings to /etc/hosts (requires root privileges)
```bash
echo "[-] Configuring /etc/hosts entries..."
sudo tee -a /etc/hosts > /dev/null <<EOF
10.9.0.5   www.seed-server.com
10.9.0.5   www.example32a.com
10.9.0.5   www.example32b.com
10.9.0.5   www.example32c.com
10.9.0.5   www.example60.com
10.9.0.5   www.example70.com
EOF
echo "[+] DNS entries added successfully."
```
### --- Step 2: Container Setup and Launch ---
#### Navigate to Labsetup directory, build images, and start containers in the background
```bash
if [ -d "Labsetup" ]; then
    cd Labsetup
    echo "[-] Building Docker containers..."
    docker-compose build or dcbuild
    echo "[-] Starting Docker containers in the background..."
    docker-compose up -d or dcup
    echo "[+] Lab environment is up and running successfully!"
```
* بصير ان نضع كل امر في تيرمنال للسرعة

![error](test-1.png)
 ### لكن ظهر خطأ بسيط في النهاية عند محاولة حذف شبكة الدوكر (net-10.9.0.0):
has active endpoints
أي أن الشبكة لا تزال مرتبطة بحاوية أخرى تعمل في الخلفية ولم تتوقف بعد (غالباً حاوية الـ mysql السابقة).
#### 1. حذف حاوية الـ MySQL القديمة يدوياً بقوة:
```bash
docker rm -f 2d6417fc1bbe
```
#### 2. تشغيل البيئة من جديد بسلاسة:
```bash
dcup
```

![noerror](test-3.png)
*هذا يعني أن قاعدة البيانات أتمت إعدادها بنجاح وأصبحت جاهزة تماماً لتلقي الاتصالات من تطبيق Elgg.
#### الخطوة التالية الآن:
افتحي المتصفح في الـ VM (مثل Firefox).

اكتبي في شريط العنوان:
```bash
http://www.seed-server.com
```
#### كيف ندخل بالطريقة الصحيحة الآن؟
انظري إلى أعلى يسار الصفحة، ستجدين زر Log in.

بدلاً من الدخول عبر الرابط التجريبي، اضغطي على Log in وسجلي الدخول بالطريقة الطبيعية للموقع باستخدام بيانات أليس:

* Username: alice

* Password: seedalice

![login](test-2.png)


