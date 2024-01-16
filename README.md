# OVS Tracing
<div dir="rtl">
<div style: align="center">
<figure>
  <img src="https://upload.wikimedia.org/wikipedia/commons/5/59/Open_vSwitch_Logo.svg" alt="OVS Logo" />
</figure>
</div>
 

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

# قسمت دوم: پیکربندی سناریوهای پروژه OVS
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


## معماری Namespace و OVS پیاده سازی شده در پروژه:
 <div dir="rtl" style: align="center">
<figure>
  <img  style="display:block" src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/arch.JPG" alt="Architecture"/>
<!--   <figcaption style="display:block"><em>‏معماری OVS</em></figcaption> -->
  
  *معماری OVS*
</figure>
</div>

##  استخراج Function های صدا زده در حین اجرای دستور  ping و netcat :

برای پی بردن به Function های مهم ovs در ابتدا یک بار دستور perf را به صورت عادی اجرا کرده و بار دیگر دستور perf را حین سناریو ping و netcat اجرا کردیم. در ادامه عکس های زیر که بخشی از نتایج بدست آمده است، نشان داده می‌شود.
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/With%20Ping(perf).png" alt="perf with ping request" />
  <figcaption><em>پاره ای از توابع صدا زده شده در حین اجرای ping و netcat</em></figcaption>
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

در نهایت با مقایسه نتایج این دو عملیات، توابعی که تنها در حین سناریو Ping و netcat صدا زده می شوند، یافت شد. (توابع بر اساس Hit مرتب شده اند):
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/function-WithPing.png" alt="Unique Function With Ping" />

  *توابع مختص سناریو Ping و netcat*
  
</figure>
</div>

## تعریف kprobe:

در ابتدا به علت نبود Event و یا Trace Point بر روی Kernel Module OVS، اقدام به تعریف کردن Kprobe بر روی Function های مرتبط با OVS که در قسمت بالا مطرح کردیم. لیست مورد نظر به شرح زیر می باشد:
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/Defination%2013%20Kprobes(New%20Version).png" alt="List Kprobes" />
<!--   <figcaption><em>لیست Kprobes های تعریف شده</em></figcaption> -->
  
  *لیست Kprobes های تعریف شده*
</figure>
</div>

## تحلیل kprobe های ایجاد شده:

پس از تعریف Kprobe در قسمت قبل، ضمن اجرای دستور ping، اقدام به اجرای perf stat نمودیم که نتیجه این دستور به شرح زیر می‌باشد:
<p></p>
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/perf_stat_13-kprobes.png" alt="perf_stat_13-kprobes" />
  <figcaption><em>تصویری از دستور perf بر روی kprobeهای تعریف شده</em></figcaption>
</figure>
</div>


پس از بررسی و آنالیز صورت گرفته Event های زیر را بدست آوردیم:

- vs\_vport\_send
- vs\_vport\_receive
- vs\_dp\_process\_packet
- vs\_flow\_cmd\_dump

در ادامه گزارش به بررسی هر یک از رویدادها می پردازیم.


# قسمت سوم: سناریوی Ping

## راه اندازی سناریو Ping:

در ابتدا با استفاده از Name Space های ساخته شده در قسمت قبل، اقدام به ping کردن VRF1 و VRF2 کردیم. که در شکل زیر مشهود است:
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ping_initial.png" alt="Send ICMP Reqeust from VRF1 to VRF2" />
  <figcaption><em>ارسال دستور ICMP از طریق VRF1 به VRF2</em></figcaption>
</figure>
</div>



با توجه به نتایج بدست آمده در ادامه تمرکز خود را بر روی 13 رخدادی که در شکل مشخص شده و بیشترین تعداد فراخوانی را داشته اند، منعطف کردیم.



## رویداد ovs_vport_recieve

### تحلیل رویداد vport_recieve با دستور perf:
به منظور بررسی این رویداد همچون رویداد ovs\_vport\_send ابتدا با استفاده از دستور perf، یک call stack از آن تهیه کردیم

همانطور که از خروجی kprobeهای تعریف شده در بالا دیدیم، تعداد 16 مرتبه تابع ovs\_vport\_recieve در سناریوی ping صدا زده شده است. یعنی 16 بار در سویچ مجازی ما بسته ارسال و دریافت شده است.



سپس برای اطلاع دقیق‌تر از روند ovs-vport-receive ، از دستور ftrace استفاده کردیم. برای اینکار در ضمن برقراری ارتباط بین Namespace ها دستور ftrace را نیز اجرا کردیم. نتایج در تصویر زیر نمایش داده می‌شود:

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/perf_call_graph_vport_recievepng.png" alt="perf call graph vport recieving" />
  <figcaption><em>  
نمودار call-graph برای رویداد ovs_vport_recieved
</em></figcaption>
</figure>
</div>

توضیح هر تابع و دلیل صدا زدن آنها  به شرح زیر است:
<div dir="rtl">

طبق call graph روال کار به این صورت است که VRF1 بسته ping را به پورت ovs ارسال میکند. تابع ovs\_vport\_receive بسته ping را از پورت ovs دریافت کرده، اگر بسته ping برای یک namespace معتبر باشد، تابع ovs\_vport\_receive بسته ping را به تابع netdev\_frame\_hook تحویل میدهد. تابع netdev\_frame\_hook بسته ping را از نظر صحت فرمت بررسی میکند. اگر بسته ping معتبر باشد، تابع netdev\_frame\_hook بسته ping را به تابع \_\_netif\_receive\_skb\_core تحویل میدهد. تابع \_\_netif\_receive\_skb\_core بسته ping را به هسته لینوکس تحویل داده و هسته لینوکس بسته ping را بر اساس آدرس مقصدش پردازش میکند. در گام بعد تابع  process\_backlog را داریم که وظیفه دارد که بسته های شبکه را از صف backlog پردازش کند. این تابع ابتدا بسته ها را از صف backlog دریافت میکند. سپس، بر اساس آدرس مقصد آنها را پردازش میکند.
</div>
از آنجا که دو namespace ساخته شده در یک کرنل لینوکس هستند طبیعی است که ftrace توابع ovs-vport-send و ovs-vport-receive تقریبا مشابه یکدیگر باشد؛ اما در توابعی زیر با یکدیگر تفاوت دارند که در ادامه توضیح میدهیم:

- تابع ovs\_vport\_receive: این تابع مربوط به ماژول هسته‌ی Open vSwitch (OVS) است. وظیفه‌ی این تابع دریافت پکت‌های شبکه‌ای است که از طریق یک پورت مجازی در OVS وارد می‌شوند. به عبارت دیگر، زمانی که یک بسته شبکه‌ای به یکی از پورت‌های مجازی OVS می‌رسد، این تابع فراخوانی می‌شود تا بسته را پردازش کند.

- تابع `netdev_frame_hook`: این تابع نیز بخشی از ماژول هسته‌ی OVS است و به عنوان یک hookعمل می‌کند که به فریم‌های شبکه‌ای که توسط دستگاه‌های شبکه‌ای مدیریت شده توسط OVS دریافت می‌شوند، واکنش نشان می‌دهد. این تابع در مسیریابی و انتقال فریم‌های شبکه در درون OVS نقش دارد.

- تابع `__netif_receive_skb_core`: این تابع بخشی از هسته‌ی لینوکس است و در پردازش اصلی بسته‌های شبکه‌ای درون هسته‌ی لینوکس نقش دارد. وظیفه‌ی این تابع پردازش اولیه بسته‌های شبکه‌ای است که توسط رابط‌های شبکه (مانند کارت‌های شبکه فیزیکی یا مجازی) دریافت می‌شوند. این تابع تعیین می‌کند که چگونه بسته‌ها باید در سیستم پردازش شوند، مثلاً ارسال به سمت مقصد نهایی یا اعمال سیاست‌های شبکه‌ای.

### تحلیل رویداد vport_recieve با دستور ftrace:
<div style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace_ovs_vport_recieve.png" alt="ftrace vport recieving" />


  *تصویر ftrace تابع ovs_vport_recieve*
</figure>
</div>


   در هسته Linux، بسته ها توسط تابع parse\_ethertype پردازش می شوند. این تابع نوع پروتکل بسته را شناسایی می کند.اگر نوع پروتکل بسته ICMP باشد، تابع do\_output بسته را به یک خروجی شبکه منتقل می کند. خروجی شبکه، بسته را به میزبان دیگر منتقل می کند. این میزبان بسته را از طریق شبکه ارسال می کند. در طرف دیگر شبکه، بسته توسط میزبان دیگر دریافت می شود که بسته را به هسته Linux منتقل می کند. در هسته Linux، بسته توسط تابع ovs\_lookup\_vport میزبان مقصد را شناسایی می کند. سپس، بسته توسط تابع ovs\_vport\_send  ارسال می شود و میزبان مقصد بسته را به لایه شبکه منتقل می کند.
  
  
## رویداد ovs\_vport\_send:

### تحلیل رویداد vport_send با دستور perf:

ابتدا اقدام به تهیه Call Stack با استفاده از دستور perf کردیم که   تصویر آن در پایین مشهود است:

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
برخی توابع مهم این Call Graph عبارتند از:

- تابع ovs_dp_process_packet: این تابع برای پردازش بسته ها در یک سوییچ مجازی استفاده می شود. این تابع برای هر بسته ای که به سوییچ وارد می شود، یک بار فراخوانی می شود. وظایف این تابع به شرح زیر می باشد:

  -- بررسی اینکه بسته از کدام پورت ورودی وارد شده است.
  
  -- جستجوی یک Flow مطابق با بسته.
  
  -- اگر Flow مطابق با بسته یافت نشود، یک Event از دست دادن بسته ارسال می‌شود.

* تابع ovs_execute_action: پس از بررسی و تطابق با Flow Table، اقدام مورد نظر انتخاب می شود و اجرا می شود. وظایف این تابع عبارتد از:
 
  -- بررسی نوع اقدام از جمله ارسال بسته به پورت خروجی، تغییر آدرس MAC و غیره
  
  -- اجرای اقدام مربوطه
  
سایر توابع مربوطه، در قسمت های مختلف این سند بررسی شده است.

### تحلیل رویداد vport_send با دستور ftrace:


سپس برای اطلاع دقیق‌تر از روند ovs-vport-send، از دستور ftrace استفاده کردیم. برای اینکار در ضمن برقراری ارتباط بین Namespace ها دستور ftrace را نیز اجرا کردیم. نتایج در تصویر زیر نمایش داده می‌شود:

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace_ovs_vport_send.png" alt="ftrace Command" />
  <figcaption><em>تصویر ftrace تابع ovs_vport_recieve</em></figcaption>
</figure>
</div>
<p></p>

که در این قسمت به تفضیل توابع مهمی که داخل این تابع صدا زده می‌شود، پرداخته می شود:

تابع dev\_queue\_xmit صدا زده میشود تا بسته را برای ارسال آماده کند. تابع dev\_queue\_xmit نیز بسته را به یک صف ارسال منتقل می کند. سپس، میزبان، بسته را از صف ارسال بررسی می کند. یعنی ابتدا توسط تابع netdev\_core\_pick\_tx کامپیوتر دیگر را برای ارسال بسته انتخاب می کند. سپس، توسط تابع validate\_xmit\_skb بسته را بررسی می کند تا مطمئن شود که دارای فرمت صحیح است. اگر بسته دارای فرمت صحیح باشد، توسط تابع dev\_hard\_start\_xmit بسته را به سخت افزار شبکه منتقل می کند تا بسته از طریق شبکه ارسال شود.


## رویداد ovs_dp_process_packet


### تحلیل رویداد dp_process_packet با دستور perf:

اولین مرحله در پردازش بسته های شبکه، دریافت آنها از رابط شبکه است. این کار توسط تابع netdev\_port\_receive انجام می شود. این تابع بسته های شبکه را از درایور رابط شبکه دریافت می کند و آنها را به هسته لینوکس تحویل می دهد. پس از دریافت بسته های شبکه، آنها باید پردازش شوند. این کار توسط تابع \_netif\_receive\_skb\_core انجام می شود. این تابع بسته های شبکه را بررسی می کند تا مطمئن شود که معتبر هستند. سپس، نوع پروتکل بسته را تعیین می کند و آن را به لایه شبکه مناسب ارسال می کند. پس از پردازش بسته های شبکه، آنها باید به شبکه ارسال شوند. این کار توسط تابع ip\_output انجام می شود. این تابع مسیر بسته را تعیین می کند، آن را در جدول مسیریابی درج می کند، و سپس آن را به رابط شبکه مناسب ارسال می کند.


### تحلیل رویداد dp_process_packet با دستور ftrace:

شکل زیر Call Stack مربوط به این رویداد را نشان میدهد. اینtrace از توابع نشان‌دهنده‌ی مراحل پردازش یک بسته شبکه‌ای درOpen vSwitch (OVS) و هسته‌ی لینوکس است. هر یک از این توابع نقش مشخصی در این فرآیند دارند:
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace_ovs_dp_proccess_packet.png" alt="ftrace ovs_dp_process_packet" />

  *تصویری از ftrace ovs_dp_packet_packet*
</figure>
</div>

ابتدا بسته ورودی توسط تابع skb\_get\_hash  هش می شود تا بر اساس آن در جدول جریان جستجو شود. اگر بسته در جدول جریان یافت شود، تابع \_skb\_flow\_dissect اطلاعات مربوط به جریان را از بسته استخراج می کند. این اطلاعات سپس توسط تابع ovs\_flow\_tbl\_lookup\_stats  برای به روزرسانی آمار جریان استفاده می شود. اگر بسته در جدول جریان یافت شد تابع ovs\_execute\_actions  اقدامات مربوط به جریان را بر روی بسته ورودی اجرا می کند .این اقدامات می تواند شامل تغییر آدرس مقصد، تغییر شماره پورت مقصد یا ارسال بسته به پورت دیگری باشد .سپس، تابع ovs\_lookup\_vportپورت خروجی را برای بسته خروجی بر اساس شناسه جریان جستجو می کند.در نهایت، تابع ovs\_vport\_sendبسته خروجی را به پورت خروجی ارسال می کند. این کار توسط توابع داخلی dev\_queue\_xmitیا \_dev\_queue\_xmitانجام می شود.

## رویداد OVS\_flow\_cmd\_dump:

این رویداد برای نمایش OVS Flow table که در قسمت ساختار OVS توضیح داده شده است، بکار می رود. در واقع بسته ping به OVS می رسد باید برا اساس یک Entry از Flow Table شناسایی شود (یا اگر اولین بار است، ایجاد شود) و براساس اون Action مورد نظر انتخاب شود.

در همین ابتدای کار بهتر است توضیحی در مورد [Generic Netlink](https://docs.kernel.org/networking/generic_netlink.html)داده شود. در واقع این Generic Netlink که مکانیسمی انعطاف پذیر برای Kernel است که بتواند بین Process های داخل Kernel و با Process های User-Space ارتباط برقرار کند. این مکانیسم جایگزین روش قبلی که IOCTL نامیده میشده است، شد.

همان طور که در تصویر ساختار OVS مشهود است، در OVS\_flow\_cmd\_dump به علت اینکه Flow Table داخل Kernel Space قرار دارد و Tread Hanlder ها در User Space، از مکانیزم Netlink بسیار استفاده می شود.

در ادامه با استفاده از ابزارهای perf و ftrace به تحلیل این رویداد می پردازیم.

### تحلیل رویداد flow\_cmd\_dump با استفاده از perf:

به منظور بررسی این رویداد همچون رویداد ovs\_vport\_send ابتدا با استفاده از دستور perf، یک call stack از آن تهیه کردیم.

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/perf%20ovs_flow_cmd_dump.png" alt="perf ovs_flow_cmd_fill_stats" />

  *تصویری از perf ovs_flow_cmd_fill_stats*
</figure>
</div>


- تابع \_\_netlink\_dump\_start: این تابع بخشی از API مربوط به ارتباط بین Kernel و UserSpace است. در دستور ping، اطلاعات Flow Table را از Kernel به User Space می برد.
- تابع genl\_family\_rcv\_msg: این تابع بخشی از مکانیزم Generic Handle که وظیفه این تابع پردازش اطلاعات دریافتی از User-space است.
- تابع netlink\_rcv\_skb: این تابع نیز بخشی از Generic Handle است که اولین تابعی است که موقع دریافت پیام از User Space، یک Socket Buffer دریافت می کند و Message داخلش از User Space است.

### تحلیل رویداد flow\_cmd\_dump با استفاده از ftrace:

شکل زیر Call Stack مربوط به این رویداد را نشان میدهد.
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace%20ovs_flow_cmd_dump.png" alt="ftrace ovs_flow_cmd_fill_stats" />

  *تصویری از ftrace ovs_flow_cmd_fill_stats*
</figure>
</div>


- تابع ovs\_flow\_tbl\_dump\_next: وقتی تابع flow\_cmd\_dump صدا زده میشود، برای iterate کردن روی Flow Table از این تابع استفاده می شود. در واقع با این تابع سطر بعدی Flow Table خوانده می شود.
- تابع ovs\_flow\_cmd\_fill\_info: این تابع وظیفه Dump کردن اطلاعات به سطرهای Flow Table را برعهده دارد. هر سطر Flow Table اطلاعات زیادی نظیر Condition ها، Actionها و اطلاعات آماری را شامل می شود.

همچنین دریافت اطلاعات هر ردیف Flow Table و ارسال به User Space نیز برعهده دارد.

- تابع ovs\_flow\_cmd\_fill\_stats: همان طور که در قسمت قبل توضیح داده شد، هر ردیف از Flow Table خود دارای قسمت های متعددی از جمله اطلاعات آماری است که این تابع وظیفه Dump کردن این اطلاعات آماری را برعهده دارد.
- 

## اتصال OVS Module به Kernel

در ادامه به بررسی نقطه ای که در فضای Runtime، لینوکس پس دریافت بسته، اجرای روند را به OVS می دهد. برای این منظور به سورس کد لینوکس رفته و با جستجوی تابع \_\_netif\_receive\_skb\_core و آنالیز بدنه تابع مربوطه به متغیر rx\_handler رسیدیم که در این جا با استفاده از Switch case مربوطه، بسته را به OVS ارسال می کند. تصویر مربوط به این متغیر را در ذیل قرار دادیم:


<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/Source%20Code.png" alt="Joint Kernel and OVS Module" />
  
  *نقطه اتصال کرنل لینوکس به OVS\_MODULE*
</figure>
</div>


## مبحث گلوگاه

### جدول آماری توابع اجرا شده در سناریوی Ping

<div align="center">
<p><em>جدول آماری زمان اجرای توابع</em></p>
  
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

جدول فوق زمان‌های اجرایی (به میکروثانیه) در زمان اجرای سناریوی Ping برای توابع مرتبط با OVS را نشان می‌دهد. این اعداد می‌توانند نشان دهنده عملکرد و کارآیی مختلف جنبه‌های OVS باشند. برای تحلیل گلوگاه‌ها در OVS، باید به سه معیار MAX (بیشینه)، MIN (کمینه)، و AVG (میانگین) توجه کرد. بر اساس این جدول:

1. تابع ovs\_dp\_cmd\_get: این تابع بالاترین زمان اجرایی را دارد (1281.66 میکروثانیه)، که نشان می‌دهد در بدترین حالت، این تابع می‌تواند به شدت بر عملکرد تأثیر بگذارد.

2. توابع ovs\_vport\_receive و ovs\_dp\_process\_packet: این توابع نیز زمان‌های اجرایی نسبتاً بالایی دارند، به خصوص در مقادیر بیشینه و میانگین. این نشان می‌دهد که دریافت و پردازش بسته‌ها می‌تواند منابع قابل توجهی را مصرف کند.

3. توابع ovs\_flow\_key\_extract, ovs\_ct\_fill\_key, ovs\_ct\_update\_key, و ovs\_lookup\_vport: این توابع زمان‌های اجرایی کمتری دارند، که نشان می‌دهد آنها به احتمال زیاد گلوگاه‌های عمده نیستند.

با توجه به این تحلیل، به نظر می‌رسد که گلوگاه اصلی OVS در تابع ovs\_dp\_cmd\_get باشد، به دلیل زمان اجرایی بسیار بالا در بدترین حالت. همچنین، ovs\_vport\_receive و ovs\_dp\_process\_packet نیز به دلیل زمان‌های اجرایی بالا در حالت‌های میانگین و بیشینه، می‌توانند نقاط کلیدی برای بهبود کارایی باشند.

.
 ### نتیجه گیری و تحلیل گلوگاه OVS
 <p></p>


 # قسمت چهارم: سناریوی ‏netcat

 ## تحیلیل سناریو Netcat

در این قسمت به ارزیابی عملکرد OVS برای سناریوی netcat می‌پردازیم. ابتدا بین دو namespace یک ارتباط برقرار می‌کنیم؛ یعنی VRF1 را به‌عنوان سرور و VRF2 را به‌عنوان کلاینت در نظر می‌گیریم و با استفاده از OVS بین این دو به تبادل اطلاعات می‌پردازیم.

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Netcat%20Scenario/Netcat%20Command%20Between%20Two%20Namespace.png" alt="Netcat Command " />
  <figcaption><em>ارتباط netcat بین دو namespaceS</em></figcaption>
</figure>
</div>

در قسمت پیکربندی سناریو های پروژه، تعداد 13 مورد Kprobes تعریف کرده بودیم که همان‌ها را برای این آزمایش تکرار می‌کنیم تا ببینیم نتیجه به چه صورت خواهد بود و چه تغییراتی را شاهد هستیم. بازهم مشاهده می‌کنیم که بیشترین تعداد رخداد مربوط به تابع ovs\_flow\_cmd\_dump می‌باشد. در خروجی function\_call\_graph هم همین‌طور که انتظار می‌رود، این تابع بیشترین زمان اجرا را به خود اختصاص می‌دهد.

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Netcat%20Scenario/Netcat%2013%20Kprobes.png" alt="Kprobes Defined " />
  <figcaption><em>Kprobe‏ های تعریف شده</em></figcaption>
</figure>
</div>


زمانی که بین دو تا namespace در ovs ارتباط netcat برقرار می‌کنیم، بسته‌های شبکه باید از طریق bridge ovs عبور کنند. bridge ovs برای پیدا کردن مسیر بسته‌ها، از جدول flow استفاده می‌کند. این جدول یک ساختار داده است که شامل اطلاعات مربوط به پورت‌های مبدأ و مقصد، پروتکل، پورت مقصد و غیره است.

هنگامی‌که یک بسته وارد bridge ovs می‌شود، bridge ovs ابتدا باید جدول flow را بررسی کند تا ببیند کدام پورت مقصد برای آن بسته مناسب است. اگر bridge ovs بتواند یک ورودی مناسب در جدول flow پیدا کند، بسته را به پورت مقصد ارسال می‌کند؛ اما اگر bridge ovs نتواند یک ورودی مناسب در جدول flow پیدا کند، یک upcall به user space ارسال می‌کند تا user space مسیر بسته را پیدا کند که در اینجا 5 بار upcall صورت گرفته است.

در ارتباط ping، بسته‌های شبکه فقط شامل یک header هستند. این header شامل اطلاعات مربوط به آدرس مبدأ و مقصد است؛ بنابراین، bridge ovs می‌تواند به‌راحتی مسیر بسته را با استفاده از جدول flow پیدا کند؛ اما در ارتباط netcat، بسته‌های شبکه ممکن است شامل داده‌های زیادی باشند. در این حالت، bridge ovs ممکن است نتواند مسیر بسته را با استفاده از جدول flow پیدا کند و مجبور شود یک upcall به user space ارسال کند.

به‌طورکلی، هر چه داده‌های بسته‌های شبکه بیشتر باشد، احتمال اینکه bridge ovs نتواند مسیر بسته را با استفاده از جدول flow پیدا کند و مجبور شود یک upcall به user space ارسال کند، بیشتر است؛ بنابراین، تعداد upcallهایی که به user space زده می‌شود تا مسیر بسته‌ها را پیدا کند زمانی که بین دو تا namespace در ovs ارتباط netcat برقرار می‌کنیم، بیشتر از زمانی است که ping بین این دو namespace می‌زنیم.

اما نکته بعدی که باید بیشتر توضیح داده شود تابع ovs\_flow\_cmd\_dump است. تابع ovs\_flow\_cmd\_dump برای نمایش جدول flow استفاده می‌شود. این جدول یک ساختار داده است که شامل اطلاعات مربوط به پورت‌های مبدأ و مقصد، پروتکل، پورت مقصد و غیره است.

در ارتباط netcat، بسته‌های شبکه ممکن است شامل داده‌های زیادی باشند. در این حالت، جدول flow ممکن است حاوی تعداد زیادی ورودی باشد؛ بنابراین، تابع ovs\_flow\_cmd\_dump برای نمایش جدول flow باید تعداد زیادی ورودی را پردازش کند. این پردازش می‌تواند زمان زیادی را صرف کند.

اما در ارتباط ping، از آنجا که بسته‌های شبکه فقط شامل یک header هستند، جدول flow ممکن است حاوی تعداد کمی ورودی باشد. در این حالت، تابع ovs\_flow\_cmd\_dump برای نمایش جدول flow باید تعداد کمی ورودی را پردازش کند. این پردازش زمان کمتری را صرف می‌کند.

به‌طورکلی، هر چه تعداد ورودی‌های جدول flow بیشتر باشد، زمان پردازش تابع ovs\_flow\_cmd\_dump بیشتر است؛ بنابراین، زمانی که بین دو تا namespace در ovs ارتباط netcat برقرار می‌کنیم، تابع ovs\_flow\_cmd\_dump زمان بیشتری را صرف می‌کند و این زمان به نسبت به زمانی که ping می‌زنیم بین این دو namespace کمتر است.



## مبحث گلوگاه

### جدول آماری توابع اجرا شده در سناریوی netcat

<div align="center">
<p><em>جدول آماری زمان اجرای توابع</em></p>
  
|            Func           |    MAX   |   MIN  |   AVG   |
|:-------------------------:|:--------:|:------:|:-------:|
|     ovs_vport_receive     |  204.512 | 13.262 |  65.088 |
|   ovs_dp_process_packet   |  186.358 | 10.569 |  52.591 |
|       ovs_vport_send      |  33.389  |  3.042 |  14.77  |
|    ovs_flow_key_extract   |  57.011  |  1.857 |  10.19  |
| ovs_flow_tbl_lookup_stats |   8.16   |  1.224 |  4.391  |
|      ovs_ct_fill_key      |   3.262  |  0.651 |  1.833  |
|       ovs_dp_cmd_get      |  1181.30 | 34.103 |  319.43 |
|     ovs_flow_cmd_dump     | 2713.895 |  3.935 | 456.165 |
|   ovs_flow_stats_update   |   4.306  |  0.554 |  1.813  |
|     ovs_ct_update_key     |   1.159  |  0.238 |  0.420  |
|      ovs_lookup_vport     |   0.571  |  0.227 |  0.292  |

</div>
در سناریوی netcatنیز، گلوگاه ovs در تابع ovs_dp_process_packet قرار دارد که میانگین زمان پردازش آن 52.591 میکروثانیه است. این مقدار نسبتاً بالا است و می‌تواند باعث کاهش عملکرد OVS شود.

سایر توابع نیز زمان پردازش قابل‌توجهی دارند، اما میانگین زمان پردازش آن‌ها کمتر از ovs\_dp\_process\_packet است. به‌عنوان‌مثال، میانگین زمان پردازش تابع  ovs\_vport\_receive
برابر 65.088 میکروثانیه است که حدود 12 میکروثانیه بیشتر از ovs\_dp\_process\_packet است.

برای بهبود عملکرد OVS، باید راه‌هایی برای کاهش زمان پردازش تابع ovs\_dp\_process\_packet پیدا کرد. این کار می‌تواند با بهینه‌سازی کد تابع، استفاده از سخت‌افزار سریع‌تر یا تغییر الگوریتم پردازش بسته‌ها انجام شود.

چند راهکار برای کاهش زمان پردازش تابع ovs\_dp\_process\_packet به ذهن می‌رسد:

- بهینه‌سازی کد تابع: می‌توان کد تابع ovs\_dp\_process\_packet را بهینه‌سازی کرد تا از منابع سخت‌افزاری به‌طور کارآمدتر استفاده کند. این کار می‌تواند شامل استفاده از الگوریتم‌های سریع‌تر، حذف کدهای غیرضروری و استفاده از ساختارهای داده کارآمدتر باشد.
- استفاده از سخت‌افزار سریع‌تر: می‌توان از سخت‌افزار سریع‌تر برای پردازش بسته‌ها استفاده کرد. این کار می‌تواند شامل استفاده از پردازنده‌های سریع‌تر، رم سریع‌تر یا شبکه‌های سریع‌تر باشد.
- تغییر الگوریتم پردازش بسته‌ها: می‌توان الگوریتم پردازش بسته‌ها را تغییر داد تا کارآمدتر باشد. این کار می‌تواند شامل استفاده از الگوریتم‌های پردازش موازی یا استفاده از حافظه نهان برای ذخیره اطلاعات پرکاربرد باشد.

با توجه به این راهکارها، می‌توان گلوگاه ovs را برطرف کرد و عملکرد آن را بهبود بخشید.

در مورد ارتباط netcat بین دو namespace در ovs، گلوگاه اصلی مربوط به زمان پردازش تابع ovs_dp_process_packet است. این تابع مسئولیت پردازش بسته‌های ورودی به OVS را بر عهده دارد و در ارتباط netcat، بسته‌های شبکه ممکن است شامل داده‌های زیادی باشند. در این حالت، تابع ovs_dp_process_packet ممکن است زمان زیادی را صرف پردازش این داده‌ها کند.

برای بهبود عملکرد ارتباط netcat در ovs، می‌توان راهکارهای ذکرشده در بالا را برای کاهش زمان پردازش تابع ovs_dp_process_packet اعمال کرد. علاوه بر این، می‌توان از موارد زیر نیز برای بهبود عملکرد استفاده کرد:

- استفاده از جدول flow دقیق‌تر: می‌توان از یک جدول flow دقیق‌تر برای پیدا کردن مسیر بسته‌ها استفاده کرد. این کار می‌تواند به bridge ovs کمک کند تا مسیر بسته‌ها را بدون نیاز به ارسال upcall به user space پیدا کند و زمان پردازش بسته‌ها را کاهش دهد.
- استفاده از caching: می‌توان از caching برای ذخیره اطلاعات مربوط به مسیر بسته‌ها استفاده کرد. این کار می‌تواند به bridge ovs کمک کند تا مسیر بسته‌ها را سریع‌تر پیدا کند و نیاز به ارسال upcall به user space کمتر شود.
- استفاده از سخت‌افزار سریع‌تر: می‌توان از سخت‌افزار سریع‌تر برای پردازش بسته‌ها استفاده کرد. این کار می‌تواند به bridge ovs کمک کند تا مسیر بسته‌ها را سریع‌تر پیدا کند و نیاز به ارسال upcall به user space کمتر شود.

در اینجا چند راهکار برای کاهش زمان پردازش تابع ovs\_flow\_cmd\_dump ارائه‌شده است:

- استفاده از جدول flow کوچک‌تر: می‌توان از یک جدول flow کوچک‌تر برای ذخیره اطلاعات مربوط به جریان‌های شبکه استفاده کرد. این کار می‌تواند به تابع ovs\_flow\_cmd\_dump کمک کند تا تعداد کمتری ورودی را پردازش کند و زمان پردازش آن کاهش یابد.
- بهینه‌سازی کد تابع: می‌توان کد تابع ovs\_flow\_cmd\_dump را بهینه‌سازی کرد تا از منابع سخت‌افزاری به‌طور کارآمدتر استفاده کند. این کار می‌تواند شامل استفاده از الگوریتم‌های سریع‌تر، حذف کدهای غیرضروری و استفاده از ساختارهای داده کارآمدتر باشد.

با توجه به این راهکارها، می‌توان زمان پردازش تابع ovs\_flow\_cmd\_dump را کاهش داد.

.
 ### نتیجه گیری و تحلیل گلوگاه OVS

 |
| --- |
| سید حمیدرضا میریگانه
متین برهانی  |
| زمستان1402 |
</div>


