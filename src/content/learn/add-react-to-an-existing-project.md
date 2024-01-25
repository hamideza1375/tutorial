---
title: اضافه کردن React به یک پروژه موجود
---

<Intro>

اگر می خواهید به پروژه موجود خود برخی از تعاملات را اضافه کنید، نیازی نیست که آن را به صورت کامل با React دوباره بنویسید. React را به استک موجود خود اضافه کنید و کامپوننت های تعاملی React را در هر کجا رندر کنید.

</Intro>

<Note>

شما برای توسعه محلی نیاز به نصب Node.js دارید. اگرچه می‌توانید React را آنلاین یا با یک صفحه HTML ساده امتحان کنید، اما به طور واقعی بیشتر ابزارهای جاوااسکریپتی که می‌خواهید برای توسعه استفاده کنید، نیازمند Node.js هستند.

</Note>

## استفاده از React برای یک (subroute) زیرمسیر کامل از وبسایت موجود شما {/*using-react-for-an-entire-subroute-of-your-existing-website*/}

بیایید فرض کنیم یک برنامه وب موجود در example.com با فناوری سرور دیگری (مانند Rails) ساخته شده است و می خواهید تمام مسیرهایی که با example.com/some-app/ شروع می شوند را به طور کامل با React پیاده سازی کنید.
اینجا چگونگی پیشنهاد ما برای تنظیم آن است:
1.بخش React برنامه خود را با استفاده از یک چارچوب مبتنی بر React بسازید.
2.مسیر اصلی را در پیکربندی چارچوب خود به عنوان /some-app مشخص کنید (اینجا چگونگی این کار در Next.js، Gatsby آمده است).
3.سرور خود را یا یک پروکسی را پیکربندی کنید تا تمام درخواست های زیر /some-app/ توسط برنامه React شما پردازش شود.

این اطمینان حاصل می کند که بخش React برنامه شما از بهترین شیوه های جاسب شده در این چارچوب ها بهره مند شود.
بسیاری از چارچوب های مبتنی بر React از نوع full-stack هستند و به برنامه React شما اجازه می دهند از سرور بهره مند شود. با این حال، حتی اگر نتوانید یا نخواهید JavaScript را در سرور اجرا کنید، می توانید از همین روش استفاده کنید. در این صورت، HTML/CSS/JS export (next export output برای Next.js، default برای Gatsby) را به جای /some-app/ سرو کنید.

## استفاده از React برای یک بخش از صفحه موجود شما {/*using-react-for-a-part-of-your-existing-page*/}

بیایید فرض کنیم که شما یک صفحه موجود دارید که با یک فناوری دیگر (به عنوان مثال یک فناوری سروری مانند Rails یا یک فناوری مشتری مانند Backbone) ساخته شده است و می خواهید اجزای تعاملی React را در جایی از آن صفحه رندر کنید. این یک روش رایج برای ادغام React است - در واقع، این چگونگی بسیاری از استفاده های React در Meta برای سال های بسیار بود!
شما می توانید این کار را در دو مرحله انجام دهید:
1. تنظیم یک محیط جاوااسکریپت که به شما اجازه می دهد از نحو JSX استفاده کنید، کد خود را به ماژول ها با نحو وارد/صادر تقسیم کنید و از بسته ها (به عنوان مثال، React) از ثبت بسته npm استفاده کنید.
2. اجزای React خود را در جایی که می خواهید آنها را در صفحه ببینید، رندر کنید.

روش دقیق بستگی به تنظیم صفحه موجود شما دارد، بنابراین بیایید جزئیات را بررسی کنیم.

### مرحله ۱: راه اندازی یک محیط جاوااسکریپت ماژولار {/*step-1-set-up-a-modular-javascript-environment*/}

محیط جاوااسکریپت مدولار به شما اجازه می‌دهد تا کامپوننت‌های React خود را در فایل‌های جداگانه بنویسید، به جای نوشتن کل کد‌های خود در یک فایل. این همچنین به شما اجازه می‌دهد که از تمامی بسته‌های فوق‌العاده‌ای که توسط توسعه‌دهندگان دیگر در ثبت‌نام npm منتشر شده‌اند، استفاده کنید - از جمله React خود! نحوه انجام این کار بستگی به تنظیمات موجود شما دارد:
اگر برنامه شما از قبل به فایل‌هایی تقسیم شده است که از دستورات import استفاده می‌کنند، سعی کنید از تنظیماتی که از قبل دارید استفاده کنید. بررسی کنید که آیا نوشتن <div /> در کد JS شما باعث خطای نحوی می‌شود یا خیر. اگر باعث خطای نحوی می‌شود، شاید نیاز به تبدیل کد جاوااسکریپت خود با Babel داشته باشید و فعال کردن تنظیمات Babel React برای استفاده از JSX.

اگر برنامه شما دارای یک تنظیمات موجود برای کامپایل ماژول‌های جاوااسکریپت نیست، آن را با Vite تنظیم کنید. جامعه Vite بسیاری از ادغام‌ها با چارچوب‌های پشتیبان را حفظ می‌کند، از جمله Rails، Django و Laravel. اگر چارچوب پشتیبان شما لیست نشده است، این راهنما را دنبال کنید تا به صورت دستی ادغام‌های Vite خود را با چارچوب پشتیبان خود انجام دهید.

برای بطور اطمینان بخش کردن که راه اندازی شما کار می‌کند، این دستور را در پوشه پروژه خود اجرا کنید:
<TerminalBlock>
npm install react react-dom
</TerminalBlock>

سپس این خطوط کد را به بالای فایل اصلی جاوااسکریپت خود اضافه کنید (ممکن است به نام index.js یا main.js باشد):

<Sandpack>

```html index.html hidden
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <!-- Your existing page content (in this example, it gets replaced) -->
  </body>
</html>
```

```js index.js active
import { createRoot } from 'react-dom/client';

// Clear the existing HTML content
document.body.innerHTML = '<div id="app"></div>';

// Render your React component instead
const root = createRoot(document.getElementById('app'));
root.render(<h1>Hello, world</h1>);
```

</Sandpack>

اگر کل محتوای صفحه شما با "سلام، دنیا!" جایگزین شد، همه چیز درست کار کرده است! ادامه بخوانید.

<Note>

ادغام یک محیط جاوا اسکریپت ماژولار در یک پروژه موجود برای اولین بار می تواند ترسناک باشد، اما ارزشش را دارد! اگر گیر کردید، منابع انجمن یا Vite Chat را امتحان کنید.
 [community resources](/community) or the [Vite Chat](https://chat.vitejs.dev/).

</Note>

### مرحله ۲: رندر کردن کامپوننت‌های React در هر نقطه‌ای از صفحه {/*step-2-render-react-components-anywhere-on-the-page*/}

در مرحله قبلی، این کد را در بالای فایل اصلی خود قرار دادید:

```js
import { createRoot } from 'react-dom/client';

// Clear the existing HTML content
document.body.innerHTML = '<div id="app"></div>';

// Render your React component instead
const root = createRoot(document.getElementById('app'));
root.render(<h1>Hello, world</h1>);
```

البته، شما در واقع نمی خواهید محتوای HTML موجود را پاک کنید!

این کد را حذف کنید

در عوض، احتمالاً می خواهید اجزای React خود را در مکان های خاصی در HTML خود رندر کنید. صفحه HTML خود را باز کنید (یا الگوهای سروری که آن را تولید می کنند) و یک ویژگی id منحصر به فرد به هر تگ اضافه کنید، به عنوان مثال:

```html
<!-- ... somewhere in your html ... -->
<nav id="navigation"></nav>
<!-- ... more html ... -->
```

این به شما امکان می دهد تا عنصر HTML مورد نظر خود را با استفاده از document.getElementById پیدا کنید و آن را به createRoot منتقل کنید تا بتوانید کامپوننت React خود را در داخل آن رندر کنید:

<Sandpack>

```html index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <p>This paragraph is a part of HTML.</p>
    <nav id="navigation"></nav>
    <p>This paragraph is also a part of HTML.</p>
  </body>
</html>
```

```js index.js active
import { createRoot } from 'react-dom/client';

function NavigationBar() {
  // TODO: Actually implement a navigation bar
  return <h1>Hello from React!</h1>;
}

const domNode = document.getElementById('navigation');
const root = createRoot(domNode);
root.render(<NavigationBar />);
```

</Sandpack>

توجه کنید که چگونه محتوای اصلی HTML از index.html حفظ می‌شود، اما جزء NavigationBar React شما اکنون در `<nav id="navigation">` از HTML شما ظاهر می‌شود. برای کسب اطلاعات بیشتر در مورد رندر کردن اجزای React در یک صفحه HTML موجود، اسناد استفاده از createRoot را بخوانید.

هنگامی که React را در یک پروژه موجود می‌پذیرید، معمول است که با اجزای تعاملی کوچک (مانند دکمه‌ها) شروع کنید و سپس به تدریج «به سمت بالا حرکت کنید» تا در نهایت کل صفحه شما با React ساخته شود. اگر زمانی به آن نقطه رسیدید، توصیه می‌کنیم بلافاصله پس از آن به یک چارچوب React مهاجرت کنید تا بیشترین بهره را از React ببرید.


## استفاده از React Native در یک برنامه تلفن همراه اصلی بومی {/*using-react-native-in-an-existing-native-mobile-app*/}

React Native همچنین می تواند به صورت تدریجی در برنامه های بومی موجود ادغام شود. اگر یک برنامه بومی موجود برای اندروید (جاوا یا کاتلین) یا iOS (Objective-C یا Swift) دارید، این راهنما را دنبال کنید تا یک صفحه React Native به آن اضافه کنید. [follow this guide](https://reactnative.dev/docs/integration-with-existing-apps) to add a React Native screen to it.
