# حقیقت درمورد Threads

> بیایید یک لحظه بی پرده و صریح باشیم، شما واقعا نمی خواهید از Curio استفاده کنید. همه چیز برابر است. احتمالا باید با thread ها برنامه نویسی کنید. بله، threads. این thrads. کاملا جدی. من شوخی نمی کنم.
>
> &nbsp;&nbsp;&nbsp; [Dave Beazley, “Developing with Curio”](https://oreil.ly/oXJaC)


<p style='text-align: justify;'>
    اگر قبلاً در مورد thread ها نشنیده اید، در اینجا یک توضیح اساسی وجود دارد: Thread ها یک ویژگی ارائه شده توسط یک سیستم عامل (OS) هستند که در اختیار توسعه دهندگان نرم افزار قرار می گیرد تا بتوانند به سیستم عامل نشان دهند که کدام بخش از برنامه خود را می توان به صورت موازی در آن اجرا کرد. سیستم عامل تصمیم می گیرد که چگونه منابع CPU را با هر یک از بخش ها به اشتراک بگذارد، درست همانطور که سیستم عامل تصمیم می گیرد منابع CPU را با سایر برنامه ها (فرایندهای) مختلف که به طور همزمان اجرا می شوند به اشتراک بگذارد.
</p>

<p style='text-align: justify;'>
    از آنجایی که شما در حال خواندن کتاب Asyncio هستید، باید در این قسمتی باشید که به شما می گویم: «thread ها وحشتناک هستند و هرگز نباید از آنها استفاده کنید، درسته؟ متاسفانه شرایط به این سادگی نیست. ما باید مزایا و خطرات استفاده از thread ها را بسنجیم، درست مانند انتخاب هر تکنولوژی ای.
</p>

<p style='text-align: justify;'>
    این کتاب اصلاً قرار نیست در رابطه با thread ها باشد. اما اینجا دو مشکل وجود دارد: Asyncio به‌عنوان جایگزینی برای threading ارائه می‌شود، بنابراین درک ارزش پیشنهادی بدون مقایسه دشوار است. و حتی زمانی که از Asyncio استفاده می کنید، همچنان احتمالاً مجبور خواهید بود با thread ها و فرآیندها سر و کار داشته باشید، بنابراین باید چیزی در مورد threading بدانید.
</p>

> زمینه این بحث که منحصراً همزمانی (concurrency) در برنامه های کاربردی شبکه است. multithreading پیشگیرانه در حوزه های دیگر نیز استفاده می شود، جایی که مبادلات کاملاً متفاوت است.

<hr>

## مزایای Threading

در این جا مهمترین مزایای استفاده از threading بیان می شود:

- *سهولت در خواند کد*

    - کد شما می‌تواند همزمان اجرا شود، اما همچنان در یک توالی خطی بسیار ساده و بالا به پایین از دستورات تنظیم می‌شود تا جایی که - نکته کلیدی - می‌توانید در بدنه توابع خود وانمود کنید که هیچ همزمانی اتفاق نمی‌افتد.
 
- *موازی سازی با حافظه مشترک*

    - کد شما می تواند از چندین CPU استفاده کند در حالی که thread ها هنوز حافظه مشترک دارند. برای مثال، در بسیاری از بارهای کاری که جابجایی مقادیر زیادی داده بین فضاهای حافظه جداگانه فرآیندهای مختلف بسیار پرهزینه است، مهم است.

- *دانش و کد موجود*

    - مجموعه وسیعی از دانش و بهترین شیوه ها برای نوشتن برنامه های کاربردی thread ای وجود دارد. همچنین تعداد زیادی کد "مسدود کننده" موجود وجود دارد که برای عملیات همزمان به multithreading بستگی دارد.

الان، با <i>پایتون</i>، نکته در مورد موازی بودن مشکوک است زیرا مفسر پایتون از یک قفل global به نام <i>global interpreter lock</i> (GIL) برای محافظت از وضعیت داخلی خود مفسر استفاده می کند. یعنی از اثرات فاجعه بار احتمالی شرایط مسابقه ای بین رشته های متعدد محافظت می کند. یکی از عوارض قفل این است که تمام رشته های برنامه شما را به یک CPU متصل می کند. همانطور که ممکن است تصور کنید، این هر گونه مزایای عملکرد موازی را نفی می کند (مگر اینکه از ابزارهایی مانند Cython یا Numba برای مانور در اطراف محدودیت استفاده کنید).

با این حال، اولین نکته در مورد سادگی درک شده بسیار مهم است: threading در پایتون بسیار ساده به نظر می‌رسد، و اگر قبلاً توسط باگ‌های غیرممکن در شرایط مسابقه ای نسوخته‌اید، threading یک مدل همزمانی بسیار جذاب ارائه می‌دهد. حتی اگر در گذشته رایت شده باشید، threading یک گزینه قانع کننده باقی می ماند زیرا احتمالاً یاد گرفته اید (راه سخت) چگونه کد خود را ساده و ایمن نگه دارید.

من فضایی برای ورود به برنامه نویسی thread ای ایمن در اینجا ندارم، اما به طور کلی، بهترین روش برای استفاده از thread ها استفاده از کلاس <code>ThreadPoolExecutor</code> به صورت همزمان است. <code>concurrent.futures</code>، تمام داده های مورد نیاز را از طریق متد <code>submit()</code> ارسال می کند. مثال ۱-۲ یک مدل پایه را نشان می دهد.

مثال ۱-۲. بهترین تمرین برای thread  زدن است


```python
from concurrent.futures import ThreadPoolExecutor as Executor

def worker(data):
    # <process the data>

with Executor(max_workers=10) as exe:
    future = exe.submit(worker, data)
```