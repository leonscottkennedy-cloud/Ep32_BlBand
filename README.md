# ESP32 Bluetooth Car Audio Receiver

تبدیل یک برد ESP32 (DevKit V1) به گیرنده صوتی بلوتوث (A2DP Sink) برای پخش موزیک گوشی در ماشین، از طریق ورودی AUX.

## امکانات

- دریافت صدا از طریق بلوتوث کلاسیک (پروفایل A2DP)
- خروجی صدا با استفاده از DAC داخلی ESP32 (بدون نیاز به قطعه DAC خارجی)
- قابل اتصال مستقیم به ورودی AUX 3.5mm ماشین

## سخت‌افزار مورد نیاز

| قطعه | توضیح |
|---|---|
| ESP32 DevKit V1 | برد اصلی پروژه |
| کابل USB | برای برنامه‌ریزی و تغذیه برد |
| جک AUX نری 3.5mm | با سیم‌های قابل لحیم‌کاری |
| ۲ عدد خازن ۱ میکروفاراد (۱۶ ولت یا بیشتر) | برای حذف ولتاژ DC از سیگنال صدا |
| سیم و هویه لحیم‌کاری | برای اتصالات |

## نقشه سیم‌کشی

جک AUX سه پایه دارد: چپ (L)، راست (R)، گراند (GND).

| پین ESP32 | مقصد | نکته |
|---|---|---|
| GPIO25 | چپ (L) جک AUX | از طریق خازن ۱µF |
| GPIO26 | راست (R) جک AUX | از طریق خازن ۱µF |
| GND | گراند (GND) جک AUX | اتصال مستقیم، بدون خازن |

⚠️ خازن‌ها اجباری هستند — بدون آن‌ها ولتاژ DC می‌تواند به آمپلی‌فایر ماشین آسیب بزند.

## پیش‌نیازهای نرم‌افزاری

این پروژه با **ESP-IDF** (فریم‌ورک رسمی Espressif) ساخته شده، نه Arduino IDE.

1. دانلود نصب‌کننده آفلاین ESP-IDF (نسخه ویندوز):
   https://github.com/espressif/idf-installer/releases
   (فایلی که در اسمش کلمه `offline` دارد، حدود ۱.۵ گیگابایت)

2. نصب‌کننده را اجرا کن و مراحل را طی کن تا آیکون **"ESP-IDF PowerShell"** روی دسکتاپ یا منوی Start ظاهر شود.

## راه‌اندازی و اجرای پروژه

### ۱. کلون کردن ریپازیتوری

```powershell
git clone https://github.com/USERNAME/esp32_bt_band.git
cd esp32_bt_band
```

### ۲. تنظیم نوع برد

داخل ترمینال ESP-IDF PowerShell:

```powershell
idf.py set-target esp32
```

### ۳. تنظیم خروجی صدا روی DAC داخلی

```powershell
idf.py menuconfig
```

مسیر زیر را طی کن:

```
A2DP Sink Internal Codec Example Configuration  --->
    A2DP Sink Output  --->
        (X) Internal DAC
```

با فلش‌ها به گزینه `Internal DAC` برو، `Space` یا `Enter` بزن تا انتخاب شود، سپس `ESC` بزن تا از منوها خارج شوی و در پیام نهایی `Save configuration?` گزینه `Y` را بزن.

### ۴. کامپایل کد

```powershell
idf.py build
```

### ۵. پیدا کردن پورت برد

از **Device Manager** ویندوز، بخش **Ports (COM & LPT)** را باز کن و شماره پورتی که کنارش نوشته `CP210x` یا `CH340` است را یادداشت کن (مثلاً `COM5`).

### ۶. آپلود کد روی برد

```powershell
idf.py -p COM5 flash
```

(به‌جای `COM5` شماره پورت خودت را بگذار)

### ۷. مشاهده لاگ زنده (اختیاری)

```powershell
idf.py -p COM5 monitor
```

برای خروج از حالت مانیتور: `Ctrl+]`

## استفاده

1. برد را با کابل USB به یک منبع تغذیه (پاوربانک، شارژر، یا پورت USB ماشین فقط برای برق) وصل کن.
2. بلوتوث گوشی را روشن کن و دنبال دستگاه بگرد — نامی شبیه `ESP_A2DP_SINK` ظاهر می‌شود.
3. به آن وصل شو.
4. جک AUX را به ورودی AUX ماشین وصل کن.
5. موزیک را از گوشی پلی کن.

## پاک کردن کامل حافظه برد

برای بازگرداندن برد به حالت خام (حذف کامل فایروِر):

```powershell
idf.py -p COM5 erase-flash
```

## منابع و فایل‌های جانبی

- [ESP-IDF Offline Installer (ویندوز)](https://github.com/espressif/idf-installer/releases)
- [مستندات رسمی ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/)
- [نمونه‌کد اصلی A2DP Sink (منبع این پروژه)](https://github.com/espressif/esp-idf/tree/master/examples/bluetooth/bluedroid/classic_bt/a2dp_sink_stream)
- [مستندات پین‌های ESP32 DevKit V1](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/hw-reference/esp32/get-started-devkitc.html)

## توضیحات فنی

- این پروژه بر پایه نمونه‌کد رسمی `a2dp_sink_stream` از مخزن ESP-IDF ساخته شده است.
- تراشه بلوتوث کلاسیک (A2DP) فقط روی ESP32 اصلی (Xtensa، مانند WROOM/DevKit) موجود است؛ نسخه‌های S2، S3 و C3 این قابلیت را ندارند.
- کیفیت صدای خروجی از DAC داخلی (۸ بیتی) پایین‌تر از یک DAC خارجی مانند PCM5102 است؛ برای کیفیت بهتر می‌توان بعداً به حالت I2S خارجی سوییچ کرد.

## لایسنس

این پروژه صرفاً برای استفاده شخصی و آموزشی تهیه شده است.
