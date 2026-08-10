# SQL Injection Attack Lab - Setup & Task 1 Guide
# دليل إعداد وحل التاسك الأول لمختبر حقن قواعد البيانات

## Overview / الفكرة العامة

### English
This lab focuses on **SQL Injection (SQLi)**, a code injection technique that exploits vulnerabilities in the interface between web applications and backend database servers. When user inputs are not properly checked or sanitized, attackers can manipulate SQL queries to view, modify, or delete unauthorized data. The goal of this lab is to understand how these vulnerabilities occur, how to exploit them, and how to defend against them using techniques like prepared statements.

### العربية
يركز هذا اللاب على ثغرات **حقن قواعد البيانات (SQL Injection)**، وهي تقنية استغلال تحدث عندما لا يتم التحقق من مدخلات المستخدمين بشكل صحيح قبل إرسالها إلى قاعدة البيانات. يتيح ذلك للمهاجم التلاعب بالاستعلامات لرؤية أو تعديل أو حذف بيانات غير مأذون بها. الهدف من هذا اللاب هو فهم كيفية حدوث هذه الثغرات، وطريقة استغلالها، وكيفية الحماية منها باستخدام الاستعلامات المحضرة (Prepared Statements).

---

## Lab Environment & First Steps / بيئة العمل والخطوات الأولى

### English
1. **Navigate to the Lab Directory:** Unzip the lab files and open the terminal inside the `Labsetup` folder.
2. **Start the Containers:** Use Docker Compose to build and start the web application and MySQL database containers in the background. `dcup`
3. **Configure Hostname Mapping:** Map the web server IP (`10.9.0.5`) to the domain `www.seed-server.com` in your `/etc/hosts` file.
:
### العربية
1. **الانتقال إلى مجلد اللاب:** فك ضغط ملفات اللاب وفتح التيرمنال داخل مجلد `.
2. **تشغيل الحاويات:** استخدام Docker Compose لبدء تشغيل حاويات تطبيق الويب وقاعدة البيانات في الخلفية.
3. **ربط اسم النطاق:** ربط عنوان الـ IP الخاص بخادم الويب (`10.9.0.5`) بالرابط `www.seed-server.com` داخل ملف `/etc/hosts` في جهازك.

## الخطوات القادمة بالترتيب:
1.تشغيل الحاويات (Containers):
بما أنكِ داخل مجلد Labsetup الآن، قومي بتشغيل حاويات الـ Docker عبر استخدام أمر التشغيل (أو اختصاره):
`dcup`
2-تعديل ملف الـ hosts (ربط الـ IP بالرابط):
افتحي نافذة تيرمنال جديدة أو استخدمي محرر النصوص لتعديل ملف الـ /etc/hosts بصلاحيات الـ root لإضافة رابط موقع اللاب:
`sudo nano /etc/hosts`

وأضيفي هذا السطر في نهاية الملف وحفظيه:
`10.9.0.5    www.seed-server.com`
```bash
# ==========================================
# الخطوة 3: الدخول إلى حاوية قاعدة البيانات (MySQL)
# Step 3: Access the MySQL database container
# ==========================================

# استخدام أمر الـ dockps لمعرفة الـ ID أو الدخول مباشرة للـ MySQL باستخدام بيانات الـ root
# (Username: root, Password: dees)
mysql -u root -pdees

# ==========================================
# الخطوة 4: حل Task 1 (استعراض قاعدة البيانات وجدول الموظفة Alice)
# Step 4: Task 1 Operations (Exploring database and querying Alice's profile)
# ==========================================

# داخل شل الـ MySQL، قم بتنفيذ الأوامر التالية بالترتيب:

# 1. اختيار قاعدة البيانات الخاصة باللاب
# use sqllab_users;

# 2. عرض الجداول الموجودة للتأكد من وجود جدول credential
# show tables;

# 3. طباعة جميع معلومات الملف الشخصي للموظفة Alice (حل التاسك المطلوب)
# SELECT * FROM credential WHERE Name = 'Alice';
```

## 3. معرفة أرقام الحاويات العاملة (List Active Containers)
استخدمي الأمر التالي لعرض الحاويات النشطة ومعرفة الـ ID الخاص بحاوية الـ MySQL `dockps` 
* (سيظهر لكِ الـ ID الخاص بحاوية الـ mysql والذي يبدأ عادةً بأحرف مثل 2d64...)
## 4. الدخول إلى شل حاوية الـ MySQL (Access MySQL Container Shell)
بما أن أمر الـ mysql غير مثبت على الجهاز الأساسي، يجب الدخول إلى داخل شل حاوية الـ MySQL مباشرة باستخدام أول أحرف من الـ ID الخاص بها:    `docksh 2d64`
## 5. الاتصال بقاعدة البيانات (Connect to MySQL Monitor)  
`mysql -u root -pdees`
## 6. تنفيذ استعلام Task 1 واستخراج بيانات Alice (Execute Task 1 Query)
بعد ظهور علامة التفاعل (mysql>)، قومي باختيار قاعدة البيانات الخاصة باللاب ثم تنفيذ الاستعلام المطلوب لإحضار معلومات الموظفة أليس:
`use sqllab_users;
SELECT * FROM credential WHERE Name = 'Alice';`

![test-1](te.png)

 ## SQL Injection Lab - Task 2.1: Web Login Attack

This guide explains how to bypass the login authentication using SQL Injection.

## 1. Objective (الهدف)
To log into the web application as the 'admin' user without knowing the password, by injecting malicious SQL code into the username field.

## 2. Steps (الخطوات)

1.  **Start the Web Container (تشغيل حاوية الويب):**
    Ensure your web container is running. If not, go to the `Labsetup` directory and run:
    ```bash
    cd Desktop/Labsetup
    dcup
    ```
* بتكوني عامليتهم قبل من التاسك الاول
2.  **Access the Login Page (الدخول لصفحة تسجيل الدخول):**
    Open your web browser inside the VM and go to:
    `http://www.seed-server.com`

3.  **Execute the Attack (تنفيذ الهجوم):**
    *   In the **Username** field, enter the following payload:
        `admin' -- `
        *(Note: The space after the two dashes is very important.)*
    *   Leave the **Password** field empty.
    *   Click **Login**.

## 3. How it Works (شرح الثغرة - لماذا نجحت؟)
The application constructs an SQL query in the background like this:
`SELECT ... FROM credential WHERE name = 'INPUT_USERNAME' AND Password = 'HASHED_PASSWORD';`

### The Injection `admin' -- ` breakdown:
*   **`'` (Single Quote):** This closes the 'name' string field in the SQL query prematurely.
*   **`-- ` (Double Dash + Space):** This is the SQL **comment** symbol. It tells the database to ignore everything that comes after it in the query.

**Resulting Query:**
The database executes: `SELECT ... FROM credential WHERE name = 'admin'` and **ignores** the password check entirely. This tricks the system into granting you access as the administrator.
 
![test-2](te-2.png)

 ## SQL Injection Lab - Task 2.2: Web Login Attack

This guide explains how to perform the SQL Injection attack using the command line instead of the web browser.

## 1. Objective (الهدف)
To perform the same SQL Injection attack as in Task 2.1, but using command-line tools like `curl` to send HTTP requests directly to the server.

## 2. Important Notes & Troubleshooting (ملاحظات هامة)

*   **Where to execute:** Do **not** execute the `curl` command inside the MySQL container (the one showing `mysql>`). You must run it from the main terminal of your virtual machine (e.g., inside the `Labsetup` directory).
*   **Why:** The `curl` command sends HTTP requests to the web application. The MySQL container is only for database management, while the web requests must be handled by the web container or the host environment.
*   **Encoding:** Since we are using special characters (`'`, space) in a URL, we must encode them:
    *   `'` becomes `%27`
    *   Space becomes `%20`

## 3. Steps (الخطوات)

1.  **Open the Terminal:** Ensure you are in your working directory (e.g., `~/Labsetup`).
2.  **Execute the Attack:** Run the following command:
    ```bash
    curl 'www.seed-server.com/unsafe_home.php?username=admin%27%20--%20&Password='
    ```
3.  **Result:** The terminal will display the raw HTML of the page. You will see employee data (like 'Alice', 'Boby', 'Admin') within the HTML tags, confirming the attack was successful.

![test-3](te-3.png)

# SQL Injection Lab - Task 2.3: Appending SQL Statements

This guide documents the attempt to perform an SQL injection attack by appending a second SQL statement to the original one.

## 1. Objective (الهدف)
The goal was to inject a second SQL command (like `UPDATE`) into the login page to modify database information, using a semicolon (`;`) to separate statements.

## 2. Methodology & Payload (المنهجية والأمر المستخدم)
To attempt this, we injected the following payload into the **Username** field:
`admin'; UPDATE credential SET PhoneNumber='12345' WHERE Name='admin'; -- `

### Payload Breakdown (شرح الأمر):
*   **`admin'`**: Closes the initial SQL string field for the username.
*   **`;`**: A semicolon used to terminate the original `SELECT` statement.
*   **`UPDATE ...`**: The second SQL statement intended to modify the database.
*   **`; -- `**: Terminating the new statement and commenting out the rest of the original query.

## 3. Findings (النتائج والملاحظات)
Upon executing this, the application returned an SQL syntax error:
*   **Observation:** The system rejected the second command and returned a syntax error message.
*   **Conclusion:** This error serves as the expected result for this task. It confirms the presence of a **countermeasure** (likely a multi-query prevention setting in the database connection or the web application code) that explicitly prohibits executing multiple SQL statements via a single web input.

---
*Note: The generated syntax error is the required evidence to prove that the application is protected against this specific type of advanced SQL injection.*

![test-4](te-4.png)

# SQL Injection Lab - Task 3.1: Modifying Salary via Edit Profile

هذا الملف يوثق خطوات تنفيذ المهمة 3.1 لاستغلال ثغرة SQL Injection في صفحة تعديل الملف الشخصي (Edit Profile) لتغيير الراتب.
This file documents the steps taken to complete Task 3.1, exploiting the SQL injection vulnerability in the Edit Profile page to modify the salary.

## 1. Objective (الهدف)
تغيير الراتب الخاص بالموظفة (Alice) في قاعدة البيانات، رغم أن واجهة الموقع لا توفر صلاحية أو خانة لتعديله.
To modify Alice's salary in the database, even though the website interface does not provide authorization or an input field to change it.

## 2. Methodology & Payload (المنهجية والأمر المستخدم)
لقد استغلينا خانة الـ (NickName) في صفحة تعديل الملف الشخصي لحقن أمر تعديل الراتب.
We exploited the 'NickName' field in the Edit Profile page to inject the salary update command.

**Payload used:**
`alice', salary='99999`

### Explanation (الشرح):
*   **`alice'`**: قمنا بإغلاق حقل الـ nickname الأصلي في استعلام الـ UPDATE لنتجنب حدوث خطأ في الـ Syntax.
    *   Closed the original 'nickname' field in the UPDATE query to avoid syntax errors.
*   **`,` (الفاصلة)**: سمحت لنا هذه الفاصلة بإضافة عمود جديد (salary) إلى استعلام الـ UPDATE.
    *   The comma allowed us to append a new column (salary) to the UPDATE statement.
*   **`salary='99999'`**: هذا هو الأمر الذي قام فعلياً بتغيير قيمة الراتب في قاعدة البيانات.
    *   This is the command that successfully updated the salary value in the database.

## 3. Steps Taken (الخطوات التي قمنا بها)
1. قمنا بتسجيل الدخول كـ (Alice) في الموقع.
   Logged into the website as 'Alice'. `alice' --` 
   * هذا الأمر سيغلق خانة الاسم ويلغي التحقق من كلمة المرور تماماً فتدخلين الحساب مباشرة!
2. توجهنا إلى صفحة تعديل الملف الشخصي (Edit Profile Page).
   Navigated to the 'Edit Profile' page.
3. قمنا بحقن الـ Payload في خانة الـ (NickName).
   Injected the payload into the 'NickName' field. `alice', salary='99999`

4. ضغطنا على زر (Save) لتنفيذ الأمر.
   Clicked the 'Save' button to execute the command.
5. تحققنا من النتيجة عبر صفحة الـ (Home) حيث ظهر الراتب الجديد (99999).
   Verified the result on the 'Home' page, where the new salary (99999) was displayed.

## 4. Observations & Notes (الملاحظات)
* تم التأكد من أن الثغرة تسمح بتعديل بيانات غير مصرح بها.
  Confirmed that the vulnerability allows unauthorized data modification.
* نجاح العملية أثبت أن المدخلات في صفحة الـ Edit Profile لا يتم تنظيفها بشكل صحيح من قبل النظام.
  The success of the operation proved that inputs in the 'Edit Profile' page are not properly sanitized by the system.

![test-5](te-5.png)

# SQL Injection Lab - Task 3.2: Modifying Other People's Salary

هذا الملف يوثق تنفيذ المهمة 3.2 لاستغلال ثغرة SQL Injection لتعديل راتب موظف آخر (Boby) إلى 1 دولار.
This file documents the completion of Task 3.2, exploiting SQL injection to modify another employee's salary (Boby) to 1 dollar.

## 1. Objective (الهدف)
تغيير راتب موظف آخر (Boby) إلى 1 دولار كعقاب، وذلك باستخدام ثغرة في صفحة تعديل الملف الشخصي.
To change another employee's salary (Boby) to 1 dollar as a punishment, using the vulnerability in the 'Edit Profile' page.

## 2. Methodology & Payload (المنهجية والأمر المستخدم)
قمنا باستخدام خانة الـ (NickName) لحقن شرط `WHERE` جديد يستهدف موظفاً آخر.
We used the 'NickName' field to inject a new `WHERE` clause targeting another employee.

**Payload used:**
`alice', salary=1 WHERE Name='Boby' -- `

### Explanation (الشرح):
*   **`alice'`**: إغلاق حقل الـ nickname لتجنب أخطاء الـ Syntax.
    *   Closed the 'nickname' field to avoid syntax errors.
*   **`salary=1`**: القيمة الجديدة لراتب المستهدف.
    *   The new salary value for the target.
*   **`WHERE Name='Boby'`**: توجيه أمر الـ UPDATE ليتم تطبيقه على Boby بدلاً من الموظف الحالي.
    *   Redirecting the UPDATE command to be applied to Boby instead of the current employee.
*   **`-- `**: تعليق ما تبقى من الاستعلام الأصلي.
    *   Commenting out the rest of the original query.

## 3. Troubleshooting & Database Access (حل المشاكل والوصول لقاعدة البيانات)
واجهنا بعض المشاكل في البداية عند محاولة الوصول لقاعدة البيانات من التيرمنال، وهذه هي الخطوات الصحيحة لتفاديها مستقبلاً:
We encountered some issues initially when trying to access the database from the terminal. Here are the correct steps to avoid them in the future:

1. **الدخول إلى الـ MySQL (Accessing MySQL):**
   استخدم الأمر التالي مع كلمة المرور الخاصة باللاب:
   Use the following command with the lab password:
   `mysql -u root -pdees`

2. **عرض قواعد البيانات (Show Databases):**
   `SHOW DATABASES;`

3. **اختيار قاعدة البيانات الصحيحة (Select the correct Database):**
   تأكدي من اختيار الاسم الصحيح (في حالتنا كان `sqllab_users`):
   Make sure to select the correct name (in our case, it was `sqllab_users`):
   `USE sqllab_users;`

4. **التحقق من البيانات (Verifying Data):**
   لعرض النتائج بعد تنفيذ الـ Payload:
   To view the results after executing the payload:
   `SELECT ID, Name, salary FROM credential;`

## 4. Conclusion (الخاتمة)
نجحت العملية وتم تغيير راتب Boby إلى 1، وتم التحقق من ذلك بنجاح من خلال استعلام قاعدة البيانات.
The operation was successful; Boby's salary was changed to 1, and this was successfully verified via a database query.

![test-6](te-6.png)


# SQL Injection Lab - Task 3.3: Modifying Other People's Password

هذا الملف يوثق تنفيذ المهمة 3.3 لاستغلال ثغرة SQL Injection لتغيير كلمة مرور موظف آخر (Boby) والتحكم بحسابه.
This file documents the completion of Task 3.3, exploiting SQL injection to change another employee's password (Boby) and take control of their account.

## 1. Objective (الهدف)
تغيير كلمة المرور الخاصة بحساب الموظف (Boby) إلى كلمة مرور معروفة لنا، لكي نتمكن من الدخول إلى حسابه والتحكم به.
To change the password of Boby's account to a password known to us, allowing us to log in and control his account.

## 2. Technical Challenge & Solution (التحدي التقني والحل)
*   **المشكلة:** النظام لا يخزن كلمة المرور بشكل نصي عادي (Plaintext)، بل يستخدم دالة التشفير `SHA1`. لذا، لا يمكننا كتابة كلمة المرور مباشرة في أمر الحقن.
    *   **Problem:** The system does not store passwords in plaintext; it uses the `SHA1` hash function. Therefore, we cannot inject the password string directly.
*   **الحل:** قمنا بحساب قيمة الـ `SHA1` لكلمة المرور التي اخترناها (`123456`) وهي: `7c4a8d09ca3762af61e59520943dc26494f8941b`.
    *   **Solution:** We calculated the `SHA1` hash for our chosen password (`123456`), which is: `7c4a8d09ca3762af61e59520943dc26494f8941b`.

## 3. Methodology & Payload (المنهجية والأمر المستخدم)
قمنا باستخدام خانة الـ (NickName) لحقن أمر تعديل كلمة المرور في قاعدة البيانات.
We used the 'NickName' field to inject the password update command into the database.

**Payload used:**
`alice', Password='7c4a8d09ca3762af61e59520943dc26494f8941b' WHERE Name='Boby' -- `

### Explanation (شرح الأمر):
*   **`alice'`**: إغلاق حقل الـ nickname الأصلي لتجنب أخطاء الـ Syntax.
    *   Closed the original 'nickname' field to avoid syntax errors.
*   **`Password='...'`**: تعيين قيمة الـ Hash الخاصة بكلمة المرور الجديدة لحساب Boby.
    *   Setting the hash value of the new password for Boby's account.
*   **`WHERE Name='Boby'`**: توجيه التعديل ليتم تطبيقه على حساب Boby.
    *   Directing the update to be applied to Boby's account.
*   **`-- `**: تعليق ما تبقى من الاستعلام الأصلي.
    *   Commenting out the rest of the original query.

## 4. Steps Taken (الخطوات التي قمنا بها)
1. قمنا بالدخول إلى صفحة تعديل الملف الشخصي (Edit Profile) بحساب Alice.
   Accessed the 'Edit Profile' page using Alice's account.
2. قمنا بحقن الـ Payload في خانة الـ (NickName).
   Injected the payload into the 'NickName' field.
3. ضغطنا على زر (Save) لتنفيذ الأمر.
   Clicked the 'Save' button to execute the command.
4. قمنا بتسجيل الخروج والدخول مجدداً باستخدام اسم المستخدم `Boby` وكلمة المرور `123456`.
   Logged out and logged back in using username `Boby` and password `123456`.
5. نجحنا في الدخول والتحكم بحساب Boby.
   Successfully logged in and controlled Boby's account.

![test-7](te-7.png)

# SQL Injection Lab - Task 4: Countermeasure (Prepared Statement)

هذا الملف يوثق تنفيذ المهمة 4 لإصلاح ثغرات SQL Injection باستخدام آلية الـ Prepared Statements.
This file documents the completion of Task 4, fixing SQL injection vulnerabilities using the Prepared Statement mechanism.

## 1. Objective (الهدف)
تحويل الكود البرمجي من صيغة معرضة للاختراق إلى صيغة آمنة تمنع هجمات الـ SQL Injection عن طريق فصل الكود عن البيانات.
Transforming the code from a vulnerable state to a secure one that prevents SQL injection attacks by separating code from data.

## 2. Methodology & Troubleshooting (المنهجية وحل المشاكل)
للوصول إلى الملف المطلوب، قمنا بالعمل داخل **حاوية الويب (Web Container)** وليس حاوية قاعدة البيانات، باتباع الخطوات التالية:
To access the required file, we worked inside the **Web Container**, not the database container, following these steps:

1. **تحديد اسم الحاوية (Identifying the Container):**
   قمنا بتشغيل أمر `docker ps` لمعرفة اسم حاوية الويب الصحيح، والذي تبين أنه `www-10.9.0.5`.
   We ran the `docker ps` command to identify the correct web container name, which was `www-10.9.0.5`.

2. **الدخول إلى الحاوية (Entering the Container):**
   استخدمنا الأمر التالي للدخول إلى الحاوية:
   We used the following command to enter the container:
   `docker exec -it www-10.9.0.5 /bin/bash`

3. **الوصول للملف (Accessing the File):**
   انتقلنا إلى مجلد الدفاع المطلوب:
   We navigated to the target defense folder:
   `cd /var/www/SQL_Injection/defense/`

## 3. The Fix (الإصلاح البرمجي)
قمنا بتعديل ملف `unsafe.php` باستخدام محرر النصوص `nano`. استبدلنا الاستعلام المباشر بـ `Prepared Statement` لضمان الحماية:
We modified the `unsafe.php` file using the `nano` editor. We replaced the direct query with a `Prepared Statement` to ensure security:

**Code Before (قبل التعديل):**
```php
$result = $conn->query("SELECT id, name, eid, salary, ssn FROM credential WHERE name= '$input_uname' and Password= '$hashed_pwd'");
```

**Code After (بعد التعديل):**
```php
$stmt = $conn->prepare("SELECT id, name, eid, salary, ssn FROM credential WHERE name = ? and Password = ?");
$stmt->bind_param("ss", $input_uname, $hashed_pwd);
$stmt->execute();
$result = $stmt->get_result();
```
![test-8](te-8.png)


## 4. Conclusion (الخاتمة)
بعد حفظ التعديلات، قمنا بتجربة الموقع مرة أخرى. النتيجة كانت فشل أي محاولة حقن (SQL Injection)، مما يؤكد نجاح آلية الـ Prepared Statements في حماية الموقع.
After saving the changes, we tested the site again. All SQL injection attempts failed, confirming the success of the Prepared Statement mechanism in securing the site.

![test-9](te-9.png)
