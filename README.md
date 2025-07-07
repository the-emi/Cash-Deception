# Cash Deception

## What is?

<div dir="rtl">
<p style="text-align: justify;">اگه با ارسال درخواست به یه مسیر، اطلاعات حساس کاربر که توسط سی‌دی‌ان کش(cache) شدن رو به دست بیاریم، از آسیب‌پذیری کش‌دیسپشن استفاده کرده‌ایم.</p>
<p style="text-align: justify;">درواقع این آسیب‌پذیری به مهاجم اجازه می‌ده تا با سو استفاده از قابلیت کش(cache)، به محتوایی دسترسی پیدا کنه که نباید توسط سی‌دی‌ان(cache server) کش می‌شد.</p>
<ul>
<li style="text-align: justify;">مواردی وجود داره که به صورت مدیریت شده، اطلاعات حساس کاربر کش(cache) می‌شن. اما توی مرورگر خود کاربر و نه توسط سی‌دی‌ان(cache server).</li>
</ul>
</div>

## Why?

<div dir="rtl">
<p style="text-align: justify;">این آسیب‌پذیری زمانی به وجود می‌آد که بین سرور اصلی(origin server) و سرور کش(cache server)، عدم هناهنگی وجود داشته باشه.</p>
</div>

## How to exploit?

<div dir="rtl">
<p style="text-align: justify;">برای اکسپلویت(exploit) کردن این آسیب‌پذیری، مهاجم با دستکاری URL مسیری که اطلاعات حساس(Dynamic content) رو نشون می‌ده، سرور کش رو فریب می‌ده و باعث می‌شه تا پاسخی که نباید کش کنه رو کش کنه. حالا مهاجم یه لینک داره که هرکسی روی اون کلیک کنه، محتواش کش می‌شه. مهاجم هم با باز کردن همون URL محتوای کش شده رو به دست می‌آره.</p>
</div>

# Cash

## Cash Headers

**1.    Cache-Control**

| **Directive** | **توضیح** |
| --- | --- |
| public | اجازه کش کردن توسط  سرور کش و مرورگر |
| private | اجازه کش فقط برای مرورگر |
| no-store | کلا کش نکن |
| no-cache | کش بشه، ولی قبل از استفاده، با سرور چک کنه |
| max-age=0 | بلافاصله منقضی شه |
| s-maxage | برای سرور کش از مکس‌ایج اولویت بیشتری داره |

**2.    X-Cash**

| **X-Cache** **مقدار** | **توضیح** |
| --- | --- |
| hit | درخواست به سرور اصلی نرفته و از کش اومده |
| miss | کش نداشت. پاسخ از سرور اصلی اومد |
| dynamic | محتوای پاسخ توسط سرور به صورت داینامیک تولید شده؛ معمولاً کش نشده |
| refresh | کش قدیمی شده بود و تایید از سرور هم داشت، اما باید بررسی می‌شد |
| revalidate | کش موجود بود ولی نیاز به تایید از سرور داشت |
| stale | پاسخ کش‌شده استفاده شده، حتی با اینکه منقضی شده بوده |

**3.    Vary**

مشخص می‌کنه کش باید بر اساس چه پارامترهایی پاسخ رو تفکیک کنه.

## Cash Key

<p dir="rtl">سرور کش برای اینکه ببینه برای درخواستی که اومده کش داره یا نه، از مسیر(Path)، کوئری(Query)، متد(Method)، بعضی از هدر(Headers)ها و... یه کلید(Cash Key) می‌سازه. اینجوری اگه کلیدی که می‌سازه تکراری باشه، محتوای کلید ساخته شده قبلی رو نشون می‌ده.</p>
<p dir="rtl"> </p>

| **جزء** | **مثال** | **تأثیر بر کش شدن؟** |
| --- | --- | --- |
| **Host** | example.com | همیشه هست |
| **Path** | /index.html | همیشه هست |
| **Query String** | ?page=1 | معمولاً هست |
| **HTTP Method** | GET, POST | GET معمولا |
| **Headers** | Cookie, User-Agent, Accept-Encoding | Vary بسته به |
| **Cookies** | Cookie: session=xyz | Vary: Cookie |
| **Protocol (HTTP vs HTTPS)** | http:// یا https:// | معمولا |

## Cash Rules

<p dir="rtl">روشی که سرور کش باهاش کش رو انجام می‌ده، همیشه به یه صورت نیست. به مواردی که تعیین می‌کنن چه چیزهایی کش بشن، قوانین کش(Cash Rules) می‌گن.</p>
<p dir="rtl"> </p>

**1.    Static file extension rules**

<p dir="rtl">بعضی مواقع محتوا بر اساس پسوند(extension) کش می‌شن. یعنی سرور کش URL رو کامل نگاه می‌کنه و اگه مواردی مثل لیست زیر بودن، اون‌ها رو کش می‌کنه:</p>

<pre>
.7z .apk .avi .bin .bmp .bz2 .class .css .csv .doc .docx .dmg .eot .eps .exe .ejs .flac .gif .ico .iso .jar .js .jpg .jpeg .mid .midi .mk .mp3 .mp4 .ogg .otf .pdf .pict .pls .ps .ppt .pptx .rar .svg .svgz .swf .tar .tif .tiff .ttf .torrent .webp .woff .woff2 .xls .xlsx .zip .zst
</pre>

**2.    Static directory rules**

<p dir="rtl">بعضی مواقع هم سرور کش یه دایرکتوری خاص رو کش می‌کنه. برای مثال هر مسیری که بعد از static/ قرار بگیره رو کش می‌کنه. لیست زیر شامل مسیرهای رایج برای کش کردنه:</p>

<pre>/static
/assets
/wp-content
/media
/templates
/public
/shared</pre>

**3.    File name rules**

<p dir="rtl">یکی دیگه از روش‌ها، تعیین کردن یه سری فایل خاص برای کش شدنه. به این صورت که برای سرور کش تعریف می‌شه که فقط این فایل‌ها رو کش کن. لیست زیر فایل‌های رایج رو نشون می‌ده:</p>

<pre>index.html
robots.txt
humans.txt
security.txt
manifest.json
sitemap.xml
favicon.ico
apple-touch-icon.png
browserconfig.xml
styles.css
main.css
bootstrap.min.css
main.js
app.js
vendor.js
scripts.min.js
logo.png
banner.jpg
icon.svg
background.webp
favicon.ico
video.mp4
intro.webm
audio.mp3
fonts.woff
fonts.woff2
custom-font.ttf
icon-font.eot
config.json
data.json</pre>

# How to exploit

<p dir="rtl">برای تست این آسیب‌پذیری، لازمه تا یه سری کلمه کلیدی رو تعریف کنیم.</p>
<p dir="rtl"> </p>

### Origin Server Delimiter

<p dir="rtl">جداکننده(Delimiter) سرور اصلی، به کاراکتری گفته می‌شه که توسط سرور اصلی به عنوان جداکننده در نظر گرفته می‌شه. برای مثال اگه کاراکتر ; توسط سرور به عنوان جداکننده در نظر گرفته بشه:</p>
<p dir="rtl"> </p>

| URL | مسیری که سرور می‌بینه |
| --- | --- |
| /profile;test | /profile |

<p dir="rtl">همونطور که گفته شد، 3 نوع متفاوت برای اعمال کش(Cache Rules) وجود داره. همین موضوع باعث شده تا برای هرمورد، یک نوع اکسپلویت به وجود بیاد. در ادامه به بررسی هر کدام پرداخته شده است.</p>
<p dir="rtl"> </p>

### CacheServer Delimiter

<p dir="rtl">جداکننده(Delimiter) سرور کش، به کاراکتری گفته می‌شه که توسط سرور کش به عنوان جداکننده در نظر گرفته می‌شه. برای مثال اگه کاراکتر ; توسط سرور کش به عنوان جداکننده در نظر گرفته بشه:</p>
<p dir="rtl"> </p>

| URL | مسیری که سرور می‌بینه |
| --- | --- |
| /profile;test | /profile |

#### Delimiter List

<pre>
! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~ %21 %22 %23 %24 %25 %26 %27 %28 %29 %2A %2B %2C %2D %2E %2F %3A %3B %3C %3D %3E %3F %40 %5B %5C %5D %5E %5F %60 %7B %7C %7D %7E %00 %0A %09
</pre>

### Base Line

<p dir="rtl">درخواست اصلی(Base Line) درخواستیه که به صورت عادی زده می‌شه و از اون برای مقایسه رفتار سرور توی بقیه درخواست‌ها استفاده می‌شه.</p>
<p dir="rtl"> </p>

## Exploiting static extension cache rules

1. /<dynamic> => Base Line
  
2. /<dynamyc>{Random Character} => B
  
3. if Base Line = B => Change Endpoint
  
4. /<dynamic>{Delimiter}{Random Character} = Test
  
5. if Test = Base Line => vulnerable
  
  Exploit:
  
  <pre>/<dynamic>{Delimiter}{Static extension}</pre>
  

<p dir="rtl">اگه محتوای کش بر اساس پسوند مشخص بشه، اول یه درخواست ساده به مسیری که محتوای حساس رو برمی‌گردونه می‌زنیم. بعد به انتهای اون مسیر یه کاراکتر رندوم اضافه می‌کنیم.</p>
<p dir="rtl">!!! اگه درخواستی که کاراکتر اضافه داره، همون محتوای درخواست عادی رو نشون بده، یعنی داره ریدایرکت می‌شه و باید یه مسیر دیگه رو انتخاب کرد !!!</p>
<p dir="rtl">در ادامه، بین درخواست اصلی و کاراکتر رندوم، کاراکترهایی که ممکه برای سرور به عنوان جداکننده(Delimiter) در نظر گرفته بشه رو امتحان می‌کنیم. اگه کاراکتری پیدا کردیم که با ارسال اون بین درخواست اصلی و کاراکتر رندوم، محتوای اصلی رو نشون داد، یعنی اون کاراکتر توسط سرور به عنوان جداکننده تفسیر می‌شه. در ادامه، بعد از مسیر اصلی و کاراکتر جداکننده پسوندهای استاتیک رو امتحان می‌کنیم.</p>
<p dir="rtl">!!! این متد زمانی کار می‌کنه که کاراکتر جداکننده توسط سرور اصلی به عنوان جداکننده تفسیر بشه اما سرور کش اون رو بخشی از مسیر در نظر بگیره !!!</p>
<p dir="rtl"> </p>

### Tips

#### 1.

<p dir="rtl">!!! بعضی مواقع هم جداکننده رو انکد(encode) شده می‌فرستیم !!!</p>
<p dir="rtl"> </p>

#### 2.

| PHP | اگه از فریم‌ورک استفاده نشده باشه، هرچی آخر مسیر باشه، نادیده می‌گیره | /login == /login.php/* |
| --- | --- | --- |
| **Django** | **دستی نوشته می‌شه و دیسپاچر داره. بستگی داره چجوری نوشته شده باشه** | **-** |
| **ASP.NET** | **قابلیت فرندلی داره. اگه روشن باشه تمام پسوندها رو حذف و ریدایرکت می‌کنه. اما اگه خاموش باشه می‌شه به مسیر کاراکتر اضافه کرد** | **/login.aspx != /login** |

#### 3.

| Spring | ;   |
| --- | --- |
| Ruby on Rails | .   |
| OpenLiteSpeed | %00 |
| Nginx | %0  |

## Exploiting static directory cache rules

<p dir="rtl">اگه کش بر اساس مسیر درخواست بود، نیازه تا برای اکسپلویت از دایرکتوری تراورسال(Directory traversal) هم استفاده کرد. به طور کلی، بسته به اینکه سرور اصلی نسبت به تراورسال آسیب‌پذیر باشه یا سرور کش، دو نوع اکسپلویت وجود داره.</p>
<p dir="rtl"> </p>

<pre>encoded_dot = /..%2f </pre>

### Origin Server Or Cashe Server?

<p dir="rtl">برای اینکه ببینیم بین سرور اصلی و کش، کدوم دایرکتوری تراورسال می‌خورن، باید رفتار هرکدوم رو بررسی کنیم. بعد که متوجه شدیم، اکسپلویت اون رو بررسی کنیم.</p>
<p dir="rtl"> </p>
<p dir="rtl"> </p>

1. POST /<dynamic> = Base Line
  
2. POST /<dynamic>/test/{encoded_dot} = B
  
3. if Base Line == B => Origin Server
  
4. GET /<static> = Base Line
  
5. GET /<static>/test/{encoded_dot} =B
  
6. if B == HIT => Cashe Server
  

### Exploiting normalization by the origin server

<pre>/{dynamic}{delimeter}{encoded_dot}{static path}</pre>

### Exploiting normalization by the cache server

<pre>/{static}{encoded_dot}{dynamic}</pre>

### Tips

#### 1.

| Send | /te/..%2fst | /test |
| --- | --- | --- |
| /te/..%2fst | Cloud Flare | Cloud Front |
| /te/..%2fst | GCP | Azure |
| /te/..%2fst | Fastly | Imperva |
| /te/..%2fst | Apache | NginX |
| /te/..%2fst | Gunicorn | IIS |
| /te/..%2fst | Puma | OpenLite |

#### 2.

**encoded_dot**

/..%2f

/..%5c

%2f%2e%2e%2f

## Exploiting file name cache rules

<p dir="rtl">اگر کش بر اساس اسم فایل‌ها بود، دقیقا شبیه زمانیه که سرور اصلی نرمالزیشین رو انجام بده ولی سرور کش نه.</p>
<p dir="rtl"> </p>
<p dir="rtl"> </p>

Exploit

<pre>/{dynamic}{delimeter}{encoded_dot}{Static_File}</pre>

### Static Files

<pre>
index.html
robots.txt
humans.txt
security.txt
manifest.json
sitemap.xml
favicon.ico
apple-touch-icon.png
browserconfig.xml
styles.css
main.css
bootstrap.min.css
main.js
app.js
vendor.js
scripts.min.js
logo.png
banner.jpg
icon.svg
background.webp
favicon.ico
video.mp4
intro.webm
audio.mp3
fonts.woff
fonts.woff2
custom-font.ttf
icon-font.eot
config.json
data.json
</pre>
