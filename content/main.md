---
weight: 10
title: API Reference
---

# معرفی

> دریافت کلید API برنامه‌نویسان برای استفاده از امکانات از سایت [http://app.text-mining.ir](http://app.text-mining.ir) می‌باشد

به مستدات سرویس [متن‌کاوی](http://text-mining.ir) خوش آمدید.

متن‌کاوی، مجموعه‌ای از خدمات ‌آنلاین مربوط به پردازش متون فارسی را در قالب Rest API در اختیار برنامه‌نویسان قرار می‌دهد. 

**توجه: برای استفاده از هر یک از امکانات وب سرویس متن‌کاوی باید کلید API داشته باشید** 

<aside class="success">
لیست کامل توابع API در قالب <code>swagger</code> در <a href="http://api.text-mining.ir">http://api.text-mining.ir</a> در دسترس است
</aside>

# احراز هویت

> مطمئن شوید که `YOUR_API_KEY` را با کلید API دریافتی خود جایگزین کرده‌اید

```csharp
var client=new RestClinet("http://api.text-mining.ir/api/Token/GetToken/apikey=YOUR_API_KEY");
var request = new RestRequest(Method.GET);
request.AddHeader("Cache-Control","no-cache");
IRestResponse response = client.Execute(request);
```

```python
import requests

url = "http://api.text-mining.ir/api/Token/GetToken"
querystring = {"apikey":"YOUR_API_KEY"}
headers = {'Cache-Control': "no-cache"}

response = requests.request("GET", url, headers=headers, params=querystring)
print(response.text)
```


> در صورت موفق بودن درخواست، خروجی شبیه زیر برگردانده می‌شود که `TOKEN_VALUE` همان توکن احراز هویت شماست

```json

  {
    "token": "TOKEN_VALUE"
  }
```

اولین گام در استفاده از توابع API متن‌کاوی احراز هویت است. احراز هویت به وسیله توکن‌های JWT در درخواست http انجام می‌شود.
برای شروع باید یک [کلید API](http://app.text-mining.ir) دریافت کنید. سپس برای دریافت توکن تابع زیر را فراخوانی کنید


`GET http://api.text-mining.ir/api/Token?apikey=YOUR_API_KEY`


<aside class="notice">
عبارت  <code>YOUR_API_KEY</code> را با مقدار کلید API دریافتی خود جایگزین کنید
</aside>

بعد از دریافت خروجی که یک <code>JWT Token</code> است باید برای احراز هویت (Authentication & Authorization) برای استفاده از هر یک از توابع وب سرویس در <code>Header</code> مربوط به  <code>Http Request</code> خود برای آن تابع مقدار توکن دریافتی را وارد کنید. کدهای نمونه فراخوانی توابع وب سرویس این مورد را به شما نمایش می‌دهند
# پیش‌پردازش متون

## استانداردسازی متن ورود

این تابع، متن ورودی را با یکسان‌سازی حروف عربی و فارسی ، اصلاح نیم‌فاصله‌‌ها و فاصله‌ها، تبدیل به متن استاندارد می‌کند. در این تابع بیشتر از ۹۰۰ جایگزینی کاراکتر بر روی متن ووردی انجام می‌شود

```csharp

//ToDo: complete sample code

```


### آدرس و نوع تابع وب‌سرویس

`POST http://api.text-mining.ir/api/PreProcessing/NormalizePersianWord`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
text |  | متن ورودی
replaceWildChar | true | آیا کاراکترها و علائم خاص با نسخه استاندارد آن جایگزین شوند
replaceDigit | true | آیا ارقام (اعداد) عربی و انگلیسی با ارقام استاندارد فارسی جایگزین شوند
refineSeparatedAffix | true | آیا نیم فاصله بین پسوند و پیشوند کلمات اصلاح شود
refineQuotationPunc | true | آیا فاصله گذاری استاندارد بین علائم و عبارت نقل قول اعمال شود

## اصلاح اشتباهات تایپی

این تابع وب سرویس، اشتباهات تایپی را بر اساس لیست کلمات خود اصلاح می‌کند و کلمه درست را برمی‌گرداند. این تابع مانند همه توابع دیگر نیاز به توکن JWT برای احراز هویت دارد

> به جای `TOKEN_VALUE` باید از توکن دریافتی تابع احراز هویت استفاده کنید

```csharp
var client=new RestClinet("http://api.text-mining.ir/api/PreProcessing/SpellCorrectors");
var request = new RestRequest(Method.POST);
request.AddHeader("Cache-Control","no-cache");
request.AddHeader("Authorization","TOKEN_VALUE");
request.AddHeader("Content-Type","application/json");
request.AddParameter("input", $"\"{inputText}\"", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```python
import requests

url = "http://api.text-mining.ir/api/PreProcessing/SpellCorrector"

payload = "\"inputText\""
headers = {
    'Content-Type': "application/json",
    'Cache-Control': "no-cache"
    }

response = requests.request("POST", url, data=payload, headers=headers)
print(response.text)
```

> به جای `inputText` متن ورودی خود را قرار دهید مثلاً `شلام`





### آدرس و نوع تابع وب‌سرویس

`POST http://api.text-mining.ir/api/PreProcessing/SpellCorrectors`

### مدل دریافتی به عنوان پارامتر

عنوان | توضیح پارامتر
---------  | -----------
inputText  | متن ورودی که حاوی کلمات اشتباه است و بر اساس لیست کلمات صحیح، در خروجی بازگردانده می‌شود
