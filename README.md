# ovs-tracing
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

# قسمت دوم: معماری  OVS
در این قسمت به بیان پیش نیازهایی که برای اجرای سناریوها نیاز است، پرداخته می ‏شود. در ادامه به معماری که طرح ریزی شده توضیحاتی ارائه می شود.‏

## پیش نیازهای OVS
پیش نیازهایی که برای اجرای سناریوها نیاز است عبارتند از: 

* Linux Machine (Native or Virtual Machine) (Linux Version 4.18 Or Above)
* Install OVS from [Official Site](https://docs.openvswitch.org/en/latest/intro/install/general/)
* VS Code

## پیاده سازی OVS:

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


# قسمت دوم: سناریوی Ping

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
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/perf_stat_13-kprobes.png" alt="perf with statistical ping request" />
  <figcaption><em>اطلاعات آماری با kprobe های تعریف شده با استفاده از دستور perf</em></figcaption>
</figure>
</div>


پس از بررسی و آنالیز صورت گرفته Event های زیر را بدست آوردیم:

- vs\_vport\_send
- vs\_vport\_receive
- vs\_dp\_process\_packet
- vs\_flow\_cmd\_dump

در ادامه گزارش به بررسی هر یک از رویدادها می پردازیم.

## رویداد ovs\_vport\_send:

ابتدا اقدام به تهیه Call Stack با استفاده از دستور perf کردیم که دستور آن در تصویر پایین بدست آمد:

<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/perf%2013%20kprobe%20just%20command.png" alt="perf Command" />
  <figcaption><em>دستور perf stat</em></figcaption>
</figure>
</div

در ادامه تصویری از نتایج بدست آمده نشان داده میشود.

![](RackMultipart20240109-1-uw5vs1_html_4714f4a27dc845d.png)

تصویر 15: نمایی از perf call graph

سپس برای اطلاع دقیق‌تر از روند ovs-vport-send، از دستور ftrace استفاده کردیم. برای اینکار در ضمن برقراری ارتباط بین Namespace ها دستور ftrace را نیز اجرا کردیم. نتایج در تصویر زیر نمایش داده می‌شود:

![](RackMultipart20240109-1-uw5vs1_html_62de287bc01b983c.png)

تصویر 16: نتیجه دستور ftrace برای تابع ovs\_vport\_send

که در این قسمت به تفضیل توابع مهمی که داخل این تابع صدا زده می‌شود، پرداخته می شود:

در ادامه به بررسی نقطه ای که در فضای Runtime، لینوکس پس دریافت بسته، اجرای روند را به OVS می دهد. برای این منظور به سورس کد لینوکس رفته و با جستجوی تابع \_\_netif\_receive\_skb\_core و آنالیز بدنه تابع مربوطه به متغیر rx\_handler رسیدیم که در این جا با استفاده از Switch case مربوطه، بسته را به OVS ارسال می کند. تصویر مربوط به این متغیر رادر ذیل قرار دادیم:


<div dir="rtl" style: align="center">
<figure>
  <img src="https://github.com/matinborhani/ovs-tracing/blob/main/screenshots/Ping%20Scenario/Source%20Code.png" alt="Joint Kernel and OVS Module" />
  <figcaption><em>نقطه اتصال کرنل لینوکس به OVS\_MODULE</em></figcaption>
</figure>
</div

 |
| --- |
| سید حمیدرضا میریگانه
متین برهانی  |
| زمستان1402 |
</div>
