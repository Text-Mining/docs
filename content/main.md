---
weight: 10
title: API Reference
---

# شروع به کار

> دریافت کلید API برنامه‌نویسان برای استفاده از امکانات از سایت [https://app.text-mining.ir](https://app.text-mining.ir) می‌باشد

به مستدات سرویس [متن‌کاوی](https://text-mining.ir) خوش آمدید.

متن‌کاوی، مجموعه‌ای از خدمات ‌آنلاین مربوط به پردازش متون فارسی را در قالب Rest API در اختیار برنامه‌نویسان قرار می‌دهد. 

**توجه: برای استفاده از هر یک از امکانات وب سرویس متن‌کاوی باید کلید API داشته باشید** 

<aside class="success">
لیست کامل توابع API در قالب <code>swagger</code> در <a href="https://api.text-mining.ir">https://api.text-mining.ir</a> در دسترس است
</aside>

# احراز هویت

> مطمئن شوید که `YOUR_API_KEY` را با کلید API دریافتی خود جایگزین کرده‌اید

```csharp
var client=new RestClinet("https://api.text-mining.ir/api/Token/GetToken/apikey=YOUR_API_KEY");
var request = new RestRequest(Method.GET);
request.AddHeader("Cache-Control","no-cache");
IRestResponse response = client.Execute(request);
```

```python
import requests

url = "https://api.text-mining.ir/api/Token/GetToken"
querystring = {"apikey":"YOUR_API_KEY"}
headers = {'Cache-Control': "no-cache"}

response = requests.request("GET", url, headers=headers, params=querystring)
print(response.text)
```

```go

package main

import (
	"fmt"
	"net/http"
	"io/ioutil"
)

func main() {

	url := "https://api.text-mining.ir/api/Token/GetToken?apikey=YOUR_API_KEY"

	req, _ := http.NewRequest("GET", url, nil)
	req.Header.Add("cache-control", "no-cache")

	res, _ := http.DefaultClient.Do(req)

	defer res.Body.Close()
	body, _ := ioutil.ReadAll(res.Body)

	fmt.Println(res)
	fmt.Println(string(body))

}

```


```js
var request = require("request");

var options = { method: 'GET',
  url: 'https://api.text-mining.ir/api/Token/GetToken',
  qs: { apikey: 'YOUR_API_KEY' },
  headers: 
   {'cache-control': 'no-cache' } };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});


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
  'cache-control' => 'no-cache'
));

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}
```

```javascript

var data = null;

var xhr = new XMLHttpRequest();
xhr.withCredentials = true;

xhr.addEventListener("readystatechange", function () {
  if (this.readyState === 4) {
    console.log(this.responseText);
  }
});

xhr.open("GET", "https://api.text-mining.ir/api/Token/GetToken?apikey=YOUR_API_KEY");
xhr.setRequestHeader("cache-control", "no-cache");

xhr.send(data);

```

> در صورت موفق بودن درخواست، خروجی شبیه زیر برگردانده می‌شود که `TOKEN_VALUE` همان توکن احراز هویت شماست

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

بعد از دریافت خروجی که یک <code>JWT Token</code> است باید برای احراز هویت (Authentication & Authorization) برای استفاده از هر یک از توابع وب سرویس در <code>Header</code> مربوط به  <code>Http Request</code> خود برای آن تابع مقدار توکن دریافتی را وارد کنید. کدهای نمونه فراخوانی توابع وب سرویس این مورد را به شما نمایش می‌دهند
# پیش پردازش متون

## استانداردسازی متن ورودی

این تابع، متن ورودی را با یکسان‌سازی حروف عربی و فارسی ، اصلاح نیم‌فاصله‌‌ها و فاصله‌ها، تبدیل به متن استاندارد می‌کند. در این تابع بیشتر از ۹۰۰ جایگزینی کاراکتر بر روی متن ووردی انجام می‌شود

```csharp

//ToDo: NormalizePersianWord c# code

```

```python

#ToDo: NormalizePersianWord python code


```


### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PreProcessing/NormalizePersianWord`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
text |  | متن ورودی
replaceWildChar | true | آیا کاراکترها و علائم خاص با نسخه استاندارد آن جایگزین شوند
replaceDigit | true | آیا ارقام (اعداد) عربی و انگلیسی با ارقام استاندارد فارسی جایگزین شوند
refineSeparatedAffix | true | آیا نیم فاصله بین پسوند و پیشوند کلمات اصلاح شود
refineQuotationPunc | true | آیا فاصله گذاری استاندارد بین علائم و عبارت نقل قول اعمال شود

## شناسایی مرز جملات ساده
در این تابع شناسایی مرز جملات ساده/مرکب آن انجام می‌شود

```csharp

//ToDo: SentenceSplitter c# code

```

```python

#ToDo: SentenceSplitter python code


```


### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PreProcessing/SentenceSplitter`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
text |  | متن ورودی
checkSlang | true | آیا تبدیل محاوره ای به رسمی انجام شود
normalize | true | آیا نرمالسازی متن نیز انجام شود
normalizerParams |  | پارامترهای نرمالسازی متن ورودی
complexSentence | true | آیا بخشهای جملات مرکب غیرتودرتو نیز جدا شوند


## شناسایی مرز جملات و جداسازی کلمات

در این تابع شناسایی مرز جملات ساده/مرکب و جداسازی کلمات (توکن‌ها) آن انجام می‌شود


```csharp

//ToDo: SentenceSplitterAndTokenize c# code

```

```python

#ToDo: SentenceSplitterAndTokenize python code


```


### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PreProcessing/SentenceSplitterAndTokenize`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
text |  | متن ورودی
checkSlang | true | آیا تبدیل محاوره ای به رسمی انجام شود
normalize | true | آیا نرمالسازی متن نیز انجام شود
normalizerParams |  | پارامترهای نرمالسازی متن ورودی
complexSentence | true | آیا بخشهای جملات مرکب غیرتودرتو نیز جدا شوند


## جداسازی توکن‌های متن

در این تابع شناسایی مرز جملات ساده/مرکب آن انجام می‌شود


```csharp

//ToDo: Tokenize c# code

```

```python

#ToDo: Tokenize python code


```


### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/PreProcessing/Tokenize`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
inputText |  | متن ورودی

# بهبود متون


## اصلاح اشتباهات تایپی

این تابع وب سرویس، اشتباهات تایپی را بر اساس لیست کلمات خود اصلاح می‌کند و کلمه درست را برمی‌گرداند. این تابع مانند همه توابع دیگر نیاز به توکن JWT برای احراز هویت دارد

> به جای `TOKEN_VALUE` باید از توکن دریافتی تابع احراز هویت استفاده کنید

```csharp
var client=new RestClinet("https://api.text-mining.ir/api/PreProcessing/SpellCorrectors");
var request = new RestRequest(Method.POST);
request.AddHeader("Cache-Control","no-cache");
request.AddHeader("Authorization","TOKEN_VALUE");
request.AddHeader("Content-Type","application/json");
request.AddParameter("input", $"\"{inputText}\"", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```python
import requests

url = "https://api.text-mining.ir/api/PreProcessing/SpellCorrector"

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

`POST https://api.text-mining.ir/api/PreProcessing/SpellCorrectors`

### مدل دریافتی به عنوان پارامتر

عنوان | توضیح پارامتر
---------  | -----------
inputText  | متن ورودی که حاوی کلمات اشتباه است و بر اساس لیست کلمات صحیح، در خروجی بازگردانده می‌شود


## تبدیل محاوره به رسمی

کلمات محاوره‌ای درون متن به شکل (معادل) رسمی آنها تبدیل می‌شود


```csharp

//ToDo: FormalConverter c# code

```

```python

#ToDo: FormalConverter python code


```


### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextRefinement/FormalConverter`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
inputText |  | متن ورودی


## تشخیص کلمات رکیک و ناسزا

این تابع کلمات زشت/ناسزا (در دو سطح از اهانت) را تشخیص و تعیین میکند


```csharp

//ToDo: SwearWordTagger c# code

```

```python

#ToDo: SwearWordTagger python code


```


### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/TextRefinement/SwearWordTagger`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
inputText |  | متن ورودی

# زبان متون

## شناسایی نوع زبان

این تابع حدود ۶۰ نوع زبان را برای متن ورودی شناسایی کند
[https://iso639-3.sil.org/code_tables/639/data/all](https://iso639-3.sil.org/code_tables/639/data/all)

```csharp

//ToDo: Predict c# code

```

```python

#ToDo: Predict python code


```


### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/LanguageDetection/Predict`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
inputText |  | متن ورودی

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

//ToDo: NamedEntityRecognitionDetect c# code

```

```python

#ToDo: NamedEntityRecognitionDetect python code


```


### آدرس و نوع تابع وب‌سرویس

`POST https://api.text-mining.ir/api/NamedEntityRecognition/Detect`

### مدل دریافتی به عنوان پارامتر

عنوان | مقدار پیش‌فرض | توضیح پارامتر
--------- | ------- | -----------
inputText |  | متن ورودی
