---
weight: 10
title: API Reference
---

# شروع به کار

> دریافت کلید API برنامه‌نویسان برای استفاده از امکانات از سایت [https://app.text-mining.ir](https://app.text-mining.ir) است.

> استفاده‌کنندگان رایگان از API، حداکثر مجاز به ارسال روزی ۱۰۰۰ [واحد](https://text-mining.ir/pricing#C2) درخواست (فراخوانی) و در هر ثانیه یک درخواست هستند.

> لیست کامل توابع API در قالب <code>swagger</code> در آدرس [https://api.text-mining.ir](https://api.text-mining.ir)  در دسترس است.

به مستندات سرویس [متن‌کاوی فارسی‌یار](https://text-mining.ir) خوش آمدید.

متن‌کاوی، مجموعه‌ای از خدمات ‌آنلاین مربوط به پردازش متون فارسی را در قالب Rest API در اختیار برنامه‌نویسان قرار می‌دهد.

<aside class="info">
برای اطلاع از میزان محدودیت‌های نسخه رایگان API به <a href="https://text-mining.ir/terms">صفحه قوانین و مقررات</a> مراجعه فرمایید.
</aside>

**توجه: برای استفاده از هر یک از امکانات وب سرویس متن‌کاوی باید کلید API داشته باشید.**
همچنین برای جلوگیری از تکرار در نمونه کد بعضی از زبان‌ها مانند پایتون، بعضی از توابع پایه‌ای مثل تابع فراخوانی Rest API یا دریافت توکن تنها یک بار اضافه شده‌اند.

<aside class="success">
<a href="https://aparat.com/v/BDcQV" target="_blank">این ویدئو در 5 دقیقه</a> نحوه دریافت کلید API و استفاده از آن را توضیح میدهد و جهت استفاده از سرویس‌های پردازش متن فارسی‌یار در سایر زبان‌های برنامه‌نویسی <a href="https://aparat.com/v/sxvF4" target="_blank">این ویدئوی 3 دقیقه‌ای</a> را تماشا بفرمایید.
</aside>

# احراز هویت

> مطمئن شوید که `YOUR_API_KEY` را با کلید API دریافتی خود جایگزین کرده‌اید.

```csharp

private string GetJWTToken()
{
    string jwtToken = string.Empty;
    HttpClient client = new HttpClient();
    var response = client.GetAsync($"https://api.text-mining.ir/api/Token/GetToken?apikey={YOUR_API_KEY}").Result;
    if (response.IsSuccessStatusCode)
    {
        string res = response.Content.ReadAsStringAsync().Result;
        jwtToken = (string) JObject.Parse(res)["token"];
    }
    return jwtToken;
}

```

```python

import requests
import json

def callApi(url, data, tokenKey):
    headers = {
        'Content-Type': "application/json",
        'Authorization': "Bearer " + tokenKey,
        'Cache-Control': "no-cache"
    }
    response = requests.request("POST", url, data=data.encode("utf-8"), headers=headers)
    return response.text

##################### Get Token by Api Key ##########################

baseUrl = "http://api.text-mining.ir/api/"
url = baseUrl + "Token/GetToken"
querystring = {"apikey":"YOUR_API_KEY"}
response = requests.request("GET", url, params=querystring)
data = json.loads(response.text)
tokenKey = data['token']

```

```java

OkHttpClient client = new OkHttpClient();

Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/Token/GetToken?apikey=YOUR_API_KEY")
  .get()
  .addHeader("Content-Type", "application/json")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/Token/GetToken');
$request->setMethod(HTTP_METH_GET);

$request->setQueryData(array(
  'apikey' => 'YOUR_API_KEY'
));

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Content-Type' => 'application/json'
));

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> در صورت موفق بودن درخواست، خروجی شبیه زیر برگردانده می‌شود که `TOKEN_VALUE` همان توکن احراز هویت شماست.

```json
{
  "token": "TOKEN_VALUE"
}
```

اولین گام در استفاده از توابع API متن‌کاوی احراز هویت است. احراز هویت به وسیله توکن‌های JWT در درخواست http انجام می‌شود.
برای شروع باید یک [کلید API](https://app.text-mining.ir) دریافت کنید. سپس برای دریافت توکن تابع زیر را فراخوانی کنید

`GET https://api.text-mining.ir/api/Token/GetToken?apikey=YOUR_API_KEY`

<aside class="notice">
عبارت  <code>YOUR_API_KEY</code> را با مقدار کلید API دریافتی خود جایگزین کنید
</aside>

بعد از دریافت خروجی که یک <code>JWT Token</code> است باید برای احراز هویت (Authentication & Authorization) برای استفاده از هر یک از توابع وب سرویس در <code>Header</code> مربوط به <code>Http Request</code> خود برای آن تابع مقدار توکن دریافتی را وارد کنید. کدهای نمونه فراخوانی توابع وب سرویس این مورد را به شما نمایش می‌دهند

# پیش پردازش متون

## استانداردسازی متن ورودی

این تابع، متن ورودی را با یکسان‌سازی حروف عربی و فارسی ، اصلاح نیم‌فاصله‌‌ها و فاصله‌ها، تبدیل به متن استاندارد می‌کند. در این تابع بیشتر از ۹۰۰ جایگزینی کاراکتر بر روی متن ووردی انجام می‌شود

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = "ولــے اگــر دڪــمــه مــڪــث رو لــمــس ڪــنــیــم ڪــلــا مــتــن چــنــدیــن صــفــحــه جــابــه جــا مــیــشــه و دیــگــه نــمــیــشــه فــهمــیــد ڪــدوم آیــه تــلــاوت مــی شود بــایــد چــے ڪــنــیــم؟.";
string json = JsonConvert.SerializeObject(new
    {
        Text = inputText,
        RefineQuotationPunc = false,
        RefineSeparatedAffix = true
    });
var response = client.PostAsync(_baseAddress + "PreProcessing/NormalizePersianWord", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

######################## Call Normalizer ############################

baseUrl = "http://api.text-mining.ir/api/"
url =  baseUrl + "PreProcessing/NormalizePersianWord"
payload = u"{\"text\":\"ولــے اگــر دڪــمــه مــڪــث رو لــمــس ڪــنــیــم ڪــلــا مــتــن چــنــدیــن صــفــحــه جــابــه جــا مــیــشــه و دیــگــه نــمــیــشــه فــهمــیــد ڪــدوم آیــه تــلــاوت مــی شود بــایــد چــے ڪــنــیــم؟.\", \"refineSeparatedAffix\":true}"
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"ولــے اگــر دڪــمــه مــڪــث رو لــمــس ڪــنــیــم ڪــلــا مــتــن چــنــدیــن صــفــحــه جــابــه جــا مــیــشــه و دیــگــه نــمــیــشــه فــهمــیــد ڪــدوم آیــه تــلــاوت مــی شود بــایــد چــے ڪــنــیــم؟.\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/PreProcessing/NormalizePersianWord")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/PreProcessing/NormalizePersianWord');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"ولــے اگــر دڪــمــه مــڪــث رو لــمــس ڪــنــیــم ڪــلــا مــتــن چــنــدیــن صــفــحــه جــابــه جــا مــیــشــه و دیــگــه نــمــیــشــه فــهمــیــد ڪــدوم آیــه تــلــاوت مــی شود بــایــد چــے ڪــنــیــم؟."
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: ولی اگر دکمه مکث رو لمس کنیم کلا متن چندین صفحه جابه جا میشه و دیگه نمیشه فهمید کدوم آیه تلاوت می‌شود باید چی کنیم؟.

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PreProcessing/NormalizePersianWord`

### مدل دریافتی به عنوان پارامتر

| عنوان                | مقدار پیش‌فرض | توضیح پارامتر                                                          |
| -------------------- | ------------- | ---------------------------------------------------------------------- |
| text                 |               | متن ورودی                                                              |
| replaceWildChar      | true          | آیا کاراکترها و علائم خاص با نسخه استاندارد آن جایگزین شوند            |
| replaceDigit         | true          | آیا ارقام (اعداد) عربی و انگلیسی با ارقام استاندارد فارسی جایگزین شوند |
| refineSeparatedAffix | true          | آیا نیم فاصله بین پسوند و پیشوند کلمات اصلاح شود                       |
| refineQuotationPunc  | true          | آیا فاصله گذاری استاندارد بین علائم و عبارت نقل قول اعمال شود          |

## شناسایی مرز جملات ساده

در این تابع شناسایی مرز جملات ساده/مرکب آن انجام می‌شود

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = "من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم";
string json = JsonConvert.SerializeObject(new
    {
        Text = inputText,
        Normalize = true,       // نرمالسازی نیز انجام شود
        NormalizerParams = new  // تنظیم پارامترهای نرمالسازی متن (اختیاری)
        {
            Text = "required but don't care :)",
            RefineQuotationPunc = false
        },
        CheckSlang = true,  // عملیات تبدیل متن محاوره‌ای به رسمی نیز انجام شود
        ComplexSentence = true  // جملات مرکب (غیرتودرتو) نیز جدا شوند
    });
var response = client.PostAsync(baseAddress + "PreProcessing/SentenceSplitter", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
var result = JsonConvert.DeserializeObject<List<string>>(resp);
Console.WriteLine(resp);

```

```python

##################### Call Sentence Splitter ########################
url =  baseUrl + "PreProcessing/SentenceSplitter"
payload = u'''{\"text\": \"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم\",
    \"checkSlang\": true,
    \"normalize\": true,
    \"normalizerParams\": {
        \"text\": \"don't care\",
        \"RefineQuotationPunc \": false
    },
    \"complexSentence\": true
}'''
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/PreProcessing/SentenceSplitter")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/PreProcessing/SentenceSplitter');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: ["من با دوستم به مدرسه می‌رفتیم","و در آنجا مشغول به تحصیل بودیم .","سپس به دانشگاه راه یافتیم"]

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PreProcessing/SentenceSplitter`

### مدل دریافتی به عنوان پارامتر

| عنوان            | مقدار پیش‌فرض | توضیح پارامتر                                |
| ---------------- | ------------- | -------------------------------------------- |
| text             |               | متن ورودی                                    |
| checkSlang       | true          | آیا تبدیل محاوره ای به رسمی انجام شود        |
| normalize        | true          | آیا نرمالسازی متن نیز انجام شود              |
| normalizerParams |               | پارامترهای نرمالسازی متن ورودی               |
| complexSentence  | true          | آیا بخشهای جملات مرکب غیرتودرتو نیز جدا شوند |

## شناسایی مرز جملات و جداسازی کلمات

در این تابع شناسایی مرز جملات ساده/مرکب و جداسازی کلمات (توکن‌ها) آن انجام می‌شود

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = "من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم";
string json = JsonConvert.SerializeObject(new
    {
        Text = inputText,
        Normalize = true,       // نرمالسازی نیز انجام شود
        NormalizerParams = new  // تنظیم پارامترهای نرمالسازی متن (اختیاری)
        {
            Text = "required but don't care :)",
            RefineQuotationPunc = false
        },
        CheckSlang = true,  // عملیات تبدیل متن محاوره‌ای به رسمی نیز انجام شود
        ComplexSentence = true  // جملات مرکب (غیرتودرتو) نیز جدا شوند
    });
var response = client.PostAsync(baseAddress + "PreProcessing/SentenceSplitterAndTokenize", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
var result = JsonConvert.DeserializeObject<List<List<string>>>(resp);
Console.WriteLine(resp);

```

```python

############# Call Sentence Splitter and Tokenizer #################
url =  baseUrl + "PreProcessing/SentenceSplitterAndTokenize"
payload = u'''{\"text\": \"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم\",
    \"checkSlang\": true,
    \"normalize\": true,
    \"normalizerParams\": {
        \"text\": \"don't care\",
        \"replaceWildChar\": true,
        \"replaceDigit\": true,
        \"refineSeparatedAffix\": true,
        \"refineQuotationPunc\": false
    },
    \"complexSentence\": true
}'''
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/PreProcessing/SentenceSplitterAndTokenize")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/PreProcessing/SentenceSplitterAndTokenize');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: [["من","با","دوستم","به","مدرسه","می‌رفتیم"],["و","در","آنجا","مشغول","به","تحصیل","بودیم","."],["سپس","به","دانشگاه","راه","یافتیم"]]

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PreProcessing/SentenceSplitterAndTokenize`

### مدل دریافتی به عنوان پارامتر

| عنوان            | مقدار پیش‌فرض | توضیح پارامتر                                |
| ---------------- | ------------- | -------------------------------------------- |
| text             |               | متن ورودی                                    |
| checkSlang       | true          | آیا تبدیل محاوره ای به رسمی انجام شود        |
| normalize        | true          | آیا نرمالسازی متن نیز انجام شود              |
| normalizerParams |               | پارامترهای نرمالسازی متن ورودی               |
| complexSentence  | true          | آیا بخشهای جملات مرکب غیرتودرتو نیز جدا شوند |

## جداسازی توکن‌های متن

در این تابع شناسایی مرز جملات ساده/مرکب آن انجام می‌شود

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = "من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم... سپس به دانشگاه راه یافتیم";
string json = JsonConvert.SerializeObject(inputText);
var response = client.PostAsync(baseAddress + "PreProcessing/Tokenize", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
var result = JsonConvert.DeserializeObject<List<string>>(resp);
Console.WriteLine(resp);

```

```python

######################## Call Tokenizer ############################

url =  baseUrl + "PreProcessing/Tokenize"
payload = u"\"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم\""
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\tinputText:\"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/PreProcessing/Tokenize")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/PreProcessing/Tokenize');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	inputText:"من با دوستم به مدرسه می رفتیم و در آنجا مشغول به تحصیل بودیم. سپس به دانشگاه راه یافتیم"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: ["من","با","دوستم","به","مدرسه","می","رفتیم","و","در","آنجا","مشغول","به","تحصیل","بودیم","...","سپس","به","دانشگاه","راه","یافتیم"]

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PreProcessing/Tokenize`

### مدل دریافتی به عنوان پارامتر

| عنوان     | مقدار پیش‌فرض | توضیح پارامتر |
| --------- | ------------- | ------------- |
| inputText |               | متن ورودی     |

# بهبود متون

## پیشنهاد کلمات صحیح با توجه به زمینه

این تابع اشتباهات تایپی/املایی متن ورودی با توجه به محتوای جمله اصلاح میکند

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = @"ستر حیوانی است که در صحرا با مقدار کم آب زندگی میکند";
string json = JsonConvert.SerializeObject(new
{
    Text = inputText,
    Normalize = true,
    CandidateCount = 3
});
var response = client.PostAsync(baseAddress + "TextRefinement/SpellCorrectorInContext", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

rl =  baseUrl + "TextRefinement/SpellCorrectorInContext"
payload = u'''{\"text\": \"ستر حیوانی است که در صحرا با مقدار کم آب زندگی میکند\",
            \"normalize\": true,
            \"candidateCount\": 3}'''
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"ستر حیوانی است که در صحرا با مقدار کم آب زندگی میکند\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextRefinement/SpellCorrectorInContext")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextRefinement/SpellCorrectorInContext');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"ستر حیوانی است که در صحرا با مقدار کم آب زندگی میکند"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: {شتر,سطر,سفر} {حیوانی,حیوان,یونانی} {است,دست,هست} که در {صحرا,صفرا,صدرا} با {مقدار,مدار,مقدر} کم آب {زندگی,بندگی,زدگی} {می‌کند,می‌کند,مکند}

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextRefinement/SpellCorrectorInContext`

### مدل دریافتی به عنوان پارامتر

| عنوان          | توضیح پارامتر                                                 |
| -------------- | ------------------------------------------------------------- |
| CandidateCount | تعداد کلمات کاندید برای جایگزین کردن با کلمه ناآشنای درون متن |
| Normalize      | آیا نرمالسازی متن نیز انجام شود                               |
| Text           | متن ورودی                                                     |

## اصلاح اشتباهات تایپی

این تابع وب سرویس، اشتباهات تایپی را بر اساس لیست کلمات خود اصلاح می‌کند و کلمه درست را برمی‌گرداند. این تابع مانند همه توابع دیگر نیاز به توکن JWT برای احراز هویت دارد

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = @"فهوه با مبات میجسبد";
string json = JsonConvert.SerializeObject(new
{
    Text = inputText,
    Normalize = true,
    CandidateCount = 3
});
var response = client.PostAsync(baseAddress + "TextRefinement/SpellCorrector", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

########################## Call SpellCorrector ##########################
url =  baseUrl + "TextRefinement/SpellCorrector"
payload = u'''{\"text\": \"فهوه با مبات میجسبد\",
            \"checkSlang\": true,
            \"normalize\": true,
            \"candidateCount\": 2}'''
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"فهوه با مبات میجسبد\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextRefinement/SpellCorrector")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextRefinement/SpellCorrector');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"فهوه با مبات میجسبد"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: قهوه با {نبات,ملات,مباد} {می‌چسبد,می‌جنبد}

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextRefinement/SpellCorrectors`

### مدل دریافتی به عنوان پارامتر

| عنوان     | توضیح پارامتر                                                                            |
| --------- | ---------------------------------------------------------------------------------------- |
| inputText | متن ورودی که حاوی کلمات اشتباه است و بر اساس لیست کلمات صحیح، در خروجی بازگردانده می‌شود |

## تبدیل محاوره به رسمی

کلمات محاوره‌ای درون متن به شکل (معادل) رسمی آنها تبدیل می‌شود

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = @"اگه اون گزینه رو کلیک کنین، یه پنجره باز میشه که میتونین رمز عبورتون رو اونجا تغییر بدین
    داشتم مي رفتم برم، ديدم گرفت نشست، گفتم بذار بپرسم ببينم مياد نمياد ديدم ميگه نميخوام بيام بذار برم بگيرم بخوابم نمیتونم بشینم.
    کتابای خودتونه
    نمیدونم چی بگم که دیگه اونجا نره
    ساعت چن میتونین بیایین؟";
string json = JsonConvert.SerializeObject(inputText);
var response = client.PostAsync(baseAddress + "TextRefinement/FormalConverter", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

################ Call Slang to Formal Converter ##################
url =  baseUrl + "TextRefinement/FormalConverter"
payload = u'''"اگه اون گزینه رو کلیک کنین، یه پنجره باز میشه که میتونین رمز عبورتون رو اونجا تغییر بدین
    داشتم مي رفتم برم، ديدم گرفت نشست، گفتم بذار بپرسم ببينم مياد نمياد ديدم ميگه نميخوام بيام بذار برم بگيرم بخوابم نمیتونم بشینم.
    کتابای خودتونه
    نمیدونم چی بگم که دیگه اونجا نره
    ساعت چن میتونین بیایین؟"'''
print(callApi(url, payload, tokenKey))

```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\n\t\"اگه اون گزینه رو کلیک کنین، یه پنجره باز میشه که میتونین رمز عبورتون رو اونجا تغییر بدین داشتم مي رفتم برم، ديدم گرفت نشست، گفتم بذار بپرسم ببينم مياد نمياد ديدم ميگه نميخوام بيام بذار برم بگيرم بخوابم نمیتونم بشینم.  کتابای خودتونه  نمیدونم چی بگم که دیگه اونجا نره ساعت چن میتونین بیایین؟\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextRefinement/FormalConverter")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextRefinement/FormalConverter');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:
	"اگه اون گزینه رو کلیک کنین، یه پنجره باز میشه که میتونین رمز عبورتون رو اونجا تغییر بدین داشتم مي رفتم برم، ديدم گرفت نشست، گفتم بذار بپرسم ببينم مياد نمياد ديدم ميگه نميخوام بيام بذار برم بگيرم بخوابم نمیتونم بشینم.  کتابای خودتونه  نمیدونم چی بگم که دیگه اونجا نره ساعت چن میتونین بیایین؟"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: اگر آن گزینه را کلیک کنید، یک پنجره باز می‌شود که می‌توانید رمز عبورتان را آنجا تغییر بدهید
> داشتم می‌رفتم بروم، دیدم گرفت نشست، گفتم بگذار بپرسم ببینم می‌آید نمی‌آید دیدم می‌گوید نمی‌خواهم بیایم بگذار بروم بگیرم بخوابم نمی‌توانم بنشینم.
> کتاب‌های خودتان است
> نمی‌دانم چه بگویم که دیگر آنجا نرود
> ساعت چند می‌توانید بیایید؟

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextRefinement/FormalConverter`

### مدل دریافتی به عنوان پارامتر

| عنوان     | مقدار پیش‌فرض | توضیح پارامتر |
| --------- | ------------- | ------------- |
| inputText |               | متن ورودی     |

## تشخیص کلمات رکیک و ناسزا

این تابع کلمات زشت/ناسزا (در دو سطح از اهانت) را تشخیص و تعیین میکند

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = "خـــــــرررررهای دیووووونههه  -   صکس  س.ک.س ی  \r\n بیپدرومادر";
string json = JsonConvert.SerializeObject(inputText);
var response = client.PostAsync(baseAddress + "TextRefinement/SwearWordTagger", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
var result = JsonConvert.DeserializeObject<Dictionary<string, string>>(resp);
Console.WriteLine(resp);

```

```python

################## Call Swear Word Detector ######################
url =  baseUrl + "TextRefinement/SwearWordTagger"
payload = u'"خـــــــرررررهای دیووووونههه  -   صکس  س.ک.س ی  \r\n بیپدرومادر"'
result = json.loads(callApi(url, payload, tokenKey))
## for item in result: ...
print(result)

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"خـــــــرررررهای دیووووونههه  -   صکس  س.ک.س ی  \\r\\n بیپدرومادر\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextRefinement/SwearWordTagger")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextRefinement/SwearWordTagger');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"خـــــــرررررهای دیووووونههه  -   صکس  س.ک.س ی  \\r\\n بیپدرومادر"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: {"خرررررهای":"MildSwearWord","دیووووونههه":"MildSwearWord","صکس":"StrongSwearWord","س.ک.س":"StrongSwearWord","بیپدرومادر":"StrongSwearWord"}

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextRefinement/SwearWordTagger`

### مدل دریافتی به عنوان پارامتر

| عنوان     | مقدار پیش‌فرض | توضیح پارامتر |
| --------- | ------------- | ------------- |
| inputText |               | متن ورودی     |

# زبان متون

## شناسایی نوع زبان

این تابع حدود ۶۰ نوع زبان را برای متن ورودی شناسایی کند
[https://iso639-3.sil.org/code_tables/639/data/all](https://iso639-3.sil.org/code_tables/639/data/all)

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject("شام ییبسن یا یوخ. سن سیز بوغازیمنان گتمیر شام. به به نه قشه یردی. ساغ اول سیز نئجه سیز. نئجه سن؟ اوشاقلار نئجه دیر؟ سلام لاری وار سیزین کی لر نئجه دیر. یاخچی");
var response = client.PostAsync(baseAddress + "LanguageDetection/Predict", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

##################### Call Language Detection #######################
url =  baseUrl + "LanguageDetection/Predict"
payload = u'"شام ییبسن یا یوخ. سن سیز بوغازیمنان گتمیر شام. به به نه قشه یردی. ساغ اول سیز نئجه سیز. نئجه سن؟ اوشاقلار نئجه دیر؟ سلام لاری وار سیزین کی لر نئجه دیر. یاخچی"'
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"شام ییبسن یا یوخ. سن سیز بوغازیمنان گتمیر شام. به به نه قشه یردی. ساغ اول سیز نئجه سیز. نئجه سن؟ اوشاقلار نئجه دیر؟ سلام لاری وار سیزین کی لر نئجه دیر. یاخچی\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/LanguageDetection/Predict")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/LanguageDetection/Predict');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"شام ییبسن یا یوخ. سن سیز بوغازیمنان گتمیر شام. به به نه قشه یردی. ساغ اول سیز نئجه سیز. نئجه سن؟ اوشاقلار نئجه دیر؟ سلام لاری وار سیزین کی لر نئجه دیر. یاخچی"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: azb

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/LanguageDetection/Predict`

### مدل دریافتی به عنوان پارامتر

| عنوان     | مقدار پیش‌فرض | توضیح پارامتر |
| --------- | ------------- | ------------- |
| inputText |               | متن ورودی     |

# موجودیت‌های نامدار متون

## شناسایی موجودیت‌های نامدار متن

لیست عبارات بهمراه برچسب نوع موجودیت. این برچسب یکی از موارد زیر می‌باشد:

- O - عدم وجود موجودیت
- B-PER - شروع موجودیت شخص یا نام فرد
- I-PER - ادامه موجودیت شخص یا نام فرد
- B-LOC - شروع موجودیت مکان یا محل خاص
- I-LOC - ادامه موجودیت مکان یا محل خاص
- B-ORG - شروع موجودیت نام سازمان یا تشکل
- I-ORG - ادامه موجودیت نام سازمان یا تشکل
- B-DAT - شروع موجودیت تاریخ یا زمان
- I-DAT - ادامه موجودیت تاریخ یا زمان
- B-EVE - شروع موجودیت رویداد یا حادثه
- I-EVE - ادامه موجودیت رویداد یا حادثه

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = "احمد عباسی به تحصیلات خود در دانشگاه آزاد اسلامی در مشهد ادامه داد";
var response = client.PostAsync(baseAddress + "NamedEntityRecognition/Detect",
                    new StringContent(JsonConvert.SerializeObject(inputText), Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;

// parse and generate result:
JArray tokens = JArray.Parse(resp);
var result = new StringBuilder();
foreach (JObject token in tokens.Children<JObject>())
    result.AppendLine($"{{{token["word"]},{token["tags"]["NER"]["item1"]}}}");
Console.WriteLine(result.ToString());

```

```python

############################ Call NER ###############################
url =  baseUrl + "NamedEntityRecognition/Detect"
payload = u'"احمد عباسی به تحصیلات خود در دانشگاه آزاد اسلامی در مشهد ادامه داد"'
result = json.loads(callApi(url, payload, tokenKey))
for phrase in result:
    print("("+phrase['word']+","+phrase['tags']['NER']['item1']+") ")

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\tinputText:\"احمد عباسی به تحصیلات خود در دانشگاه آزاد اسلامی در مشهد ادامه داد\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/NamedEntityRecognition/Detect")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/NamedEntityRecognition/Detect');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	inputText:"احمد عباسی به تحصیلات خود در دانشگاه آزاد اسلامی در مشهد ادامه داد"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: {احمد,B-PER} {عباسی,I-PER} {به,O} {تحصیلات,O} {خود,O} {در,O} {دانشگاه,B-ORG} {آزاد,I-ORG} {اسلامی,I-ORG} {در,O} {مشهد,I-LOC} {ادامه,O} {داد,O}

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/NamedEntityRecognition/Detect`

### مدل دریافتی به عنوان پارامتر

| عنوان     | مقدار پیش‌فرض | توضیح پارامتر |
| --------- | ------------- | ------------- |
| inputText |               | متن ورودی     |

# نقش کلمات متون

## تعیین برچسب نقش ادات سخن

این تابع عملیات برچسب زنی نقش (اسم، ضمیر، صفت، قید، فعل، ...) کلمات در جمله را انجام می دهد.

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string inputText = "احمد و علی به مدرسه پایین خیابان می رفتند.";
var response = client.PostAsync(baseAddress + "PosTagger/GetPos",
                    new StringContent(JsonConvert.SerializeObject(inputText), Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;

// parse and generate result:
JArray tokens = JArray.Parse(resp);
var result = new StringBuilder();
foreach (JObject token in tokens.Children<JObject>())
    result.AppendLine($"{{{token["word"]},{token["tags"]["POS"]["item1"]}}}");
Console.WriteLine(result.ToString());

```

```python

######################## Call POS-Tagger ############################
url =  baseUrl + "PosTagger/GetPos"
payload = u'"احمد و علی به مدرسه پایین خیابان می رفتند"'
result = json.loads(callApi(url, payload, tokenKey))
for phrase in result:
    print("("+phrase['word']+","+phrase['tags']['POS']['item1']+") ")

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"احمد و علی به مدرسه پایین خیابان می رفتند\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/NamedEntityRecognition/Detect")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/NamedEntityRecognition/Detect');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"احمد و علی به مدرسه پایین خیابان می رفتند"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: {احمد,N} {و,CON} {علی,N} {به,P} {مدرسه,N} {پایین,ADJ} {خیابان,N} {می‌رفتند,V} {.,DELM}

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PosTagger/GetPos`

### مدل دریافتی به عنوان پارامتر

| عنوان | مقدار پیش‌فرض | توضیح پارامتر |
| ----- | ------------- | ------------- |
| text  |               | متن ورودی     |

# ریشه‌یابی متون

## ریشه‌یابی عبارات

از این تابع برای ریشه‌یابی عبارات استفاده می‌شود.
به عنوان مثال، خروجی متن کد نمونه به شکل زیر است

`[ { "wordComment": "", "simplePos": "", "rootWords": ["دریانورد", "دریا"], "verbInformation": null, "sentenceNumber": 0, "wordNumberInSentence": 0, "startCharIndex": 0, "word": "دریانوردانی", "tags": null, "firstRoot": "دریانورد", "wordCount": 1, "length": 11, "isVerb": false, "isPunc": false }, { "wordComment": "", "simplePos": "", "rootWords": ["فرشته"], "verbInformation": null, "sentenceNumber": 0, "wordNumberInSentence": 0, "startCharIndex": 0, "word": "فرشتگان", "tags": null, "firstRoot": "فرشته", "wordCount": 1, "length": 7, "isVerb": false, "isPunc": false } ]`

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject(new
{
    Phrases = new[]
    {
        new { Word = "دریانوردانی" },
        new { Word = "فرشتگان" }
    },
    CheckSlang = false
});

var response = client.PostAsync(baseAddress + "Stemmer/LemmatizePhrase2Phrase", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

url =  baseUrl + "Stemmer/LemmatizePhrase2Phrase"
payload = u'{"phrases": [{ "word": "دریانوردانی" }, { "word": "فرشتگان" }], "checkSlang": false}'
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\"phrases\": [{ \"word\": \"دریانوردانی\" }, { \"word\": \"فرشتگان\" }], \"checkSlang\": false}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/Stemmer/LemmatizePhrase2Phrase")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/Stemmer/LemmatizePhrase2Phrase');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{"phrases": [{ "word": "دریانوردانی" }, { "word": "فرشتگان" }], "checkSlang": false}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/Stemmer/LemmatizePhrase2Phrase`

### مدل دریافتی به عنوان پارامتر

| عنوان           | مقدار پیش‌فرض | توضیح پارامتر                                                       |
| --------------- | ------------- | ------------------------------------------------------------------- |
| CheckSlang      |               | آیا در حین ریشه یابی کلمات محاوره‌ای تحلیل و به شکل رسمی تبدیل شوند |
| ComplexSentence |               | آیا بخش‌های جملات مرکب غیرتودرتو جداسازی شوند                       |
| Phrases         |               | ورودی به شکل لیست عبارات                                            |
| Text            |               | ورودی به شکل متن                                                    |

## ریشه یابی متن

برای نمایش ریشه افعال شکل گذشته ساده درنظر گرفته میشود و سایر اطلاعات صرفی فعل در متغیر ذیل وجود دارد:

خروجی کد نمونه به شرح زیر است:

‍‍‍‍‍`{دانشجویان,[دانشجو,دانش,دان]} {زیادی,[زیاد]} {به,[به]} {مدارس,[مدرسه]} {استعدادهای,[استعداد]} {درخشان,[درخشان]} {راه,[راه]} {پیدا,[پیدا]} {نخواهند کرد,[نکرد]} {که,[که]} {با,[با]} {مشکلات,[مشکل]} {بعدی,[بعد]} {مواجه,[مواجه]} {شوند,[شد]} {.,[]}`

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject(new
{
Text = "دانشجویان زیادی به مدارس استعدادهای درخشان راه پیدا نخواهند کرد که با مشکلات بعدی مواجه شوند.",
CheckSlang = false, // بررسی و تبدیل کلمات محاوره ای به شکل رسمی انجام نشود
ComplexSentence = false // شکستن جملات مرکب به چند جمله
});
var response = client.PostAsync(baseAddress + "Stemmer/LemmatizeText2Phrase", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
JArray tokens = JArray.Parse(resp);
var result = new StringBuilder();
foreach (JObject token in tokens.Children<JObject>())
{
result.Append(\$"{{{token["word"]},[");
foreach (var root in token["rootWords"].Children<JValue>())
result.Append(root.Value<string>()).Append(',');
if (result[result.Length - 1].Equals(','))
result.Remove(result.Length - 1, 1);
result.AppendLine("]}");
}
Console.WriteLine(result.ToString());

```

```python

url =  baseUrl + "Stemmer/LemmatizeText2Phrase"
payload = u'{"text": "دانشجویان زیادی به مدارس استعدادهای درخشان راه پیدا نخواهند کرد که با مشکلات بعدی مواجه شوند.", "checkSlang": false}'
result = json.loads(callApi(url, payload, tokenKey))
for phrase in result:
    print("("+phrase['word']+":"+phrase['firstRoot']+") ")

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\"text\": \"دانشجویان زیادی به مدارس استعدادهای درخشان راه پیدا نخواهند کرد که با مشکلات بعدی مواجه شوند.\", \"checkSlang\": false}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/Stemmer/LemmatizeText2Phrase")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/Stemmer/LemmatizeText2Phrase');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{"text": "دانشجویان زیادی به مدارس استعدادهای درخشان راه پیدا نخواهند کرد که با مشکلات بعدی مواجه شوند.", "checkSlang": false}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/Stemmer/LemmatizeText2Phrase`

### مدل دریافتی به عنوان پارامتر

| عنوان           | مقدار پیش‌فرض | توضیح پارامتر                                                       |
| --------------- | ------------- | ------------------------------------------------------------------- |
| CheckSlang      |               | آیا در حین ریشه یابی کلمات محاوره‌ای تحلیل و به شکل رسمی تبدیل شوند |
| ComplexSentence |               | آیا بخش‌های جملات مرکب غیرتودرتو جداسازی شوند                       |
| Phrases         |               | ورودی به شکل لیست عبارات                                            |
| Text            |               | ورودی به شکل متن                                                    |

## ریشه‌یابی یک متن

متن بهمراه کلمات ریشه (افعال به سوم شخص گذشته ساده تبدیل میشوند)

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject("دانشجویان زیادی به مدارس استعدادهای درخشان راه پیدا نخواهند کرد که با مشکلات بعدی مواجه شوند.");
var response = client.PostAsync(baseAddress + "Stemmer/LemmatizeText2Text", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

########################## Call Stemmer ##########################
url =  baseUrl + "Stemmer/LemmatizeText2Text"
payload = u'"دانشجویان زیادی به مدارس استعدادهای درخشان راه پیدا نخواهند کرد که با مشکلات بعدی مواجه شوند."'
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"دانشجویان زیادی به مدارس استعدادهای درخشان راه پیدا نخواهند کرد که با مشکلات بعدی مواجه شوند.\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/Stemmer/LemmatizeText2Text")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/Stemmer/LemmatizeText2Text');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"دانشجویان زیادی به مدارس استعدادهای درخشان راه پیدا نخواهند کرد که با مشکلات بعدی مواجه شوند."
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: دانشجو زیاد به مدرسه استعداد درخشان راه پیدا نکرد که با مشکل بعد مواجه شد.

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/Stemmer/LemmatizeText2Text`

### مدل دریافتی به عنوان پارامتر

| عنوان     | مقدار پیش‌فرض | توضیح پارامتر    |
| --------- | ------------- | ---------------- |
| inputText |               | ورودی به شکل متن |

## ریشه‌یابی لیست کلمات ورودی

لیستی از کلمات ورودی را ریشه‌یابی می‌کند.

خروجی کد نمونه به شرح زیر است:

‍`[ { "wordComment":"مشاجرات", "simplePos":"", "rootWords":[ "مشاجرات", "مشاجره" ], "verbInformation":null, "sentenceNumber":1, "wordNumberInSentence":0, "startCharIndex":0, "word":"مشاجرات", "tags":{ }, "firstRoot":"مشاجرات", "wordCount":1, "length":7, "isVerb":false, "isPunc":false }, { "wordComment":"دریانوردانی", "simplePos":"", "rootWords":[ "دریانورد", "دریا" ], "verbInformation":null, "sentenceNumber":1, "wordNumberInSentence":1, "startCharIndex":8, "word":"دریانوردانی", "tags":{ }, "firstRoot":"دریانورد", "wordCount":1, "length":11, "isVerb":false, "isPunc":false }, { "wordComment":"جزایر", "simplePos":"", "rootWords":[ "جزیره" ], "verbInformation":null, "sentenceNumber":1, "wordNumberInSentence":2, "startCharIndex":20, "word":"جزایر", "tags":{ }, "firstRoot":"جزیره", "wordCount":1, "length":5, "isVerb":false, "isPunc":false }, { "wordComment":"فرشتگان", "simplePos":"", "rootWords":[ "فرشته" ], "verbInformation":null, "sentenceNumber":1, "wordNumberInSentence":3, "startCharIndex":26, "word":"فرشتگان", "tags":{ }, "firstRoot":"فرشته", "wordCount":1, "length":7, "isVerb":false, "isPunc":false }, { "wordComment":"تنها", "simplePos":"", "rootWords":[ "تنها" ], "verbInformation":null, "sentenceNumber":1, "wordNumberInSentence":4, "startCharIndex":34, "word":"تنها", "tags":{ }, "firstRoot":"تنها", "wordCount":1, "length":4, "isVerb":false, "isPunc":false } ]`

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject(new[] { "مشاجرات", "دریانوردانی", "جزایر", "فرشتگان", "تنها" });
var response = client.PostAsync(baseAddress + "Stemmer/LemmatizeWords2Phrase", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

url =  baseUrl + "Stemmer/LemmatizeWords2Phrase"
payload = u'["دریانوردانی", "جزایر", "فرشتگان", "تنها"]'
result = json.loads(callApi(url, payload, tokenKey))
for phrase in result:
    print("("+phrase['word']+":"+phrase['firstRoot']+") ")

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "[\"دریانوردانی\", \"جزایر\", \"فرشتگان\", \"تنها\"]");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/Stemmer/LemmatizeWords2Phrase")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/Stemmer/LemmatizeWords2Phrase');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('["دریانوردانی", "جزایر", "فرشتگان", "تنها"]');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/Stemmer/LemmatizeWords2Phrase`

### مدل دریافتی به عنوان پارامتر

| عنوان | مقدار پیش‌فرض | توضیح پارامتر                  |
| ----- | ------------- | ------------------------------ |
| words |               | آرایه‌ای از کلمات در قالب رشته |

# شباهت متون

## استخراج مترادف‌ها

این تابع کلمات هم‌معنی (معادل مفهومی) با کلمه ورودی در فرهنگ لغت‌های مختلف را برمی‌گرداند

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject("احسان");
var response = client.PostAsync(baseAddress + "TextSimilarity/ExtractSynonyms", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
var result = JsonConvert.DeserializeObject<string[]>(resp);
Console.WriteLine(resp);

```

```python

###################### Call Text Similarity #########################
url =  baseUrl + "TextSimilarity/ExtractSynonyms"
payload = u"\"احسان\""
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"احسان\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextSimilarity/ExtractSynonyms")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextSimilarity/ExtractSynonyms');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"احسان"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: ["احسان","نیکی کردن","نیکوکاری", "بخشش","نیکی","خوبی","نیکویی","انعام", "نکویی","اکرام","صنع","فضل","لطف", "منت","نزل","نعمت","نیکی_کردن", "احسان (نام)","احسان_(نام)"]

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextSimilarity/ExtractSynonyms`

### مدل دریافتی به عنوان پارامتر

| عنوان | مقدار پیش‌فرض | توضیح پارامتر           |
| ----- | ------------- | ----------------------- |
| word  |               | کلمه ورودی در قالب رشته |

## محاسبه فاصله (کاراکتری) کلمات

این تابع میزان فاصله براساس کارکترهای مشابه را محاسبه میکند
دقت شود که شباهت با فاصله رابطه عکس دارد

Similarity = 1 - Distance

نحوه محاسبه بر اساس موارد زیر است

- HammingDistance (1)
- JaccardDistance (2)
- JaroDistance (4)
- JaroWinklerDistance (8)
- LevenshteinDistance (16)
- LongestCommonSubsequence (32)
- LongestCommonSubstring (64)
- NormalizedLevenshteinDistance (128)
- OverlapCoefficient (256)
- RatcliffObershelpSimilarity (512)
- SorensenDiceDistance (1024)
- TanimotoCoefficient (2048)

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject(new
{
    String1 = "ایرانی ها",
    String2 = "ایرانیان",
    DistanceFunc = 2         // JaccardDistance
});
var response = client.PostAsync(baseAddress + "TextSimilarity/GetSyntacticDistance", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
double similarity = 1.0 - double.Parse(resp);
Console.WriteLine(resp);

```

```python

###################### Call Text Similarity #########################

url =  baseUrl + "TextSimilarity/GetSyntacticDistance"
payload = u'{"string1": "ایرانی ها", "string2": "ایرانیان", "distanceFunc": 2}'  # JaccardDistance
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\"string1\": \"ایرانی ها\", \"string2\": \"ایرانیان\", \"distanceFunc\": 2}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextSimilarity/GetSyntacticDistance")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextSimilarity/GetSyntacticDistance');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{"string1": "ایرانی ها", "string2": "ایرانیان", "distanceFunc": 2}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: 0.333333343

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextSimilarity/GetSyntacticDistance`

### مدل دریافتی به عنوان پارامتر

| عنوان           | مقدار پیش‌فرض | توضیح پارامتر                                          |
| --------------- | ------------- | ------------------------------------------------------ |
| DistanceFunc    |               | نحوه محاسبه شباهت (کاراکتری) دو رشته                   |
| inDistThreshold |               | حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها |
| String1         |               | کلمه یا رشته ورودی اول                                 |
| String2         |               | کلمه یا رشته ورودی دوم                                 |

## محاسبه شباهت براساس کلمات مشابه

این تابع میزان شباهت را برمی‌گرداند

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject(new
{
    String1 = "حمله مغولها به ایران",
    String2 = "حملات مغولان به ایران",
    DistanceFunc = 2             // JaccardDistance
});
var response = client.PostAsync(baseAddress + "TextSimilarity/SentenceSimilarityBipartiteMatching", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
double distance = 1.0 - double.Parse(resp);
Console.WriteLine(resp);

```

```python

url =  baseUrl + "TextSimilarity/SentenceSimilarityBipartiteMatching"
payload = u'''{
    "string1": "حمله مغولها به ایران",
    "string2": "حملات مغولان به ایران",
    "distanceFunc": 2}'''  # JaccardDistance
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\n    \"string1\": \"حمله مغولها به ایران\", \n    \"string2\": \"حملات مغولان به ایران\", \n    \"distanceFunc\": 2}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityBipartiteMatching")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityBipartiteMatching');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
    "string1": "حمله مغولها به ایران",
    "string2": "حملات مغولان به ایران",
    "distanceFunc": 2}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: 0.80357146263122559

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityBipartiteMatching`

### مدل دریافتی به عنوان پارامتر

| عنوان           | مقدار پیش‌فرض | توضیح پارامتر                                          |
| --------------- | ------------- | ------------------------------------------------------ |
| DistanceFunc    |               | نحوه محاسبه شباهت (کاراکتری) دو رشته                   |
| inDistThreshold |               | حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها |
| String1         |               | کلمه یا رشته ورودی اول                                 |
| String2         |               | کلمه یا رشته ورودی دوم                                 |

## محاسبه شباهت براساس اشتراک کلمات

این تابع میزان شباهت را برمی‌گرداند

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject(new
{
    String1 = "حمله مغولها به ایران",
    String2 = "حملات مغولان به ایران",
    DistanceFunc = 2,             // JaccardDistance
    MinDistThreshold = 0.3        //حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها
});
var response = client.PostAsync(baseAddress + "TextSimilarity/SentenceSimilarityWithIntersectionMatching", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
double distance = 1.0 - double.Parse(resp);
Console.WriteLine(resp);

```

```python

url =  baseUrl + "TextSimilarity/SentenceSimilarityWithIntersectionMatching"
payload = u'''{
    "string1": "حمله مغولها به ایران",
    "string2": "حملات مغولان به ایران",
    "distanceFunc": 2,
    "minDistThreshold": 0.3}'''  # حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\n    \"string1\": \"حمله مغولها به ایران\", \n    \"string2\": \"حملات مغولان به ایران\", \n    \"distanceFunc\": 2,\n    \"minDistThreshold\": 0.3}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithIntersectionMatching")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithIntersectionMatching');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
    "string1": "حمله مغولها به ایران",
    "string2": "حملات مغولان به ایران",
    "distanceFunc": 2,
    "minDistThreshold": 0.3}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: 0.75

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithIntersectionMatching`

### مدل دریافتی به عنوان پارامتر

| عنوان           | مقدار پیش‌فرض | توضیح پارامتر                                          |
| --------------- | ------------- | ------------------------------------------------------ |
| DistanceFunc    |               | نحوه محاسبه شباهت (کاراکتری) دو رشته                   |
| inDistThreshold |               | حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها |
| String1         |               | کلمه یا رشته ورودی اول                                 |
| String2         |               | کلمه یا رشته ورودی دوم                                 |

## محاسبه شباهت دو جمله

این تابع میزان شباهت را برمی‌گرداند و همچنین این تابع دارای سرعت (کارایی) بالا و مناسب برای حجم بالای متون است

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject(new
{
    String1 = "حمله مغولها به ایران",
    String2 = "حملات مغولان به ایران"
});
var response = client.PostAsync(baseAddress + "TextSimilarity/SentenceSimilarityWithNearDuplicateDetector", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
double distance = 1.0 - double.Parse(resp);
Console.WriteLine(resp);

```

```python

url =  baseUrl + "TextSimilarity/SentenceSimilarityWithNearDuplicateDetector"
payload = u'''{
    "string1": "حمله مغولها به ایران",
    "string2": "حملات مغولان به ایران"}'''
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\n    \"string1\": \"حمله مغولها به ایران\", \n    \"string2\": \"حملات مغولان به ایران\"}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithNearDuplicateDetector")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithNearDuplicateDetector');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
    "string1": "حمله مغولها به ایران",
    "string2": "حملات مغولان به ایران"}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: 0.5

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithNearDuplicateDetector`

### مدل دریافتی به عنوان پارامتر

| عنوان           | مقدار پیش‌فرض | توضیح پارامتر                                          |
| --------------- | ------------- | ------------------------------------------------------ |
| DistanceFunc    |               | نحوه محاسبه شباهت (کاراکتری) دو رشته                   |
| inDistThreshold |               | حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها |
| String1         |               | کلمه یا رشته ورودی اول                                 |
| String2         |               | کلمه یا رشته ورودی دوم                                 |

## محاسبه شباهت براساس تعداد کلمات مشابه پشت سرهم

این تابع میزان شباهت را برمی‌گرداند

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject(new
{
    String1 = "حمله مغولها به ایران",
    String2 = "حملات مغولان به ایران",
    DistanceFunc = 2,             // JaccardDistance
    MinDistThreshold = 0.3        //حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها
});
var response = client.PostAsync(baseAddress + "TextSimilarity/SentenceSimilarityWithNGramMatching", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
double distance = 1.0 - double.Parse(resp);
Console.WriteLine(resp);

```

```python

url =  baseUrl + "TextSimilarity/SentenceSimilarityWithNGramMatching"
payload = u'''{
    "string1": "حمله مغولها به ایران",
    "string2": "حملات مغولان به ایران",
    "distanceFunc": 2,
    "minDistThreshold": 0.3}'''  # حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\n    \"string1\": \"حمله مغولها به ایران\", \n    \"string2\": \"حملات مغولان به ایران\", \n    \"distanceFunc\": 2,\n    \"minDistThreshold\": 0.3}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithNGramMatching")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithNGramMatching');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
    "string1": "حمله مغولها به ایران",
    "string2": "حملات مغولان به ایران",
    "distanceFunc": 2,
    "minDistThreshold": 0.3}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: 0.714285708963871

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextSimilarity/SentenceSimilarityWithNGramMatching`

### مدل دریافتی به عنوان پارامتر

| عنوان           | مقدار پیش‌فرض | توضیح پارامتر                                          |
| --------------- | ------------- | ------------------------------------------------------ |
| DistanceFunc    |               | نحوه محاسبه شباهت (کاراکتری) دو رشته                   |
| inDistThreshold |               | حداقل فاصله دو کلمه برای انطباق (یکسان فرض نمودن) آنها |
| String1         |               | کلمه یا رشته ورودی اول                                 |
| String2         |               | کلمه یا رشته ورودی دوم                                 |

# تحلیل نظرات

## تحلیل حسی (نسخه آزمایشی)

این تابع نوع حس جمله را به صورت یکی از سه شکل زیر برمی‌گرداند

- 0 Negative (منفی)
- 1 Neutral (خنثی)
- 2 Positive (مثبت)

```csharp

string baseAddress = "https://api.text-mining.ir/api/";
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetJWTToken());

string json = JsonConvert.SerializeObject("اصلا خوب نبود");
var response = client.PostAsync(baseAddress + "SentimentAnalyzer/SentimentClassifier", new StringContent(json, Encoding.UTF8, "application/json")).Result;
string resp = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(resp);

```

```python

################## Call Sentiment Classification ####################
url =  baseUrl + "SentimentAnalyzer/SentimentClassifier"
payload = u"\"اصلا خوب نبود\""
print(callApi(url, payload, tokenKey))

```

```java

OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = RequestBody.create(mediaType, "{\n\ttext:\"اصلا خوب نبود\"\n}");
Request request = new Request.Builder()
  .url("https://api.text-mining.ir/api/SentimentAnalyzer/SentimentClassifier")
  .post(body)
  .addHeader("Content-Type", "application/json")
  .addHeader("Authorization", "Bearer TOKEN_VALUE")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();

```

```php

<?php

$request = new HttpRequest();
$request->setUrl('https://api.text-mining.ir/api/SentimentAnalyzer/SentimentClassifier');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'Authorization' => 'Bearer TOKEN_VALUE',
  'Content-Type' => 'application/json'
));

$request->setBody('{
	text:"اصلا خوب نبود"
}');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}

```

> خروجی مثال کد بالا: 0

### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/SentimentAnalyzer/SentimentClassifier2`

### مدل دریافتی به عنوان پارامتر

| عنوان     | مقدار پیش‌فرض | توضیح پارامتر |
| --------- | ------------- | ------------- |
| inputText |               | متن ورودی     |
