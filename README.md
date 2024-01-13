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


## معماری Namespace و OVS پیاده سازی شده در پروژه:
 <div dir="rtl" style: align="center">
<figure>
  <img  style="display:block" src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/arch.JPG" alt="Architecture"/>
<!--   <figcaption style="display:block"><em>‏معماری OVS</em></figcaption> -->
  
  *معماری OVS*
</figure>
</div>


# قسمت سوم: سناریوی Ping

## راه اندازی سناریو Ping:

در ابتدا با استفاده از Name Space های ساخته شده در قسمت قبل، اقدام به ping کردن VRF1 و VRF2 کردیم. که در شکل زیر مشهود است:
<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ping_initial.png" alt="Send ICMP Reqeust from VRF1 to VRF2" />
  <figcaption><em>ارسال دستور ICMP از طریق VRF1 به VRF2</em></figcaption>
</figure>
</div>


##  استخراج Function های صدا زده در حین اجرای دستور ping :

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

  *توابع مختص سناریو Ping*
  
</figure>
</div>


با توجه به نتایج بدست آمده در ادامه تمرکز خود را بر روی 13 رخدادی که در شکل مشخص شده و بیشترین تعداد فراخوانی را داشته اند، منعطف کردیم.

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


## رویداد ovs_vport_recieve

### تحلیل رویداد vport_recieve با دستور perf:
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

طبق call graph روال کار به این صورت است که کامپیوتر در namespace1 بسته ping را به پورت ovs ارسال میکند. تابع ovs\_vport\_receive بسته ping را از پورت ovs دریافت کرده، اگر بسته ping برای یک namespace معتبر باشد، تابع ovs\_vport\_receive بسته ping را به تابع netdev\_frame\_hook تحویل میدهد. تابع netdev\_frame\_hook بسته ping را از نظر صحت فرمت بررسی میکند. اگر بسته ping معتبر باشد، تابع netdev\_frame\_hook بسته ping را به تابع \_\_netif\_receive\_skb\_core تحویل میدهد. تابع \_\_netif\_receive\_skb\_core بسته ping رو به هسته لینوکس تحویل داده وهسته لینوکس بسته ping را بر اساس آدرس مقصدش پردازش میکند. در گام بعد تابع process\_backlogرا داریم که وظیفه دارد که بسته های شبکه را از صف backlog پردازش کند. این تابع ابتدا بسته ها را از صف backlog دریافت میکند. سپس، بر اساس آدرس مقصد آنها را پردازش میکند.
</div>
از آنجا که دو namespace ساخته شده در یک کرنل لینوکس هستند طبیعی است که ftrace توابع ovs-vport-send و ovs-vport-receive تقریبا مشابه یکدیگر باشد؛ اما در توابعی زیر با یکدیگر تفاوت دارند که در ادامه توضیح میدهیم:

- تابع ovs\_vport\_receive`: این تابع مربوط به ماژول هسته‌ی Open vSwitch (OVS) است. وظیفه‌ی این تابع دریافت پکت‌های شبکه‌ای است که از طریق یک پورت مجازی در OVS وارد می‌شوند. به عبارت دیگر، زمانی که یک بسته شبکه‌ای به یکی از پورت‌های مجازی OVS می‌رسد، این تابع فراخوانی می‌شود تا بسته را پردازش کند.

- تابع `netdev_frame_hook`: این تابع نیز بخشی از ماژول هسته‌ی OVS است و به عنوان یک hookعمل می‌کند که به فریم‌های شبکه‌ای که توسط دستگاه‌های شبکه‌ای مدیریت شده توسط OVS دریافت می‌شوند، واکنش نشان می‌دهد. این تابع در مسیریابی و انتقال فریم‌های شبکه در درون OVS نقش دارد.

- تابع `__netif_receive_skb_core`: این تابع بخشی از هسته‌ی لینوکس است و در پردازش اصلی بسته‌های شبکه‌ای درون هسته‌ی لینوکس نقش دارد. وظیفه‌ی این تابع پردازش اولیه بسته‌های شبکه‌ای است که توسط رابط‌های شبکه (مانند کارت‌های شبکه فیزیکی یا مجازی) دریافت می‌شوند. این تابع تعیین می‌کند که چگونه بسته‌ها باید در سیستم پردازش شوند، مثلاً ارسال به سمت مقصد نهایی یا اعمال سیاست‌های شبکه‌ای.

### تحلیل رویداد vport_recieve با دستور ftrace:
<div style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/ftrace_ovs_vport_recieve.png" alt="ftrace vport recieving" />


  *تصویر ftrace تابع ovs_vport_recieve*
</figure>
</div>


   در هسته Linux، بسته ها توسط تابع parse\_ethertype پردازش می شوند. این تابع نوع پروتکل بسته را شناسایی می کند.اگر نوع پروتکل بسته ICMP باشد، تابع do\_output بسته را به یک خروجی شبکه منتقل می کند. خروجی شبکه، بسته را به میزبان دیگر منتقل می کند. این میزبان بسته را از طریق شبکه ارسال می کند. در طرف دیگر شبکه، بسته توسط یک دستگاه شبکه دیگر دریافت می شود که بسته را به هسته Linux منتقل می کند. در هسته Linux، بسته توسط تابع ovs\_lookup\_vport میزبان مقصد را شناسایی می کند. سپس، بسته توسط تابع ovs\_vport\_send  ارسال می شود. میزبان مقصد بسته را به لایه شبکه منتقل می کند.
  
  
## رویداد ovs\_vport\_send:

### تحلیل رویداد vport_send با دستور perf:

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

## اتصال OVS Module به Kernel

در ادامه به بررسی نقطه ای که در فضای Runtime، لینوکس پس دریافت بسته، اجرای روند را به OVS می دهد. برای این منظور به سورس کد لینوکس رفته و با جستجوی تابع \_\_netif\_receive\_skb\_core و آنالیز بدنه تابع مربوطه به متغیر rx\_handler رسیدیم که در این جا با استفاده از Switch case مربوطه، بسته را به OVS ارسال می کند. تصویر مربوط به این متغیر را در ذیل قرار دادیم:


<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/Source%20Code.png" alt="Joint Kernel and OVS Module" />
  
  *نقطه اتصال کرنل لینوکس به OVS\_MODULE*
</figure>
</div>


## مبحث گلوگاه

### جدول آماری توابع اجرا شده

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

 ### نتیجه گیری و تحلیل گلوگاه OVS
 <p></p>
 |
| --- |
| سید حمیدرضا میریگانه
متین برهانی  |
| زمستان1402 |
</div>


