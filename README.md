# کتابخانه پرداخت درون برنامه ای همراه پی
[![Release](https://jitpack.io/v/turkurt656/hamrahpay.svg)](https://jitpack.io/#turkurt656/hamrahpay)
[![GitHub license](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/TurKurT656/HamrahPay/blob/master/LICENSE.md)

<b><i>کتابخانه اندروید استودیو - پرداخت در مرورگر</i></b>
<br>
<br>
<br>
## تغییرات
 برای مشاهده تغییرات لینک زیر را باز کنید.<br>
 <a href="https://github.com/hamrahpay/HamrahPay/releases/" target="_blank">
 لیست تغییرات
 </a>

---
## ویدئو آموزشی

[آپارات همراه پی](http://www.aparat.com/hamrahpay)



## نحوه اضافه کردن کتابخانه
<p dir='rtl' align='right'>
برای استفاده از این کتابخانه، ابتدا کدهای زیر را در فایل build.gradle وارید نموده و پروژه را sync کنید:<br>
</p>

```gradle
repositories {
	maven { url "https://jitpack.io" }
}

dependencies {
	implementation 'com.github.hamrahpay:HamrahPay:3.0.5'
}
```
---

## نحوه استفاده از کتابخانه
<p dir='rtl' align='right'>
ابتدا یک اکتیویتی ساخته و یک دکمه در آن اضاقه نمایید . به عنوان مثال ما MainActivity.java را در نظر میگیریم
</p>
<p dir='rtl' align='right'>
سپس در فایل AndroidManifest.xml اصلی پروژه کد های زیر را به بلاک اکتیوتی مورد نظر اضافه میکنیم
<p>

```xml
<intent-filter>
                <action android:name="android.intent.action.VIEW"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <category android:name="android.intent.category.BROWSABLE"/>
                <data android:scheme="hamrahpay" android:host="your_app_unique_id"/>
</intent-filter>
```

**your_app_unique_id**
<p dir='rtl' align='right'>
را با یک عبارت دلخواه و کاملا یکتا جایگزین کنید.
در نظر داشته باشید که فقط و فقط میتوانید از حروف انگلیسی، اعداد و خط فاصله استفاده نمایید و استفاده از کارکترهایی مانند فاصله و علائم دیگر مجاز نیست. این عبارت باید برای هر یک از برنامه های شما به صورت یکتا باشد تا در روند پرداخت آن مشکلی ایجاد نشود.
</p>

<p dir='rtl' align='right'>
به عنوان مثال برای اکتیویتی MainActivity.java به شکل زیر میشود
</p>

```xml
<activity android:name=".MainActivity"
            android:launchMode="singleInstance">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
                <action android:name="android.intent.action.VIEW"/>
            </intent-filter>
            
            <!--Hamrahpay-->
            <intent-filter>
                <action android:name="android.intent.action.VIEW"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <category android:name="android.intent.category.BROWSABLE"/>
                <data android:scheme="hamrahpay" android:host="your_app_unique_id"/>
            </intent-filter>
</activity>
```
<p dir='rtl' align='right'>
در فایل اکتیویتی مورد نظر ابتدا یک آبجکت از همراه پی میسازیم
</p>

```java
private HamrahPay hp=null;
```

<p dir='rtl' align='right'>
سپس در کد دکمه ی پرداخت کدهای زیر را اضافه میکنیم
</p>

```java
//------- Start ------
        final String yourSKU = "hp_596c483885551620831476";   // شناسه کالای شما در سایت همراه پی
        hp = new HamrahPay(MainActivity.this)                // اکتیویتی که می خواهید از آنجا پرداخت انجام شود
                .sku(yourSKU)                               // اضافه کردن شناسه به صفحه پرداخت
                .setApplicationScheme("your_app_unique_id") // باید با مقدار دلخواه خود جایگزین نمایید.
                //.enableChromeCustomeTab()
                .listener(new HamrahPay.Listener() {        // لیسنر برای آگاهی شما از موفق بودن یا نبودن پرداخت
                    @Override
                    public void onErrorOccurred(String status, String message) {
                        // مشکلی در پرداخت روی داده است یا کاربر پرداخت را انجام نداده است
                        Toast.makeText(MainActivity.this,message,Toast.LENGTH_SHORT).show();
                        Log.e("HamrahPay", status + ": " + message);
                    }

                    @Override
                    public void onPaymentSucceed(String payCode) {
                        // کاربر با موفقیت پرداخت را انجام داده است
                        Toast.makeText(MainActivity.this,"پرداخت موفقیت آمیز بوده است",Toast.LENGTH_SHORT).show();
                        Log.i("HamrahPay", "payCode: " + payCode);
                    }

                    @Override
                    public void onGetLastPurchaseInfo(LastPurchase lastPurchase) {

                    }

                    @Override
                    public void onGetSupportInfo(SupportInfo supportInfo) {

                    }

                    @Override
                    public void onGetDeviceID(String deviceID) {

                    }
                })
                .setShouldShowHamrahpayDialog(false)
                .startPayment(); // شروع عملیات پرداخت
// ------ end ---------------
```
<p dir='rtl' align='right'>
در نهایت متد زیر را به اکتیویتی اضافه میکنیم
</p>

```java
@Override
protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);
    if(intent.getScheme().equals("hamrahpay"))
    {
        hp.verifyPayment();
    }
}
```

<p dir='rtl' align='right'>
کدهای نهایی برای درک بهتر 
</p>

```java
import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.widget.Toast;

import ir.devage.hamrahpay.HamrahPay;
import ir.devage.hamrahpay.LastPurchase;
import ir.devage.hamrahpay.SupportInfo;

public class MainActivity extends AppCompatActivity {

    private HamrahPay hp=null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //------- Start ------
        final String yourSKU = "hp_596c483885551620831476";   // شناسه کالای شما در سایت همراه پی
        hp= new HamrahPay(MainActivity.this)                // اکتیویتی که می خواهید از آنجا پرداخت انجام شود
                .sku(yourSKU)                               // اضافه کردن شناسه به صفحه پرداخت
                .setApplicationScheme("your_app_unique_id") // باید با مقدار دلخواه خود جایگزین نمایید.
                //.enableChromeCustomeTab()
                .listener(new HamrahPay.Listener() {        // لیسنر برای آگاهی شما از موفق بودن یا نبودن پرداخت
                    @Override
                    public void onErrorOccurred(String status, String message) {
                        // مشکلی در پرداخت روی داده است یا کاربر پرداخت را انجام نداده است
                        Toast.makeText(MainActivity.this,message,Toast.LENGTH_SHORT).show();
                        Log.e("HamrahPay", status + ": " + message);
                    }

                    @Override
                    public void onPaymentSucceed(String payCode) {
                        // کاربر با موفقیت پرداخت را انجام داده است
                        Toast.makeText(MainActivity.this,"پرداخت موفقیت آمیز بوده است",Toast.LENGTH_SHORT).show();
                        Log.i("HamrahPay", "payCode: " + payCode);
                    }

                    @Override
                    public void onGetLastPurchaseInfo(LastPurchase lastPurchase) {

                    }

                    @Override
                    public void onGetSupportInfo(SupportInfo supportInfo) {

                    }

                    @Override
                    public void onGetDeviceID(String deviceID) {

                    }
                })
                .setShouldShowHamrahpayDialog(false)
                .startPayment(); // شروع عملیات پرداخت
        // ------ end ---------------
    }

    //----------------------------------------------------------------------------------------------
    @Override
    protected void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        if(intent.getScheme().equals("hamrahpay"))
        {
            hp.verifyPayment();
        }
    }
    //----------------------------------------------------------------------------------------------
}

```

## توضیحات تکمیلی کد
<p dir='rtl' align='right'>1- yourSKU  را با شناسه کالای خود جایگزین نمایید</p>
<p dir='rtl' align='right'>2- your_app_unique_id را همانند شناسه ای وارد نمایید که در AndroidManifest.xml وارد کرده اید.  در صورتی که این مقدار مانند مقدار بالا نباشد و یا در برنامه های شما تکراری باشد عملیات پرداخت موفق نخواهد بود. (این مقدار باید در هر برنامه یکتا باشد)
<p dir='rtl' align='right'>3- در صورتی که میخواهید صفحه ی پرداخت در Chrome Custom tabs نمایش داده شود متد enableChromeCustomeTab() را فعال نمایید </p>
<p dir='rtl' align='right'>4- متد onPaymentSucceed زمانی فراخوانی میشود که عملیات پرداخت موفقیت آمیز بوده است و شما میتوانید کدهای خود را در این متد بنویسید
</p>
<p dir='rtl' align='right'>5- متد onErrorOccurred زمانی فراخوانی میشود که پرداخت به هر دلیلی موفقیت آمیز نبوده است
</p>
<p dir='rtl' align='right'>6- متد onGetLastPurchaseInfo زمانی فراخوانی میشود که شما درخواست دریافت اطلاعات آخرین پرداخت یک شناسه کالا  با استفاده از متد getLastPurchase()  داده باشید (مورد استفاده برای پرداخت های اشتراکی )
</p>

### متدهای کمکی


### پیکربندی نوع پرداخت
دو نوع پرداخت در همراه پی پشتیبانی میگردد:

1. پرداخت به ازای هر دستگاه :‌با این نوع پرداخت هر فردی که نرم افزار را خریداری میکند فقط بر روی همان دستگاهی که خریداری کرده است میتواند از نرم افزار استفاده نماید و پرداخت برای همان دستگاه قابل شناسایی میباشد.

2. پرداخت به ازای ایمیل :‌ در این نوع پرداخت مکانیزمی طراحی شده است که هر فردی که نرم افزار را خریداری میکند بتواند بر روی گوشی دیگری هم نصب کنم . به طور مثال با یک بار نرم افزار شما را روی گوشی و تبلت خود نصب نماید. این روش توسط گوگل پلی و دیگر مارکت ها استفاده میگردد.

برای اینکار به کد ساده کد زیر را اضافه کنید:
```java
.verificationType(HamrahPay.DEVICE_VERIFICATION)    // حالت اول  - (روش پیشنهادی)
.verificationType(HamrahPay.EMAIL_VERIFICATION)     // حالت دوم - حالت ایمیل
```
### فعال کردن ChromeCustomTabs به جای Browser
```java
.enableChromeCustomeTab()
```

### تغییر کد دستگاه به کد یکتا دلخواه
```java
.setCustomDeviceID(String deviceID) // در پارامتر آن کد دلخواه خود را وارد نمایید.
```
### چک کردن وضعیت پرداخت محصولات خریدنی . 
<p dir='rtl' align='right'>
مقدار بازگشتی از نوع Boolean است
</p>

```java
// context : Context
// sku : شناسه محصول
HamrahPay.isPremium(Context context,String sku); 
```

### متدهای مفید برای محصولات مصرف کردنی
```java
HamrahPay hamrahPay = new HamrahPay(MainActivity.this);
// sku : شناسه محصول مصرف کردنی
// value : یک مقدار عددی صحیح . مثلا عدد 100
String sku="hp_xxxxxxxxxx";شناسه محصول شما در همراه پی
Integer value=100;
hamrahPay.addScore(sku,value,this); // اضافه کردن امتیاز / سکه / بنزین و غیره
hamrahPay.minusScore(sku,value,this); // کسر کردن امتیاز / سکه / بنزین و غیره
hamrahPay.getScore(sku,this); // دریافت میزان امتیاز / سکه / بنزین و غیره باقیمانده
```
### دریافت اصلاعات آخرین خرید برنامه 
این متد زمانی کارایی دارد که محصولات شما نیازمند اشتراک زمانی هستند و با دادن مشخصات کالا اطلاعات آخرین پرداخت مشتری را برمیگرداند که شامل تاریخ آخرین خرید ، تاریخ روز ، شماره رسید پرداخت و غیره میباشد
```java
final String yourSKU = "hp_596c483885551620831476";   // شناسه کالای شما در سایت همراه پی
      hp =  new HamrahPay(MainActivity.this)                // اکتیویتی که می خواهید از آنجا پرداخت انجام شود
                .sku(yourSKU)                               // اضافه کردن شناسه به صفحه پرداخت
                .listener(new HamrahPay.Listener() {        // لیسنر برای آگاهی شما از موفق بودن یا نبودن پرداخت
                    @Override
                    public void onErrorOccurred(String status, String message) {
                        // مشکلی در پرداخت روی داده است یا کاربر پرداخت را انجام نداده است
                        Toast.makeText(MainActivity.this,message,Toast.LENGTH_SHORT).show();
                        Log.e("HamrahPay", status + ": " + message);
                    }

                    @Override
                    public void onPaymentSucceed(String payCode) {
                        // کاربر با موفقیت پرداخت را انجام داده است
                        Log.i("HamrahPay", "payCode: " + payCode);
                    }

                    @Override
                    public void onGetLastPurchaseInfo(LastPurchase lastPurchase) {
                        Log.i("status",lastPurchase.getStatus());
                        Log.i("message",lastPurchase.getMessage());
                        Log.i("error",lastPurchase.getError());
                        Log.i("Current date",lastPurchase.getCurrentDate());
                        Log.i("days ago",lastPurchase.getDaysAgo());
                        Log.i("purchase date",lastPurchase.getDate());
                    }

                    @Override
                    public void onGetSupportInfo(SupportInfo supportInfo) {

                    }

                    @Override
                    public void onGetDeviceID(String deviceID) {

                    }
                })
                .getLastPurchase();
```

### نمایش کد دستگاه به کاربر
این متد زمانی مفید است که کد دستگاه کاربر تغییر کرده است و شما نیازمند کد دستگاه کاربر برای ویرایش تراکنش آن میباشید 
این متد کد دستگاه را به کاربر نمایش میدهد
```java
	String Device_ID = HamrahPay.showDeviceID(MainActivity.this);
```


### کنترل خطا
در هنگام رویداد خطا میتوانید آنرا بصورت دستی کنترل کنید
```java
.listener(new HamrahPay.Listener() {
    @Override
    public void onErrorOccurred(String status, String message) {
        switch (status) {
            case HamrahPay.STATUS_BAD_PARAMETERS:
                break;
            case HamrahPay.STATUS_INVALID_TRANSACTION:
                break;
            case HamrahPay.STATUS_NO_NETWORK_OR_SERVER:
                break;
            case HamrahPay.STATUS_SELLER_BLOCKED:
                break;
            case HamrahPay.STATUS_TRY_AGAIN:
                break;
        }
        Log.e("HamrahPay", status + ": " + message);
    }

    @Override
    public void onPaymentSucceed(String payCode) {
    
    }
})
```

### کتابخانه Volley
اگر در برنامه خودتان از این کتابخانه استفاده می کنید، کافیست آنرا بصورت زیر به این کتابخانه اضافه کنید، در صورت اضافه نکردن برنامه بصورت پیش فرض برای خود صف جدیدی اضافه خواهد کرد
```java
RequestQueue myQueue = Volley.newRequestQueue(MainActivity.this)
new HamrahPay(MainActivity.this)
	...
	.requestQueue(myQueue)
	...
	.startPayment();
```

---
## پشتیبانی
لینک سایت همراه پی:

[![](https://hamrahpay.com/assets/home/theme/img/logo-red.png)](https://hamrahpay.com)

هرگونه سوالی در مورد کتابخانه دارید در قسمت مشکلات بپرسید.<br>
یک مثال بصورت یک برنامه اندرویدی نوشته شده است app همچنین در داخل فولدر 

<a href="https://github.com/hamrahpay/HamrahPay/issues" target="_blank">
لینک صفحه مشکلات
</a>

---
## لایسنس

    Copyright 2013 Square, Inc.

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

توسعه دهندگان

تیم همراه پی

آقای سامان ستاری ملکی
ایمیل :  turkurt656@gmail.com


