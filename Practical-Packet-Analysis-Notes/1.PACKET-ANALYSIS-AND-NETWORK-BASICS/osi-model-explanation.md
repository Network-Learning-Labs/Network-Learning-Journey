# Networking Protocols

قبل ما نبدأ بتحليل الـPackets، لازم نفهم **كيف الأجهزة أصلًا بتتفق على طريقة التواصل مع بعضها**.

الشبكات الحديثة فيها أجهزة وأنظمة مختلفة جدًا:

```text
Computer A
     ↓
Windows
     ↓
Network
     ↓
Router / Switch
     ↓
Computer B
     ↓
Linux / Other System
```

كل جهاز ممكن يكون مختلف عن الثاني في الـOperating System والـHardware والـApplication.

فكيف يستطيعوا التواصل مع بعض؟

هنا يأتي دور **Network Protocols**.

---

## What Is a Network Protocol?

الـ**Network Protocol** هو مجموعة من القواعد التي تحدد **كيف يتم التواصل وتبادل البيانات بين الأجهزة**.

ممكن نشبهه بلغة مشتركة.

إذا جهازان يريدان التواصل، لازم يكون بينهم اتفاق على أشياء مثل:

* كيف يتم إرسال البيانات؟
* كيف يعرف الطرف المستقبل أن البيانات وصلت؟
* ماذا يحدث إذا حدث خطأ؟
* كيف يتم تقسيم البيانات؟
* كيف يتم التحكم بسرعة الإرسال؟

لذلك الـProtocol لا يعني فقط "اسم البروتوكول"، بل يعني **rules that define communication behavior**.

---

## Common Network Protocols

من البروتوكولات التي سنقابلها كثيرًا:

```text
TCP
IP
ARP
DHCP
```

وكل واحد منها له وظيفة مختلفة.

مثلًا:

### IP

يهتم بالـ**addressing and routing**.

بشكل مبسط:

```text
Source IP
     ↓
   Network
     ↓
Destination IP
```

---

### TCP

يهتم بتوفير **reliable transport** بين الطرفين.

مثلًا يمكنه استخدام:

* Sequence Numbers
* Acknowledgments
* Retransmissions
* Flow Control

وهذه الأشياء سنراها لاحقًا داخل الـTCP packets.

---

### ARP

يساعد الأجهزة في شبكة IPv4 المحلية على معرفة:

```text
IPv4 Address
     ↓
MAC Address
```

---

### DHCP

يساعد الجهاز في الحصول على إعدادات الشبكة تلقائيًا، مثل:

```text
IP Address
Subnet Mask
Default Gateway
DNS Server
```

إذن كل Protocol له **وظيفة محددة**.

---

# What Is a Protocol Stack?

أحيانًا لا يعمل Protocol واحد لوحده.

مجموعة من الـProtocols تعمل معًا لتحقيق عملية اتصال كاملة تسمى:

**Protocol Stack**

مثلًا عندما نفتح موقعًا باستخدام HTTPS، يمكن أن يكون لدينا بشكل مبسط:

```text
HTTPS
  ↓
TCP
  ↓
IP
  ↓
Ethernet
```

كل Protocol هنا مسؤول عن جزء مختلف من عملية الاتصال.

وهذا مهم جدًا في Packet Analysis، لأن Wireshark يستطيع أن يوضح لنا هذه الطبقات داخل الـTraffic.

---

# Protocols Can Be Simple or Complex

مش كل الـProtocols بنفس درجة التعقيد.

بعضها يقوم بوظيفة بسيطة جدًا، بينما بعضها يحتوي على الكثير من القواعد والآليات.

والسبب هو أن **وظيفة الـProtocol هي التي تحدد ما يحتاجه**.

إذا كان البروتوكول يحتاج إلى التعامل مع:

* فقدان البيانات
* السرعة
* الأخطاء
* إعادة الإرسال
* الأمان

فسيحتاج إلى آليات أكثر.

---

# What Problems Do Protocols Need to Handle?

المؤلف هنا يبدأ يعطينا مجموعة من المشاكل التي يجب أن تتعامل معها البروتوكولات.

لكن مهم جدًا نفهم:

> **ليس كل Protocol مسؤولًا عن كل هذه الوظائف.**

كل Protocol يوفر الآليات التي يحتاجها لتحقيق هدفه.

خلينا نفهمها واحدة واحدة.

---

## 1. Flow Control

**Flow Control** يعني التحكم في سرعة إرسال البيانات بحيث لا يرسل الـSender بيانات أسرع من قدرة الـReceiver على التعامل معها.

تخيل:

```text
Sender
  ↓↓↓↓↓↓↓↓↓↓↓
Receiver
```

إذا كان الـSender يرسل بسرعة أكبر من قدرة الـReceiver، ممكن تتراكم البيانات أو تضيع.

لذلك يمكن أن يرسل الـReceiver معلومات تساعد الـSender على:

```text
Slow down
    أو
Speed up
```

أي:

> "خفف سرعة الإرسال"

أو:

> "يمكنك الاستمرار بهذه السرعة."

ومن الأمثلة المهمة التي سنراها لاحقًا **TCP Flow Control**.

---

# 2. Packet / Data Acknowledgment

المؤلف يسميها **Packet Acknowledgment**.

الفكرة:

عندما يرسل جهاز بيانات إلى جهاز آخر، كيف يعرف الـSender أن الـReceiver استلمها؟

يمكن للـReceiver أن يرسل رسالة تؤكد الاستلام.

بشكل مبسط:

```text
Sender
   |
   |---- Data ---->
   |
Receiver
   |
   |---- ACK -----> 
```

الـACK تعني:

> "استلمت البيانات."

وهذا مفهوم مهم جدًا في TCP.

وعندما نحلل TCP في Wireshark، سنرى الـACKs ونستخدمها لفهم حالة الاتصال.

---

# 3. Error Detection

ماذا لو تغيرت البيانات أثناء انتقالها؟

نحتاج إلى طريقة تجعل النظام قادرًا على اكتشاف أن البيانات التي وصلت **ليست كما أُرسلت**.

لذلك تستخدم بعض البروتوكولات mechanisms تساعد على اكتشاف الأخطاء.

بشكل مبسط:

```text
Sender
   |
   | Data + Verification Information
   ↓
Network
   ↓
Receiver
   |
   | Check
   ↓
Valid / Error
```

الفكرة هنا هي:

> **Detect that something went wrong.**

لاحظي أن **Error Detection** لا يعني بالضرورة إصلاح الخطأ.

هو فقط يكتشف وجود مشكلة.

---

# 4. Error Correction

بعد اكتشاف الخطأ، نحتاج أحيانًا إلى طريقة للتعامل معه.

ومن الطرق الممكنة:

```text
Data Lost / Damaged
        ↓
   Detect Problem
        ↓
   Retransmit Data
        ↓
   Receive Again
```

أي أن البيانات التي فُقدت أو تضررت يمكن إعادة إرسالها.

وهنا لازم نفرق:

```text
Error Detection
→ اكتشاف المشكلة

Error Correction
→ التعامل مع المشكلة / إصلاح أثرها
```

وفي بعض البروتوكولات، يكون الحل هو **Retransmission**.

مثل TCP.

---

# 5. Segmentation

ماذا لو كان لدينا كمية كبيرة جدًا من البيانات؟

بدل إرسالها ككتلة واحدة ضخمة، يمكن تقسيمها إلى أجزاء أصغر.

مثلًا:

```text
Large Data
     ↓
-------------------------
| Part 1 | Part 2 | Part 3 |
-------------------------
```

وهذه العملية تسمى **Segmentation**.

والفكرة مهمة جدًا لفهم Packet Analysis، لأن البيانات التي نراها في الشبكة ليست بالضرورة "الملف كاملًا" في Packet واحدة.

قد نرى مجموعة من الـPackets التي تحمل أجزاء من البيانات.

ولهذا السبب لاحقًا لن نحلل Packet واحدة دائمًا.

أحيانًا نحتاج إلى النظر إلى **sequence of packets** لفهم ما حدث.

---

# 6. Data Encryption

أحيانًا لا نريد أن تكون البيانات المرسلة عبر الشبكة قابلة للقراءة من أي شخص يستطيع الوصول إلى الـTraffic.

هنا يأتي دور **Encryption**.

بشكل مبسط:

```text
Readable Data
      ↓
   Encryption
      ↓
Encrypted Data
      ↓
    Network
      ↓
   Decryption
      ↓
Readable Data
```

وتستخدم عملية التشفير **cryptographic keys** لحماية البيانات.

وهذا مهم جدًا بالنسبة لنا في Packet Analysis.

لأن Wireshark قد يستطيع أن يخبرنا:

```text
Who is communicating?
Which IPs?
Which ports?
Which protocol?
How much traffic?
When did it happen?
```

لكن إذا كانت البيانات مشفرة، فقد لا نستطيع قراءة محتوى الـApplication نفسه.

وهذا فرق مهم بين:

**Seeing the traffic**

و

**Understanding the encrypted content.**

---

# 7. Data Compression

أحيانًا تكون البيانات كبيرة وفيها معلومات متكررة.

يمكن استخدام **Data Compression** لتقليل حجم البيانات التي يتم إرسالها.

بشكل مبسط:

```text
Large Data
    ↓
Compression
    ↓
Smaller Data
    ↓
Network
```

الفائدة:

* تقليل كمية البيانات المنقولة.
* تقليل bandwidth usage.
* تحسين كفاءة النقل في بعض الحالات.

الفكرة الأساسية هي التخلص من **redundant information** بطريقة تسمح بإعادة بناء البيانات الأصلية عند الحاجة.

---

# Putting Everything Together

الآن نقدر نفهم لماذا المؤلف ذكر هذه الوظائف.

عندما نقول:

> **Network Protocols define how communication happens.**

فهذا لا يعني فقط:

> "Send data from A to B."

بل قد يحتاج الاتصال إلى التعامل مع مجموعة كبيرة من المشاكل:

```text
                 Network Protocol
                        │
       ┌────────────────┼────────────────┐
       ↓                ↓                ↓
 Flow Control      Acknowledgment    Error Detection
       ↓                ↓                ↓
       └──────────── Communication ──────┘
                        │
                Segmentation
                        │
              Encryption / Compression
```

لكن تذكري:

> **A single protocol does not necessarily provide all of these functions.**

كل Protocol له وظيفته، وقد تتعاون عدة Protocols معًا ضمن **Protocol Stack**.

---

# Why Does This Matter for Packet Analysis?

وهنا نرجع لهدف الكتاب الأساسي.

لما نفتح Wireshark لاحقًا ونشوف:

```text
Ethernet
   ↓
IP
   ↓
TCP
   ↓
Application Protocol
```

ما بدنا نشوف مجرد أسماء.

بدنا نفهم:

```text
Why was this packet sent?
        ↓
Which protocol generated it?
        ↓
What function does that protocol provide?
        ↓
What information is inside it?
        ↓
What happened before and after it?
```

مثلًا إذا شفنا TCP ACK:

ما بنقول فقط:

> "هاي ACK."

بل بنفكر:

> "الـTCP protocol يستخدم acknowledgments لمتابعة استلام البيانات، فلذلك وجود هذه الـpacket جزء من آلية الاتصال."

وإذا شفنا Retransmission:

> "في Packet انبعثت مرة ثانية، فلماذا؟ هل كان هناك فقدان؟ هل لم يصل الـACK؟ ما الذي حدث في الـTraffic؟"

**وهنا يتحول Wireshark من مجرد شاشة فيها Packets إلى مصدر للأدلة.**

---

# The Big Picture

الترتيب الذي نريد أن نبنيه في فهمنا هو:

```text
Applications
     ↓
Network Protocols
     ↓
Protocol Stack
     ↓
Packets / Frames
     ↓
Network Traffic
     ↓
Wireshark
     ↓
Packet Analysis
     ↓
Evidence
     ↓
Understanding What Happened
```

## Key Takeaway

> **Protocols are the rules that allow different systems to communicate. Different protocols solve different communication problems, and multiple protocols can work together as a protocol stack.**

And for Packet Analysis:

> **We study protocols because packets are not random pieces of data. They are the visible result of protocols performing specific jobs during network communication.**


# The Seven-Layer OSI Model

أكيد. هون بالذات لازم نكون دقيقين، لأن هدفنا مش نحفظ أسماء الـ7 Layers فقط؛ بدنا نفهم ليش المؤلف جاب الـOSI Model الآن، وكيف رح يساعدنا لاحقًا في Packet Analysis.

## The Seven-Layer OSI Model
بعد ما فهمنا أن الـNetwork Protocols لها وظائف مختلفة، يأتي السؤال:

كيف ننظم هذه البروتوكولات والوظائف حتى نفهم عملية الاتصال كاملة؟
هنا يأتي دور OSI Model.

## What Is the OSI Model?
الـOSI (Open Systems Interconnection) Model هو reference model تم وضعه لتنظيم عملية Network Communication إلى سبع Layers.

### الفكرة الأساسية:
بدل ما ننظر إلى Network Communication كعملية ضخمة ومعقدة، نقسمها إلى أجزاء، وكل Layer يكون لها دور محدد.

- Layer 7 — Application
- Layer 6 — Presentation
- Layer 5 — Session
- Layer 4 — Transport
- Layer 3 — Network
- Layer 2 — Data Link
- Layer 1 — Physical

وهذا التقسيم يجعلنا قادرين على سؤال:

> What is happening at this layer?

بدل ما نحاول نفهم كل شيء مرة واحدة.

## Why Seven Layers?
تخيلي أن جهازك يريد إرسال بيانات إلى جهاز آخر.
هناك أشياء كثيرة تحدث:

```text
Application
     ↓
Data representation
     ↓
Session
     ↓
Transport
     ↓
IP addressing
     ↓
Local network delivery
     ↓
Physical transmission
```

لو حاولنا دراسة كل هذه الأشياء كعملية واحدة، سيكون من الصعب جدًا فهمها.
لذلك الـOSI Model يقول:

> Let's divide the communication process into layers, where each layer has a specific responsibility.

## The Top and Bottom of the Model
في أعلى الـOSI Model عندنا:

### Layer 7 — Application
هذه الطبقة الأقرب إلى البرامج التي يستخدمها المستخدم للوصول إلى Network Resources.
مثلًا:
- Web Browser
- Email Application
- File Transfer Application

**لكن انتبهي:**
Application Layer لا تعني "أي برنامج موجود على الكمبيوتر".
المقصود هو وظائف وProtocols الشبكة التي تتعامل معها التطبيقات.

أما في الأسفل:

### Layer 1 — Physical
هذه الطبقة مس루ولة عن الطريقة الفيزيائية التي تنتقل بها البيانات.
مثل:
- Electrical signals
- Optical signals
- Radio signals

وهنا فعلًا تنتقل البيانات عبر الـPhysical Medium.
لذلك نستطيع التفكير فيها هكذا:

```text
        User / Application
               ↓
        Application Layer
               ↓
             ...
               ↓
        Physical Layer
               ↓
          Physical Medium
```

## The Layers Work Together
وهذه نقطة مهمة جدًا.
لا يعني وجود 7 Layers أن كل Layer تعمل لوحدها.
بل كل Layer تؤدي وظيفتها وتتعاون مع الـLayer الموجودة بجانبها.
عند الإرسال، يمكن أن نفكر فيها بشكل مبسط:

```text
Application Data
      ↓
Transport Information
      ↓
Network Information
      ↓
Data Link Information
      ↓
Physical Transmission
```

كل Layer تضيف أو تتعامل مع المعلومات التي تحتاجها حتى تتم عملية الاتصال.
وهذا هو الأساس الذي سيقودنا لاحقًا إلى مفهوم:
**Encapsulation**

## Why Is This Important for Packet Analysis?
هنا تحديدًا يظهر سبب وجود هذا الجزء في كتاب Practical Packet Analysis.
لما نفتح Wireshark ونشوف Packet، ممكن نشوف شيئًا مثل:

```text
Ethernet II
    ↓
IPv4
    ↓
TCP
    ↓
HTTP / TLS / ...
```

إذا ما كنا فاهمين الـLayers، ممكن تكون هذه مجرد أسماء.
لكن مع الـOSI Model نبدأ نفهم:

- Application-related information
- Transport
- Network
- Data Link

فنبدأ نسأل:
- ما وظيفة هذا الجزء؟
- في أي Layer يعمل؟
- ما المشكلة التي يحاول حلها؟
- ما المعلومات التي أضافها؟
- كيف يؤثر على الاتصال؟

وهذا هو التفكير الذي نحتاجه في Packet Analysis.

## Is the OSI Model a Strict Rule?
لا. وهذه ملاحظة مهمة جدًا من المؤلف.
الـOSI Model هو reference model / industry standard framework يساعدنا على تنظيم وفهم Network Communication.
لكنه ليس قانونًا يجبر كل Protocol Developer على الالتزام به حرفيًا.
يعني لا نقول:
> "كل Protocol لازم يطابق الـOSI Model بنسبة 100%."

لأن هناك Networking Models أخرى، مثل DoD / TCP-IP Model.
لكن الكتاب اختار أن يستخدم مفاهيم الـOSI Model لأنه مفيد جدًا في فهم وتقسيم وظائف الشبكة.

## The Important Idea
إذن لا أريد من الطالب أن يخرج من هذا الجزء فقط حافظًا:
- 7 Application
- 6 Presentation
- 5 Session
- 4 Transport
- 3 Network
- 2 Data Link
- 1 Physical

بل أريد أن يفهم:
> The OSI Model is a way of organizing network communication into layers, so we can understand what each part of the communication process is responsible for.

وبالنسبة لنا في هذا الكتاب:

```text
Network Communication
        ↓
Divide it into Layers
        ↓
Understand each Layer's role
        ↓
Understand the Protocols
        ↓
Understand the Packets
        ↓
Analyze the Traffic
```

## The Big Picture
وهذا هو الربط بين كل شيء درسناه حتى الآن:

```text
Different Devices
       ↓
Need to Communicate
       ↓
Network Protocols
       ↓
Protocols Have Different Functions
       ↓
OSI Model Organizes These Functions
       ↓
Layers Work Together
       ↓
Data Is Encapsulated
       ↓
Frames / Packets Travel Through the Network
       ↓
Wireshark Captures the Traffic
       ↓
We Analyze the Packets
```

## Key Takeaway
The OSI Model gives us a structured way to understand network communication. It does not describe every real-world protocol perfectly, but it gives us a useful framework for understanding where different networking functions and protocols fit.

وهذا مهم جدًا للطالب: الـOSI Model مش الهدف النهائي؛ هو الخريطة اللي رح نستخدمها حتى نعرف نقرأ الـNetwork Traffic لما نوصل إلى Wireshark.


# شرح طبقات OSI السبع

إحنا عرفنا أن الـOSI Model يقسم عملية الاتصال بالشبكة إلى 7 Layers، وكل Layer إلها وظيفة معينة.

- Layer 7 — Application
- Layer 6 — Presentation
- Layer 5 — Session
- Layer 4 — Transport
- Layer 3 — Network
- Layer 2 — Data Link
- Layer 1 — Physical

المهم هون ما نحفظ أسماء الطبقات فقط.
بدنا نسأل مع كل طبقة:
> شو مسؤوليتها؟ وليش أصلًا احتجنا هاي الطبقة؟

---

## Layer 7 — Application Layer
هاي أعلى طبقة، وهي الأقرب للـApplications اللي بنستخدمها.
لكن انتبهي:
Application Layer لا تعني كل البرامج الموجودة على الجهاز.
المقصود هو الخدمات والبروتوكولات الشبكية التي تستخدمها التطبيقات للتواصل.

مثلاً لما تفتحي موقع:
```text
Web Browser
     ↓
HTTP / HTTPS
     ↓
Transport Layer
```

فالـBrowser يحتاج Application Protocol مثل HTTP أو HTTPS حتى يتواصل مع Web Server.

### أمثلة على Application-layer protocols:
- HTTP
- DNS
- DHCP
- FTP
- SMTP

### طيب ليش هذا مهم في Wireshark؟
لما نفتح Packet في Wireshark ممكن نشوف:
```text
Ethernet II
     ↓
IPv4
     ↓
TCP
     ↓
HTTP
```

فإحنا بنقدر نقول:
> HTTP هو الـApplication Protocol المستخدم في هذا الاتصال.

ومن هون نسأل:
- شو التطبيق أو الخدمة اللي بتتواصل؟
- شو نوع الـApplication Protocol؟

---

## Layer 6 — Presentation Layer
هون الفكرة شوي مختلفة.
الـPresentation Layer تهتم بـ **طريقة تمثيل البيانات**.
يعني البيانات التي وصلت تحتاج أحيانًا إلى:
- Encoding
- Decoding
- Encryption
- Decryption
- Compression

فكري فيها كأنها الطبقة التي تساعد على جعل البيانات بالصيغة المناسبة حتى يفهمها الطرف الآخر.

مثلاً:
```text
Data
 ↓
Encoding / Transformation
 ↓
Application
```

### مثال مهم: Encryption
لو البيانات مشفرة:
```text
Encrypted Traffic
       ↓
Wireshark
       ↓
نشوف IPs, Ports, Timing, Packet Size, TCP information
```

لكن ممكن ما نقدر نقرأ محتوى البيانات نفسه لأنه مشفر.
وهون لازم نفرق:
> Encryption لا يعني أن الـPackets اختفت.
> إحنا ما زلنا نشوف الـtraffic، لكن محتوى الـApplication قد يكون غير مقروء.

---

## Layer 5 — Session Layer
الـSession Layer مسؤولة عن إدارة جلسة الاتصال بين الطرفين.
يعني بدل ما نفكر فقط:
> "في جهازين بيتبادلوا Data."

نفكر:
> "في Session أو جلسة اتصال يتم إنشاؤها وإدارتها ثم إنهاؤها."

بشكل مبسط:
```text
Establish Session
       ↓
Communication
       ↓
Manage Session
       ↓
Terminate Session
```

يعني من وظائفها المفاهيمية:
- إنشاء الجلسة
- إدارة الجلسة
- المحافظة على الجلسة
- إنهاء الجلسة

والكتاب يتكلم أيضًا عن طريقة اتجاه الاتصال، مثل:
- Full-Duplex
- Half-Duplex

### لكن انتبهي لنقطة مهمة جدًا
في الشبكات الحديثة، مش شرط نلاقي Protocol منفصل حرفيًا اسمه "Session Layer Protocol".
الـOSI Model **Reference Model**.
يعني هو طريقة لتنظيم وفهم الوظائف، وليس شرطًا أن كل Protocol في الواقع يلتزم بالـ7 Layers بشكل منفصل تمامًا.

---

## Layer 4 — Transport Layer
هاي من أهم الطبقات بالنسبة إلنا في Packet Analysis.
وظيفتها الأساسية:
> توفير Transport Services بين الـEndpoints.

أشهر بروتوكولات الـTransport:
- TCP
- UDP

لكن TCP وUDP مش نفس الشيء.

### TCP
TCP يوفر آليات تساعد على جعل الاتصال Reliable.
من الأشياء التي نقدر نشوفها في TCP:
- Sequence Numbers
- Acknowledgments
- Retransmissions
- Flow Control
- Flags
- Ports

#### شو يعني Segmentation؟
تخيلي الـApplication عندها كمية كبيرة من البيانات.
مش شرط تنرسل كلها كقطعة واحدة.
ممكن يتم تقسيمها:
```text
Application Data
       ↓
     TCP
       ↓
Segment 1
Segment 2
Segment 3
Segment 4
```
وعلى الطرف الثاني يتم التعامل معها كجزء من نفس الـData Stream.

#### شو يعني Flow Control؟
معناها ببساطة:
الـSender لازم يراعي قدرة الـReceiver على استقبال البيانات.
يعني ما بصير:
```text
Sender
  ↓↓↓↓↓↓↓↓↓↓↓
Receiver
```
إذا الـReceiver مش قادر يتعامل مع هذا المعدل.
فـTCP عنده آليات تساعد في تنظيم كمية البيانات المرسلة.

#### شو يعني Acknowledgment؟
الـReceiver ممكن يخبر الـSender:
> "وصلني هذا الجزء من البيانات."

وهذا جزء أساسي من طريقة TCP في متابعة البيانات المرسلة.

#### شو يعني Retransmission؟
إذا TCP اكتشف أن البيانات المتوقعة لم تصل بالشكل المطلوب، يمكن أن تتم إعادة إرسالها.
وهذا مهم جدًا في Wireshark.
بدل ما شخص يحكيلك:
> "الشبكة بطيئة."

إحنا بنقدر نبحث عن Evidence:
- هل يوجد Retransmissions؟
- هل يوجد Delay؟
- هل الـACKs طبيعية؟
- هل هناك مشكلة في Window؟

وهون بدأنا فعلًا نستخدم Packet Analysis.

---

## Layer 3 — Network Layer
هاي الطبقة مسؤولة بشكل أساسي عن:
> Logical Addressing + Routing بين الشبكات.

وأشهر مثال: **IP**
يعني:
- IPv4
- IPv6

هون بنتعامل مع الـIP Address.
مثلاً:
```text
PC
192.168.1.10
      ↓
   Router
      ↓
192.168.2.20
Server
```

### ليش احتجنا Layer 3؟
لأن الشبكات مش كلها شبكة واحدة.
عندنا:
```text
Network A
     ↓
   Router
     ↓
Network B
```
والـRouter يحتاج يعرف:
> إلى أي Network لازم أرسل الـPacket؟

وهنا يأتي دور الـRouting.

### Router
الـRouter يعمل بشكل أساسي في Layer 3 لأنه يتعامل مع معلومات Layer 3، وأهمها Destination IP Address لاتخاذ قرار الـForwarding.

### Layer 3 في Wireshark
ممكن نشوف:
```text
Ethernet II
     ↓
IPv4
     ↓
TCP
```
داخل IPv4 نقدر نشوف أشياء مثل:
- Source IP
- Destination IP
- TTL
- Protocol

فنسأل:
- مين أرسل الـPacket؟
- لمين رايحة؟
- شو الـTransport Protocol المستخدم؟

---

## Layer 2 — Data Link Layer
هون بننزل من مستوى IP إلى مستوى Local Network.
الـData Link Layer تهتم بتوصيل البيانات عبر الـLocal Link.
في Ethernet، بنتعامل مع: **MAC Address**
مثل:
- Source MAC
- Destination MAC

### الفرق بين Layer 2 و Layer 3
هاي من أهم النقاط اللي لازم تثبت عندك:
- **Layer 2** ↓ Local Delivery ↓ MAC Address
- **Layer 3** ↓ Communication Between Networks ↓ IP Address

مثلاً:
```text
PC A
IP:  192.168.1.10
MAC: AA:AA:AA:AA:AA:AA

       ↓

    Switch

       ↓

PC B
IP:  192.168.1.20
MAC: BB:BB:BB:BB:BB:BB
```

الـSwitch يستخدم معلومات Layer 2، خصوصًا الـMAC Address، حتى يقرر على أي Port يرسل الـFrame.

### Switch
الـSwitch يعمل بشكل أساسي في Layer 2.
يتعلم:
> `MAC Address → Port`

مثلاً بشكل مبسط:
- `AA:AA:AA → Port 1`
- `BB:BB:BB → Port 2`
- `CC:CC:CC → Port 3`

فلما توصل Frame للـSwitch، يستطيع استخدام الـMAC Address لمعرفة أين يرسلها.

---

## Layer 1 — Physical Layer
هاي أقل طبقة.
وهون بنوصل إلى الشيء الفيزيائي الحقيقي الذي تنتقل من خلاله الإشارة.
مثل:
- Electrical Signal
- Optical Signal
- Radio Signal
- Cable
- Fiber
- Connectors
- Network Interface

يعني:
```text
Data
 ↓
Network Interface
 ↓
Physical Signal
 ↓
Cable / Fiber / Wireless
 ↓
Receiving Device
```

فالـPhysical Layer تهتم بكيف تنتقل الإشارة فعليًا.

---

## الآن اربطي الطبقات كلها مع بعض
تخيلي أنك فتحتي موقع.
الـApplication بدها تتواصل:
```text
Application
      ↓
Presentation
      ↓
Session
      ↓
Transport
      ↓
Network
      ↓
Data Link
      ↓
Physical
```

كل Layer عندها وظيفة مختلفة.
ممكن تحفظي الفكرة بهذا الشكل:

| Layer | الفكرة الأساسية |
| :--- | :--- |
| **7 Application** | خدمات الشبكة التي تستخدمها التطبيقات |
| **6 Presentation** | تمثيل وتحويل البيانات |
| **5 Session** | إدارة جلسة الاتصال |
| **4 Transport** | نقل البيانات بين الـEndpoints |
| **3 Network** | IP + Routing |
| **2 Data Link** | Local Delivery + MAC |
| **1 Physical** | الإشارات والوسط الفيزيائي |

---

## طيب شو علاقتها بالـWireshark؟
هون بتبدأ الصورة المهمة فعلًا.
لما Wireshark يعرض Packet ممكن تشوفي:
```text
Ethernet II
     ↓
IPv4
     ↓
TCP
     ↓
Application Protocol
```

إحنا ما بنشوف أربع أشياء عشوائية.
إحنا بنشوف وظائف مختلفة اجتمعت حتى يتم الاتصال.

بشكل مبسط:
- **Application Protocol** → شو الخدمة؟
- **Transport** → كيف يتم النقل بين الـEndpoints؟
- **Network** → كيف يتم الـLogical Addressing والـRouting؟
- **Data Link** → كيف يتم الـLocal Delivery؟
- **Physical** → كيف تنتقل الإشارة؟

---

## وهون بيبدأ أسلوب الـTroubleshooting
لو حدا حكى لك:
> "الإنترنت بطيء."

إحنا ما بنجاوب مباشرة:
> "أكيد المشكلة من الإنترنت."
> أو "أكيد السيرفر بطيء."

بل بنبدأ نبحث عن Evidence:

- **Application** ↓ هل الـApplication Protocol طبيعي؟
- **Transport** ↓ هل يوجد Retransmissions؟ هل يوجد Delay؟ هل الـACKs طبيعية؟
- **Network** ↓ هل الـPackets تصل إلى الـDestination؟ هل الـRouting صحيح؟
- **Data Link** ↓ هل الـLocal Delivery تعمل بشكل صحيح؟
- **Physical** ↓ هل يوجد مشكلة في الـPhysical Connection؟

وهذا هو بالضبط الانتقال من:
> **Guessing → Evidence**

---

## أهم فكرة أريدك تفهميها
لا تحفظي الـOSI بهذا الشكل فقط:
- Layer 4 = Transport
- Layer 3 = Network
- Layer 2 = Data Link
هذا حفظ أسماء فقط.

بدنا نرتقي لمستوى ثاني:
> Layer ↓ Responsibility ↓ Protocol ↓ Mechanism ↓ Observable Behavior ↓ Packet Evidence

مثلاً لما تشوفي TCP في Wireshark، ما يكون تفكيرك:
> "آه TCP، Layer 4." خلص.

بل:
> "TCP موجود في Transport Layer، ويقدم آليات للنقل بين الـEndpoints. أقدر أراقب الـPorts والـSequence Numbers والـACKs والـFlags والـRetransmissions حتى أفهم ماذا يحدث في الاتصال."

ولما تشوفي IPv4:
> "IPv4 موجود في Network Layer. عندي Source IP وDestination IP ومعلومات أخرى تساعدني أفهم الـLogical Addressing وكيف تسير الـPacket."

هون بالضبط بتتحولي من طالبة تحفظ Networking إلى شخص بدأ يقرأ الـNetwork من خلال الـPackets.

