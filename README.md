# OVS Tracing
<div dir="rtl">
<div style: align="center">
<figure>
  <img src="https://upload.wikimedia.org/wikipedia/commons/5/59/Open_vSwitch_Logo.svg" alt="OVS Logo" />
</figure>
</div>

- [قسمت اول: کلیات OVS](#----------------ovs)
  * [توضیح مختصر در مورد OVS:](#--------------------ovs-)
  * [نقاط قوت OVS:](#---------ovs-)
  * [نقاط ضعف OVS:](#---------ovs-)
- [قسمت دوم: معماری  OVS](#------------------ovs)
  * [پیش نیازهای OVS](#------------ovs)
  * [پیاده سازی OVS:](#-----------ovs-)
  * [معماری Namespace و OVS:](#-------namespace---ovs-)
- [قسمت سوم: سناریوی Ping](#------------------ping)
  * [مقدمات سناریو Ping:](#--------------ping-)
  * [برای آشنایی با Function هایی که در زمان اجرای دستور ping صدا زده می شوند:](#---------------function-----------------------------ping-----------------)
  * [ایجاد kprobeهای اختصاصی](#------kprobe-----------)
  * [دستور Perf برای سناریو Ping:](#------perf-------------ping-)
  * [رویداد ovs_vport_recieve](#-------ovs-vport-recieve)
    + [آنالیز این رویداد با دستور perf](#---------------------------perf)
    + [آنالیز این رویداد با دستور ftrace](#---------------------------ftrace)
  * [رویداد ovs\_vport\_send:](#-------ovs--vport--send-)

<p></p>

# قسمت اول: کلیات OVS 

## توضیح مختصر در مورد OVS:
<div dir="rtl">
Open vSwitch یا به اختصار OVS، یک پیاده‌سازی منبع باز از یک سوئیچ چند لایه مجازی  توزیع شده است هدف اصلی [Open vSwitch](https://en.wikipedia.org/wiki/Open_vSwitch)ارائه یک پشته سوئیچ برای محیط‌های مجازی‌سازی سخت‌افزاری است، در حالی که از چندین پروتکل و استاندارد مورد استفاده در شبکه‌های کامپیوتری پشتیبانی می‌کند.
</div>
## نقاط قوت OVS:

1. پشتیبانی از استانداردها: OVS از چندین پروتکل و استاندارد مورد استفاده در شبکه‌های کامپیوتری پشتیبانی می‌کند.
2. قابلیت اتوماسیون شبکه: OVS قابلیت اتوماسیون شبکه را از طریق افزونه‌های برنامه‌نویسی فراهم می‌کند.
3. پشتیبانی از توزیع شفاف: OVS طوری طراحی شده است که پشتیبانی از توزیع شفاف در سرورهای فیزیکی متعدد را فراهم می‌کند.

## نقاط ضعف OVS:

1. پیچیدگی در پیکربندی
2.  نیاز به دانش فنی خاص برای استفاده بهینه

## ساختار OVS

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/ovs-struct.jpg" alt="ovs-struct " />
  <figcaption><em>تصویری از ساختار OVS</em></figcaption>
</figure>
</div>


<p></p>

**جدول جریان (Flow Table):**

- در OVS، جدول جریان، یک ساختار داده کلیدی است که قوانین (یا جریان‌ها) برای چگونگی رسیدگی به بسته‌های شبکه‌ای را نگهداری می‌کند.
- هر قانون در جدول جریان شامل یک مجموعه از شرایط (مانند منبع و مقصد IP، پورت، نوع پروتکل) و یک سری عملیات (مانند ارسال بسته به یک پورت خاص، دور ریختن بسته، یا اعمال تغییراتی روی بسته) است.
- جدول جریان به OVS اجازه می‌دهد که تصمیمات سریع در مورد نحوه پردازش بسته‌ها بر اساس قوانین از پیش تعریف شده بگیرد.

**جریان (Flow):**

- یک جریان در OVS، یک قاعده یا مجموعه‌ای از دستورالعمل‌ها است که تعیین می‌کند چگونه بسته‌های شبکه‌ای باید پردازش شوند.
- جریان‌ها معمولاً شامل شرایطی برای مطابقت با بسته‌های خاص (مثل هدرهای IP یا TCP) و اقداماتی که بر روی بسته‌های مطابقت یافته انجام می‌شود (مانند ارسال به یک پورت خاص یا تغییر سربرگ) هستند.
- جریان‌ها به OVS این قابلیت را می‌دهند که بسته‌ها را بر اساس پالیسی‌های شبکه و نیازمندی‌های کاربردی مدیریت کند.

 **مفهوم Upcall:**

- مفهوم Upcall در OVS به فرآیندی گفته می‌شود که در آن یک بسته شبکه‌ای که مطابقتی در جدول جریان ندارد، برای تصمیم‌گیری بیشتر به فضای کاربر (user space) ارسال می‌شود.
- این اتفاق معمولاً زمانی رخ می‌دهد که بسته‌ای وارد می‌شود که هیچ قاعده موجودی در جدول جریان با آن مطابقت ندارد. در این حالت، بسته به نرم‌افزار مدیریتی OVS در فضای کاربر ارسال می‌شود تا تصمیم گیری شود که چه اقدامی باید انجام شود.
- پس از آنکه تصمیم گیری انجام شد، می‌توان یک قاعده جدید به جدول جریان اضافه کرد تا بسته‌های بعدی با ویژگی‌های مشابه بدون نیاز به upcall پردازش شوند.

 **مفهوم Datapath:**

- مفهوم Datapath در OVS مسئولیت اصلی را در مسیریابی و پردازش بسته‌های شبکه‌ای دارد. وقتی بسته‌ای به OVS می‌رسد، اولین کاری که انجام می‌شود، این است که در datapath پردازش می‌شود.
- این پردازش شامل مطابقت دادن بسته‌ها با قوانین موجود در جدول جریان (flow table)، اجرای اقدامات تعریف شده برای هر جریان و ارسال بسته‌ها به مقصد نهایی‌شان است.
-  همچنین Datapath در OVS نقطه‌ی مرکزی برای همه‌ی عملیات‌های مرتبط با شبکه است. این شامل تصمیم‌گیری‌های مربوط به مسیریابی بسته‌ها، اجرای سیاست‌های شبکه، و انجام تغییرات بر روی بسته‌ها می‌شود.
-  همچنین، به عنوان یک لایه‌ی انتزاعی عمل می‌کند که پیچیدگی‌های زیرساخت شبکه فیزیکی و مجازی را از برنامه‌های کاربردی و مدیریت شبکه جدا می‌کند.
- در نتیجه، datapath در OVS یک جزء حیاتی است که اجازه می‌دهد تا شبکه‌های مجازی با کارایی بالا و قابلیت اطمینان عمل کنند، و همزمان انعطاف‌پذیری لازم برای پشتیبانی از محیط‌های متنوع و پیچیده‌ی شبکه‌ای را فراهم می‌آورد.

# قسمت دوم: پیکربندی سناریوی پروژه OVS
در این قسمت به بیان پیش نیازهایی که برای اجرای سناریوها نیاز است، پرداخته می ‏شود. در ادامه به معماری که طرح ریزی شده توضیحاتی ارائه می شود.‏

## پیش نیازهای سناریوی پروژه OVS
پیش نیازهایی که برای اجرای سناریوها نیاز است عبارتند از: 

* Linux Machine (Native or Virtual Machine) (Linux Version 4.18 Or Above)
* Install OVS from [Official Site](https://docs.openvswitch.org/en/latest/intro/install/general/)
* [Perf](https://perf.wiki.kernel.org/index.php/Main_Page)
* VS Code

## تنظیمات راه اندازی سناریوی پروژه OVS:

در این قسمت به صورت مختصر، اقداماتی که در راستای ایجاد Switch مجازی شبکه انجام شد، شرح داده می‌شود.

1. در ابتدا با استفاده از یک ماشین مجازی لینوکسی، اقدام به ایجاد دو Name Space به نام های VRF1 و VRF2 کردیم.
2. سپس اقدام به ایجاد چهار Interface شبکه به نام های veth1 و veth2 و veth3 و veth4 کردیم.
3. سپس Interface با نام veth1 را به Namespace VRF1 و veth3 را به Namespace VRF3 اختصاص دادیم.
4. سپس به هر Interface یک IP منحصر به فرد در محدوده 10.10.10.255 اختصاص داده شد.
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/OVS/vrf1_ifconfig.png" alt="Interfaces of VRF1 " />
  <figcaption><em>Interface های Namespace به نامVRF1</em></figcaption>
</figure>
</div>

<p></p>
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/OVS/vrf2_ifconfig.png" alt="Interfaces of VRF2 " />
  <figcaption><em>Interface‏ های ‏Namespace‏ به نامVRF2 </em>‎</figcaption>
</figure>
</div>


5. در ادامه با ایجاد یک Virtual Switch و ساخت Bridge بر روی آن، اقدام به برقراری ارتباط بین دو Namespace کردیم. 
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/OVS/ovs-show.png" alt="OVS Show" />
  <figcaption><em>مشاهده اطلاعات Switch مجازی تعریف شده</em></figcaption>
</figure>
</div>

6. برای مثال بین Namespace با نام VRF1 یک بسته ICMP به Namespace با نام VRF2 ارسال کردیم.
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ping_initial.png" alt="Send ICMP Packet from VRF1 to FRM2" />
  <figcaption><em>ارسال بسته با استفاده از ‏Namespace‏ با نام ‏VRF1‎‏ به ‏VRF2 ‎‏ با آدرس 10.10.10.2‏</em></figcaption>
</figure>
</div>
<p></p>
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/OVS/ifconfig.JPG" alt="Interface of Machine" />
  <figcaption><em>Interface‏ های ساخته شدهF1</em></figcaption>
</figure>
</div>
<p></p>
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/OVS/namespace_commands.JPG" alt="Overview of Commands" />
  <figcaption><em>نگاه کلی به دستورات</em></figcaption>
</figure>
</div>


## معماری Namespace و OVS:
<div dir="rtl" style= "align:center; ">
<figure>
  <img  style="display:block" src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/arch.JPG" alt="Architecture"/>
  <figcaption style="display:block"><em>‏معماری OVS</em></figcaption>
</figure>
</div>


# قسمت سوم: سناریوی Ping

## مقدمات سناریو Ping:

در ابتدا با استفاده از Name Space های ساخته شده در قسمت قبل، اقدام به ping کردن VRF1 و VRF2 کردیم. که در شکل زیر مشهود است:
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ping_initial.png" alt="Send ICMP Reqeust from VRF1 to VRF2" />
  <figcaption><em>ارسال دستور ICMP از طریق VRF1 به VRF2</em></figcaption>
</figure>
</div>


## برای آشنایی با Function هایی که در زمان اجرای دستور ping صدا زده می شوند:

برای پی بردن به Function های مهم ovs در ابتدا یک بار دستور perf را به صورت عادی اجرا کرده و بار دیگر دستور perf را حین سناریو ping اجرا کردیم. در ادامه عکس های زیر که بخشی از نتایج بدست آمده است، نشان داده می‌شود.
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/With%20Ping(perf).png" alt="perf with ping request" />
  <figcaption><em>پاره ای از توابع صدا زده شده در حین اجرای ping</em></figcaption>
</figure>
</div>
<p></p>
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/no%20Ping(perf).png" alt="perf without ping request" />
  <figcaption><em>پاره ای از توابع صدا زده شده بدون اجرای ping</em></figcaption>
</figure>
</div>
<p> فایل کامل خروجی دستورات در پوشه artifacts موجود می باشد.</p>

در نهایت با مقایسه نتایج این دو عملیات، توابعی که تنها در حین سناریو Ping صدا زده می شوند، یافت شد. (توابع بر اساس Hit مرتب شده اند):
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/function-WithPing.png" alt="Unique Function With Ping" />
  <figcaption><em>توابع مختص سناریو Ping</em></figcaption>
</figure>
</div>


با توجه به نتایج بدست آمده در ادامه تمرکز خود را بر روی 13 رخدادی که در شکل مشخص شده و بیشترین تعداد فراخوانی را داشته اند، منعطف کردیم.

## ایجاد kprobeهای اختصاصی

در ابتدا به علت نبود Event و یا Trace Point بر روی Kernel Module OVS، اقدام به تعریف کردن Kprobe بر روی Function های مرتبط با OVS که در قسمت بالا مطرح کردیم. لیست مورد نظر به شرح زیر می باشد:
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/Defination%2013%20Kprobes(New%20Version).png" alt="List Kprobes" />
  <figcaption><em>لیست Kprobes های تعریف شده</em></figcaption>
</figure>
</div>

## دستور Perf برای سناریو Ping:

سپس در ضمن اجرای دستور ping، اقدام به اجرای دستور perf stat بر روی Kprobe های تعریف شده در قسمت قبل نمودیم که نتیجه این دستور به شرح زیر می‌باشد:
<p></p>
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace_ovs_dp_proccess_packet.png" alt="ftrace dp_packet" />
  <figcaption><em>تصویری از ftrace dp_packet_pro </em></figcaption>
</figure>
</div>


پس از بررسی و آنالیز صورت گرفته Event های زیر را بدست آوردیم:

- vs\_vport\_send
- vs\_vport\_receive
- vs\_dp\_process\_packet
- vs\_flow\_cmd\_dump

در ادامه گزارش به بررسی هر یک از رویدادها می پردازیم.


## رویداد ovs_vport_recieve

### آنالیز این رویداد با دستور perf
به منظور بررسی این رویداد همچون رویداد ovs\_vport\_send ابتدا با استفاده از دستور perf، یک call stack از آن تهیه کردیم

همانطور که در شکل زیر مشهود است، تعداد 16 مرتبه تابع ovs\_vport\_recieve در سناریوی ping صدا زده شده است. یعنی 16 بار در سویچ مجازی ما بسته ارسال و دریافت شده است.



سپس برای اطلاع دقیق‌تر از روند ovs-vport-receive ، از دستور ftrace استفاده کردیم. برای اینکار در ضمن برقراری ارتباط بین Namespace ها دستور ftrace را نیز اجرا کردیم. نتایج در تصویر زیر نمایش داده می‌شود:

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/perf_call_graph_vport_recievepng.png" alt="perf call graph vport recieving" />
  <figcaption><em>  
نمودار call-graph برای رویداد ovs_vport_recieved
</em></figcaption>
</figure>
</div>

توضیح هر تابع و دلیل صدا زدن آنها در به شرح زیر است:
<div dir="rtl">

- تابع ovs\_vport\_receive: تابعی در ماژول openvswitch که برای دریافت بسته‌های شبکه‌ای در یک پورت مجازی استفاده می‌شود.

- تابع netdev\_frame\_hook: تابعی در openvswitch که به عنوان یک hook در فرآیند دریافت فریم‌های شبکه عمل می‌کند.

- تابع \_\_netif\_receive\_skb\_core: تابعی در هسته لینوکس برای پردازش اولیه بسته‌های شبکه‌ای که به واسطه اینترفیس شبکه دریافت می‌شوند.

- تابع \_\_netif\_receive\_skb\_one\_core و \_\_netif\_receive\_skb: توابعی در هسته لینوکس برای پردازش بسته‌های شبکه‌ای.

- تابع process\_backlog: پردازش backlog بسته‌های شبکه‌ای در صف.

- تابع `napi\_poll: تابعی برای پردازش NAPI (New API) poll، که برای کاهش بار سی پی یو در پردازش بسته‌های شبکه به کار می‌رود.

- تابع `net\_rx\_action: این تابع بخشی از مکانیزم دریافت شبکه در هسته لینوکس است.

- تابع \_\_softirqentry\_text\_start` تا `\_\_local\_bh\_enable\_ip: توابع مرتبط با مکانیزم softirq در لینوکس که برای پردازش زمان‌بندی شده بخش‌هایی از هسته به کار می‌رود.

- تابع `ip_finish_output2`، `ip_do_fragment`، `ip_fragment.constprop.0`، `__ip_finish_output`، `ip_finish_output`: توابع مربوط به پردازش و ارسال بسته‌های IP.

- تابع `ip_output` و `ip_push_pending_frames`: توابعی برای ارسال بسته‌های IP.

- تابع raw\_sendmsg` و `inet_sendmsg`: توابع مربوط به ارسال پیام‌ها در پروتکل‌های شبکه.

- تابع `sock_sendmsg`: تابع ارسال پیام از طریق سوکت.

- تابع ` __sys_sendto` و `__ x64_sys_sendto`: توابع سیستمی برای ارسال داده‌ها از طریق سوکت‌ها.

- تابع `do_syscall_64`: اجرای یک فراخوان سیستمی در معماری 64 بیتی.

- تابع `entry_SYSCALL_64_after_hwframe`: نقطه ورود به یک فراخوان سیستمی در معماری 64 بیتی.

- تابع `__libc_sendto`: تابع کتابخانه استاندارد C برای ارسال داده‌ها از طریق سوکت.
</div>
از آنجا که دو namespace ساخته شده در یک کرنل لینوکس هستند طبیعی است که ftrace توابع ovs-vport-send و ovs-vport-receive تقریبا مشابه یکدیگر باشد؛ اما در توابعی زیر با یکدیگر تفاوت دارند که در ادامه توضیح میدهیم:

- تابع ovs\_vport\_receive`: این تابع مربوط به ماژول هسته‌ی Open vSwitch (OVS) است. وظیفه‌ی این تابع دریافت پکت‌های شبکه‌ای است که از طریق یک پورت مجازی در OVS وارد می‌شوند. به عبارت دیگر، زمانی که یک بسته شبکه‌ای به یکی از پورت‌های مجازی OVS می‌رسد، این تابع فراخوانی می‌شود تا بسته را پردازش کند.

- تابع `netdev_frame_hook`: این تابع نیز بخشی از ماژول هسته‌ی OVS است و به عنوان یک hookعمل می‌کند که به فریم‌های شبکه‌ای که توسط دستگاه‌های شبکه‌ای مدیریت شده توسط OVS دریافت می‌شوند، واکنش نشان می‌دهد. این تابع در مسیریابی و انتقال فریم‌های شبکه در درون OVS نقش دارد.

- تابع `__netif_receive_skb_core`: این تابع بخشی از هسته‌ی لینوکس است و در پردازش اصلی بسته‌های شبکه‌ای درون هسته‌ی لینوکس نقش دارد. وظیفه‌ی این تابع پردازش اولیه بسته‌های شبکه‌ای است که توسط رابط‌های شبکه (مانند کارت‌های شبکه فیزیکی یا مجازی) دریافت می‌شوند. این تابع تعیین می‌کند که چگونه بسته‌ها باید در سیستم پردازش شوند، مثلاً ارسال به سمت مقصد نهایی یا اعمال سیاست‌های شبکه‌ای.

  ### آنالیز این رویداد با دستور ftrace
<div style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace_ovs_vport_recieve.png" alt="ftrace vport recieving" />
  <figcaption><em>تصویر ftrace تابع ovs_vport_recieve</em></figcaption>
</figure>
</div>


   
 در شکل فوق توابعی که در ovs\_vport\_recieve صدا زده میشوند را با استفاده از دستور ftrace ملاحظه میکنیم که در ادامه به برخی از مهم ترین آنان را توضیح میدهیم:
- تابع. ovs_vport_receive  این تابع برای دریافت پکت‌های شبکه‌ای که به یک پورت مجازی(vport) درOVS می‌رسند، استفاده می‌شود. این اولین نقطه ورود بسته بهOVS است.
- تابع- parse_ethertype  تعیین نوع پروتکل موجود در پکت مثلا ً، IP ، ARP و غیره.
- تابع - __skb_get_hash محاسبه هش بسته برای مسیریابی و تعادل‌بندی بار.
- تابع- do_output  ارسال پکت به مقصد نهایی.
- تابع- ovs_lookup_vport  یافتن پورت مقصد.
- تابع- ovs_vport_send  ارسال پکت از پورت مجازی.
  
  
## رویداد ovs\_vport\_send:

###  آنالیز این رویداد با دستور perf

ابتدا اقدام به تهیه Call Stack با استفاده از دستور perf کردیم که دستور آن در تصویر پایین بدست آمد:

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/perf%2013%20kprobe%20just%20command.png" alt="perf Command" />
  <figcaption><em>دستور perf stat</em></figcaption>
</figure>
</div>
<p></p>

در ادامه تصویری از نتایج بدست آمده نشان داده میشود.

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/perf_call_graph_vport_send.png" alt="perf Command" />
  <figcaption><em>نمودار call-graph برای رویداد ovs_vport_send</em></figcaption>
</figure>
</div>
<p></p>
براخی توابع مهم این Call Graph عبارتند از:

- تابع ovs_dp_process_packet: این تابع برای پردازش بسته ها در یک سوییچ مجازی استفاده می شود. این تابع برای هر بسته ای که به سوییچ وارد می شود، یک بار فراخوانی می شود. وظایف این تابع شرح زیر می باشد:

  -- بررسی اینکه بسته از کدام پورت ورودی وارد شده است.
  -- جستجوی یک Flow مطابق با بسته.     
  -- اگر Flow مطابق با بسته یافت نشود، یک Event از دست دادن بسته ارسال می‌شود.

* تابع ovs_execute_action: پس از بررسی و تطابق با Flow Table، اقدام مورد نظر انتخاب می شود و اجرا می شود. وظایف این تابع عبارتد از:
 
  ** بررسی نوع افدام از جمله ارسال بسته به پورت خروجی، تغییر آدرس MAC و غیره
  
  ** اجرای اقدام مربوطه
  
###  آنالیز این رویداد با دستور ftrace


سپس برای اطلاع دقیق‌تر از روند ovs-vport-send، از دستور ftrace استفاده کردیم. برای اینکار در ضمن برقراری ارتباط بین Namespace ها دستور ftrace را نیز اجرا کردیم. نتایج در تصویر زیر نمایش داده می‌شود:

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace_ovs_vport_send.png" alt="ftrace Command" />
  <figcaption><em>تصویر ftrace تابع ovs_vport_recieve</em></figcaption>
</figure>
</div>
<p></p>

که در این قسمت به تفضیل توابع مهمی که داخل این تابع صدا زده می‌شود، پرداخته می شود:
* تابع dev_queue_xmit: این تابع وظیفه ارسال یک بسته از طریق پورت شبکه را بر عهده دارد. در واقع از این تابع برای ارسال Packet به Virtual Port استفاده می شود.
* تابع __dev_queue_xmit: داخل تابع dev_queue_xmit از این تابع که برای ورود Packet به صف Port مربوطه است، استفاده می شود. وظایفی از قبیل Encapsulation و غیره را انجام می دهد. تفاوت این تابع با تابع قبلی در این است که اجازه ارسال Packet به یک صف خاص از Port را می دهد.
* تابع netdev_core_pick_tx: داخل تابع dev_queue_xmit از این تابع که برای ارسال پکت ابتدا باید صف مورد نظر انتخاب شود، وظیفه انتخاب یک صف ارسال برای ارسال یک بسته از طریق یک port شبکه بر عهده این تابع است. حال باید بسته مراحل Validation را پشت سر بگذارد.
* تابع validate_xmit_skb: وظیفه Validate کردن پکت های خروجی قبل از ارسال آن ها به کارت شبکه فیزیکی را برعهده دارد.
* تابع dev_hard_start_xmit:  وظیفه ارسال فوری یک بسته از طریق یک دستگاه شبکه را بر عهده دارد.
* تابع veth_xmit:  وظیفه ارسال یک بسته از طریق یک دستگاه شبکه مجازی (veth) را بر عهده دارد. در واقع برای ارسال بسته از طریق veth هایی که در قسمت اول ساختیم به کار می رود.


## رویداد ovs_dp_process_packet

###  آنالیز این رویداد با دستور perf

- تابع netdev\_port\_receive و netdev\_frame\_hook: این توابع مربوط به دریافت بسته‌های شبکه توسط دستگاه‌های شبکه (netdev) هستند.

- تابع \_\_netif\_receive\_skb\_core: این توابع بخشی از مکانیزم پردازش بسته‌های شبکه در هسته لینوکس هستند. skb (socket buffer) نشان‌دهنده ساختار داده‌ای است که بسته‌های شبکه در آن نگهداری می‌شوند.

- تابع napi\_poll, net\_rx\_action: این توابع به مدیریت و دریافت بسته‌های شبکه در محیط NAPI (New API) مربوط می‌شوند که برای کاهش بار CPU در سیستم‌های با ترافیک شبکه بالا طراحی شده است.

- تابع ip\_finish\_output2, ip\_output, ip\_push\_pending\_frames: این توابع در مسیردهی و ارسال بسته‌های IP دخیل هستند.

- تابع raw\_sendmsg, inet\_sendmsg: مربوط به ارسال پیام‌ها از طریق سوکت‌های شبکه در لایه‌های بالاتر می‌باشد.

- تابع \_\_sys\_sendto, \_\_x64\_sys\_sendto: این توابع در سطح سیستم‌عامل برای ارسال داده‌ها از طریق سوکت‌ها به کار می‌روند.


###  آنالیز این رویداد با دستور ftrace

شکل زیر Call Stack مربوط به این رویداد را نشان میدهد. اینtrace از توابع نشان‌دهنده‌ی مراحل پردازش یک بسته شبکه‌ای درOpen vSwitch (OVS) و هسته‌ی لینوکس است. هر یک از این توابع نقش مشخصی در این فرآیند دارند:
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace_ovs_dp_proccess_packet.png" alt="ftrace ovs_dp_process_packet" />
  <figcaption><em>تصویری از ftrace ovs_dp_packet_packet</em></figcaption>
</figure>
</div>

- تابع ovs\_dp\_process\_packetاین تابع برای پردازش بسته‌ها درdatapath یا همان مسیر دادهOVS فراخوانی می‌شود.
- skb\_get\_hash این تابع هش بسته‌ی شبکه‌ای را برای مسیریابی و تعادل‌بندی بار محاسبه می‌کند.
- تابع \_skb\_flow\_dissectاین تابع بسته‌ها را تجزیه و تحلیل می‌کند تا اطلاعات لازم برای محاسبه‌ی هش را بدست آورد.
- تابع ovs\_flow\_tbl\_lookup\_statsجستجو در جدول جریانOVS برای یافتن یک جریان مطابق با بسته و به‌روزرسانی آمار آن.
- تابع flow\_lookup.constprop.0: این زیرتابع به دنبال یافتن جریان موردنظر در جدول است.
- تابع masked\_flow\_lookupجستجوی جریان با استفاده از یک ماسک.
- تابع find\_bucket.isra.0: یافتن باکت مرتبط در جدول هش جریان.
- تابع ovs\_flow\_stats\_update: به‌روزرسانی آمار جریان مرتبط با بسته.
- تابع \_raw\_spin\_lock قفل کردن برای جلوگیری از دسترسی همزمان به داده‌ها.
- تابع ovs\_execute\_actions: اجرای اقدامات تعریف شده برای بسته.
- تابع do\_execute\_actions: اجرای اقدامات مشخص شده.
- تابع do\_output: ارسال بسته به مقصد نهایی.
- تابع ovs\_lookup\_vport: یافتن پورت مجازی مقصد.
- تابع ovs\_vport\_send: ارسال بسته از طریق پورت مجازی.
- تابع dev\_queue\_xmit و \_dev\_queue\_xmit() این توابع برای قرار دادن بسته در صف ارسال دستگاه شبکه و مدیریت ارسال آن هستند.
- تابع veth\_xmit این تابع مخصوص ارسال بسته‌ها از طریق دستگاه‌های شبکه‌ی مجازی(veth) است.

## اتصال OVS Module به Kernel

در ادامه به بررسی نقطه ای که در فضای Runtime، لینوکس پس دریافت بسته، اجرای روند را به OVS می دهد. برای این منظور به سورس کد لینوکس رفته و با جستجوی تابع \_\_netif\_receive\_skb\_core و آنالیز بدنه تابع مربوطه به متغیر rx\_handler رسیدیم که در این جا با استفاده از Switch case مربوطه، بسته را به OVS ارسال می کند. تصویر مربوط به این متغیر را در ذیل قرار دادیم:


<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/Source%20Code.png" alt="Joint Kernel and OVS Module" />
  <figcaption><em>نقطه اتصال کرنل لینوکس به OVS\_MODULE</em></figcaption>
</figure>
</div>


## مبحث گلوگاه

### جدول آماری توابع اجرا شده

<div align="center">

|            Func           |   MAX   |   MIN  |   AVG  |
|:-------------------------:|:-------:|:------:|:------:|
|     ovs_vport_receive     | 169.144 | 30.112 |  92.80 |
|   ovs_dp_process_packet   | 162.795 | 23.969 | 83.192 |
|       ovs_vport_send      |  30.612 |  7.538 | 19.980 |
|    ovs_flow_key_extract   |  4.348  |  4.348 |  4.348 |
| ovs_flow_tbl_lookup_stats |  44.28  |  2.16  |  9.188 |
|      ovs_ct_fill_key      |   2.85  |  1.391 |  1.980 |
|       ovs_dp_cmd_get      | 1281.66 |  24.78 | 197.70 |
|     ovs_flow_cmd_dump     |  16.288 |  2.377 |  9.31  |
|   ovs_flow_stats_update   |  5.139  |  1.015 |  2.515 |
|     ovs_ct_update_key     |  1.639  |  0.811 |  1.166 |
|      ovs_lookup_vport     |  1.042  |  0.26  |  0.544 |
</div>
 ### نتیجه گیری و تحیلیل گلوگاه OVS
 <p></p>
 |
| --- |
| سید حمیدرضا میریگانه
متین برهانی  |
| زمستان1402 |
</div>


