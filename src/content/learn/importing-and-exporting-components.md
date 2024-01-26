---
title: Importing and Exporting Components(import و export کامپوننت ها)
---

<Intro>

The magic of components lies in their reusability: you can create components that are composed of other components. But as you nest more and more components, it often makes sense to start splitting them into different files. This lets you keep your files easy to scan and reuse components in more places.
////
جادوی اجزا در قابلیت استفاده مجدد آنها نهفته است: شما می توانید اجزایی ایجاد کنید که از اجزای دیگر تشکیل شده باشند. اما از آنجایی که بیشتر و بیشتر اجزا را لانه می‌کنید، اغلب منطقی است که آنها را به فایل‌های مختلف تقسیم کنید. این به شما امکان می دهد فایل های خود را به راحتی اسکن کنید و از اجزای آن در مکان های بیشتری استفاده مجدد کنید.
////
جادوی کامپوننت‌ها در قابلیت استفاده مجدد آنها است: شما می‌توانید کامپوننت‌هایی را ایجاد کنید که از کامپوننت‌های دیگر تشکیل شده‌اند. اما هر چه بیشتر کامپوننت‌ها را تو در تو می‌کنید، اغلب منطقی است که شروع به تقسیم آنها به فایل‌های مختلف کنید. این به شما اجازه می‌دهد تا فایل‌های خود را به راحتی اسکن کنید و کامپوننت‌ها را در بیشتر مکان‌ها قابل استفاده مجدد کنید.

</Intro>

<YouWillLearn>

* What a root component file is
* How to import and export a component
* When to use default and named imports and exports
* How to import and export multiple components from one file
* How to split components into multiple files

////
* فایل کامپوننت ریشه چیست
* نحوه واردات و صادرات یک قطعه
* زمان استفاده از import و export پیش فرض و نامگذاری شده
* نحوه وارد کردن و صادرات چندین کامپوننت از یک فایل
* نحوه تقسیم اجزا به چندین فایل

</YouWillLearn>

## The root component file(فایل کامپوننت ریشه) {/*the-root-component-file*/}

In [Your First Component](/learn/your-first-component), you made a `Profile` component and a `Gallery` component that renders it:
////
در [Your First Component](/learn/your-first-component)، یک کامپوننت «profile» و یک کامپوننت «Gallery» ایجاد کردید که آن را رندر می کند:
<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

These currently live in a **root component file,** named `App.js` in this example. Depending on your setup, your root component could be in another file, though. If you use a framework with file-based routing, such as Next.js, your root component will be different for every page.
////
اینها در حال حاضر در یک **فایل مؤلفه ریشه** به نام «App.js» در این مثال زندگی می کنند. بسته به تنظیمات شما، کامپوننت ریشه شما ممکن است در فایل دیگری باشد. اگر از چارچوبی با مسیریابی مبتنی بر فایل مانند Next.js استفاده می کنید، مؤلفه ریشه شما برای هر صفحه متفاوت خواهد بود.
////
اینها در حال حاضر در یک فایل کامپوننت ریشه به نام App.js مستقر هستند. با توجه به تنظیمات شما، کامپوننت ریشه شما ممکن است در یک فایل دیگر باشد. اگر از یک چارچوب با مسیریابی مبتنی بر فایل مانند Next.js استفاده می‌کنید، کامپوننت ریشه شما برای هر صفحه متفاوت خواهد بود.


>در Next.js، هر کامپوننت ریشه معمولاً در یک فایل متفاوت قرار دارد به دلیل ساختار پیشفرض این فریمورک. این ساختار به تقسیم کامپوننت‌ها بر اساس صفحات و راه‌های دسترسی کمک می‌کند. به عنوان مثال، یک فایل برای هر صفحه و یک فایل برای هر کامپوننت ریشه مسیر. این ساختار کمک می‌کند تا کد به صورت منظم و قابل‌پیش‌بینی نگهداری شود و همچنین امکاناتی مانند پیش‌بینی و بهینه‌سازی را فراهم می‌کند135.

## Exporting and importing a component(export و import کردن یک کامپوننت) {/*exporting-and-importing-a-component*/}

What if you want to change the landing screen in the future and put a list of science books there? Or place all the profiles somewhere else? It makes sense to move `Gallery` and `Profile` out of the root component file. This will make them more modular and reusable in other files. You can move a component in three steps:

1. **Make** a new JS file to put the components in.
2. **Export** your function component from that file (using either [default](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_the_default_export) or [named](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_named_exports) exports).
3. **Import** it in the file where you’ll use the component (using the corresponding technique for importing [default](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#importing_defaults) or [named](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#import_a_single_export_from_a_module) exports).

Here both `Profile` and `Gallery` have been moved out of `App.js` into a new file called `Gallery.js`. Now you can change `App.js` to import `Gallery` from `Gallery.js`:

////////////////////////////

اگر بخواهید در آینده صفحه فرود را تغییر دهید و لیستی از کتاب های علمی را در آنجا قرار دهید چه؟ یا همه پروفایل ها را در جای دیگری قرار دهید؟ منطقی است که «Gallery» و «profile» را از فایل مؤلفه اصلی خارج کنید. این باعث می شود که آنها ماژولارتر و قابل استفاده مجدد در فایل های دیگر باشند. شما می توانید یک جزء را در سه مرحله جابجا کنید:

1. **یک فایل JS جدید برای قرار دادن کامپوننت ها بسازید.
2. **صادر کردن** جزء تابع خود را از آن فایل (با استفاده از [default](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_the_default_export) یا [named](https ://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_named_exports) صادرات).
3. **آن را در فایلی که در آن از مؤلفه استفاده می‌کنید، وارد کنید (با استفاده از تکنیک مربوطه برای وارد کردن [پیش‌فرض] (https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import #importing_defaults) یا [named] (https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#import_a_single_export_from_a_module) صادر می کند).

در اینجا «profile» و «Gallery» از «App.js» به فایل جدیدی به نام «Gallery.js» منتقل شده‌اند. اکنون می‌توانید «App.js» را برای وارد کردن «Gallery» از «Gallery.js» تغییر دهید:

//////////////////////////////

اگر می‌خواهید در آینده صفحه نخست را تغییر دهید و یک لیست از کتاب‌های علمی را در آن قرار دهید، چه اتفاقی می‌افتد؟ یا همه پروفایل‌ها را در جای دیگری قرار دهید؟ منطقی است که Gallery و پروفایل را از فایل کامپوننت اصلی خارج کنید. این کار باعث می‌شود که آن‌ها بیشتر ماژولار و قابل استفاده در فایل‌های دیگر شوند. شما می‌توانید یک کامپوننت را در سه مرحله منتقل کنید:

1. یک فایل JS جدید برای قرار دادن کامپوننت‌ها ایجاد کنید.
2. کامپوننت تابع خود را از آن فایل صادر کنید (با استفاده از صادرات پیش‌فرض یا نام‌گذاری شده).
3. آن را در فایلی که می‌خواهید از کامپوننت استفاده کنید، وارد کنید (از تکنیک مربوط به وارد کردن صادرات پیش‌فرض یا نام‌گذاری شده استفاده کنید).

در اینجا هر دو پروفایل و Gallery از App.js به یک فایل جدید به نام Gallery.js منتقل شده‌اند. حالا می‌توانید App.js را تغییر دهید تا Gallery را از Gallery.js وارد کنید:


<Sandpack>

```js App.js
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```

```js Gallery.js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Notice how this example is broken down into two component files now:
////
توجه کنید که این مثال چگونه به دو کامپوننت فایل مجزا تقسیم می شود:
////
این مثال به دو کامپوننت فایل تقسیم شده است.


1. `Gallery.js`:
     - Defines the `Profile` component which is only used within the same file and is not exported.
     - Exports the `Gallery` component as a **default export.**
2. `App.js`:
     - Imports `Gallery` as a **default import** from `Gallery.js`.
     - Exports the root `App` component as a **default export.**

     ////
     1. «Gallery.js»:
     - کامپوننت «پروفایل» را تعریف می کند که فقط در همان فایل استفاده می شود و صادر نمی شود.
     - کامپوننت «Gallery» را به عنوان یک **صادرات پیش‌فرض صادر می‌کند.**
2. «App.js»:
     - "Gallery" را به عنوان **واردات پیش فرض** از "Gallery.js" وارد می کند.
     - جزء اصلی «برنامه» را به صورت  **صادرات پیش فرض صادر می کند.**


<Note>

You may encounter files that leave off the `.js` file extension like so:
////
“شما ممکن است با فایل‌هایی روبرو شوید که پسوند فایل .js را ندارند، مانند این نمونه است.”

```js 
import Gallery from './Gallery';
```

Either `'./Gallery.js'` or `'./Gallery'` will work with React, though the former is closer to how [native ES Modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) work.
////
"'./Gallery.js" یا ""./Gallery"" با React کار خواهند کرد، اگرچه اولی به نحوه [ماژول‌های ES بومی] نزدیک‌تر است (https://developer.mozilla.org/docs/Web/ جاوا اسکریپت/راهنما/ماژول) کار می کنند.
////
“هر دو فایل ‘./Gallery.js’ و ‘./Gallery’ با React کار می‌کنند، اما فرمر به نزدیکی روش کار با ES Modules اصلی است.”

</Note>

<DeepDive>

#### Default vs named exports(export default در مقابل named exports) {/*default-vs-named-exports*/}

There are two primary ways to export values with JavaScript: default exports and named exports. So far, our examples have only used default exports. But you can use one or both of them in the same file. **A file can have no more than one _default_ export, but it can have as many _named_ exports as you like.**
////
دو راه اصلی برای صادرات مقادیر با جاوا اسکریپت وجود دارد: صادرات پیش فرض و صادرات نام. تاکنون، نمونه‌های ما فقط از صادرات پیش‌فرض استفاده کرده‌اند. اما می توانید از یکی یا هر دوی آنها در یک فایل استفاده کنید. **یک فایل نمی تواند بیش از یک صادرات _default_ داشته باشد، اما می تواند به تعداد دلخواه صادرات _named_ داشته باشد.**

![Default and named exports](/images/docs/illustrations/i_import-export.svg)

How you export your component dictates how you must import it. You will get an error if you try to import a default export the same way you would a named export! This chart can help you keep track:
////
نحوه صادرات کامپوننت خود تعیین می کند که چگونه باید آن را وارد کنید. اگر سعی کنید یک صادرات پیش فرض را به همان روشی که صادرات نامگذاری شده وارد می کنید، با خطا مواجه خواهید شد! این نمودار می تواند به شما کمک کند تا پیگیری کنید:

| Syntax           | Export statement                           | Import statement                          |
| -----------      | -----------                                | -----------                               |
| Default  | `export default function Button() {}` | `import Button from './Button.js';`     |
| Named    | `export function Button() {}`         | `import { Button } from './Button.js';` |

When you write a _default_ import, you can put any name you want after `import`. For example, you could write `import Banana from './Button.js'` instead and it would still provide you with the same default export. In contrast, with named imports, the name has to match on both sides. That's why they are called _named_ imports!
////
هنگامی که یک import پیش فرض می نویسید، می توانید هر نامی را که می خواهید پس از import قرار دهید. برای مثال، می‌توانید import Banana را از './Button.js' بنویسید و همچنان همان صادرات پیش‌فرض را برای شما فراهم می‌کند. در مقابل، با واردات نامگذاری شده، نام باید از هر دو طرف مطابقت داشته باشد. به همین دلیل است که به آنها واردات نامیده می شود!

**People often use default exports if the file exports only one component, and use named exports if it exports multiple components and values.** Regardless of which coding style you prefer, always give meaningful names to your component functions and the files that contain them. Components without names, like `export default () => {}`, are discouraged because they make debugging harder.
////
اگر فایل فقط یک کامپوننت را صادر کند، افراد اغلب از صادرات پیش‌فرض استفاده می‌کنند و اگر چندین کامپوننت و مقادیر را صادر می‌کند از صادرات با نام استفاده می‌کنند. صرف نظر از اینکه کدام سبک کدنویسی را ترجیح می دهید، همیشه نام های معنی دار را برای توابع کامپوننت خود و فایل هایی که حاوی آنها هستند قرار دهید. کامپوننت‌ های بدون نام، مانند صادرات پیش‌فرض () => {}، منع می‌شوند زیرا اشکال‌زدایی را سخت‌تر می‌کنند.
</DeepDive>

## Exporting and importing multiple components from the same file(صادرات و وارد کردن چندین کامپوننت از یک فایل) {/*exporting-and-importing-multiple-components-from-the-same-file*/}

What if you want to show just one `Profile` instead of a gallery? You can export the `Profile` component, too. But `Gallery.js` already has a *default* export, and you can't have _two_ default exports. You could create a new file with a default export, or you could add a *named* export for `Profile`. **A file can only have one default export, but it can have numerous named exports!**
////
اگر بخواهید به جای Gallery فقط یک «profile» را نشان دهید، چه؟ می‌توانید کامپوننت «پروفایل» را نیز صادر کنید. اما «Gallery.js» قبلاً یک صادرات *پیش‌فرض* دارد و نمی‌توانید _دو_ صادرات پیش‌فرض داشته باشید. می‌توانید یک فایل جدید با یک صادرات پیش‌فرض ایجاد کنید، یا می‌توانید یک صادرات *نام* برای «profile» اضافه کنید. **یک فایل فقط می‌تواند یک صادرات پیش‌فرض داشته باشد، اما می‌تواند چندین صادرات با نام داشته باشد!**

<Note>

To reduce the potential confusion between default and named exports, some teams choose to only stick to one style (default or named), or avoid mixing them in a single file. Do what works best for you!
////
برای کاهش سردرگمی احتمالی بین صادرات پیش‌فرض و نام‌گذاری شده، برخی از تیم‌ها فقط به یک سبک (پیش‌فرض یا نام‌گذاری شده) پایبند هستند یا از ترکیب آن‌ها در یک فایل منفرد اجتناب می‌کنند. چه کاری برای شما بهتر است!

</Note>

First, **export** `Profile` from `Gallery.js` using a named export (no `default` keyword):
////
ابتدا، **صادر کردن** «profile» از «Gallery.js» با استفاده از یک صادرات با نام (بدون کلمه کلیدی «پیش‌فرض»):

```js
export function Profile() {
  // ...
}
```

Then, **import** `Profile` from `Gallery.js` to `App.js` using a named import (with the curly braces):
////
سپس، **وارد کردن** «Profile» از «Gallery.js» به «App.js» با استفاده از یک import نامگذاری شده (با curly braces):

```js
import { Profile } from './Gallery.js';
```

Finally, **render** `<Profile />` from the `App` component:
////
در نهایت، `«<Profile/>»` را از کامپوننت `«App»` ** رندر کنید:

```js
export default function App() {
  return <Profile />;
}
```

Now `Gallery.js` contains two exports: a default `Gallery` export, and a named `Profile` export. `App.js` imports both of them. Try editing `<Profile />` to `<Gallery />` and back in this example:
////
اکنون `«Gallery.js»` حاوی دو صادرات است: یک صادرات پیش‌فرض `«Gallery»` و یک صادرات «پروفایل». `"App.js"` هر دوی آنها را وارد می کند. سعی کنید `«<Profile />»` را در `«<Gallery />»` ویرایش کنید و در این مثال برگردید:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <Profile />
  );
}
```

```js Gallery.js
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Now you're using a mix of default and named exports:
////
اکنون از ترکیبی از صادرات پیش‌فرض و نام‌گذاری شده استفاده می‌کنید:

* `Gallery.js`:
  - Exports the `Profile` component as a **named export called `Profile`.**
  - Exports the `Gallery` component as a **default export.**
* `App.js`:
  - Imports `Profile` as a **named import called `Profile`** from `Gallery.js`.
  - Imports `Gallery` as a **default import** from `Gallery.js`.
  - Exports the root `App` component as a **default export.**

  //////

  * «Gallery.js»:
  - کامپوننت "Profile" را به عنوان یک **صادرات با نام "profile" صادر می کند.**
  - کامپوننت «Gallery» را به عنوان یک **صادرات پیش‌فرض صادر می‌کند.**
* "App.js":
  - «Profile» را به عنوان یک **واردکننده با نام به نام «Profile»** از «Gallery.js» وارد می‌کند.
  - "Gallery" را به عنوان **واردات پیش فرض** از "Gallery.js" وارد می کند.
  - کامپوننت اصلی «برنامه» را به عنوان یک **صادرات پیش فرض صادر می کند.**

<Recap>

On this page you learned:

* What a root component file is
* How to import and export a component
* When and how to use default and named imports and exports
* How to export multiple components from the same file
//////////

در این صفحه یاد گرفتید:

* فایل کامپوننت ریشه چیست
* نحوه واردات و صادرات یک قطعه
* زمان و نحوه استفاده از واردات و صادرات پیش فرض و نامگذاری شده
* نحوه صادرات چندین مؤلفه از یک فایل
</Recap>



<Challenges>

#### Split the components further {/*split-the-components-further*/}

Currently, `Gallery.js` exports both `Profile` and `Gallery`, which is a bit confusing.

Move the `Profile` component to its own `Profile.js`, and then change the `App` component to render both `<Profile />` and `<Gallery />` one after another.

You may use either a default or a named export for `Profile`, but make sure that you use the corresponding import syntax in both `App.js` and `Gallery.js`! You can refer to the table from the deep dive above:
////

در حال حاضر، «Gallery.js» هم «Profile» و هم «Gallery» را صادر می کند، که کمی گیج کننده است.

کامپوننت `«Profile»` را به `«Profile.js»` خودش منتقل کنید، و سپس کامپوننت «برنامه» را تغییر دهید تا هر دو `«<Profile />»` و «Gallery />» را یکی پس از دیگری ارائه کند.

می‌توانید از یک صادرات پیش‌فرض یا یک صادرات با نام برای `«Profile»` استفاده کنید، اما مطمئن شوید که از دستور واردات مربوطه در `«App.js»` و `«Gallery.js»` استفاده می‌کنید! می توانید به جدول از شیرجه عمیق بالا مراجعه کنید:

| Syntax           | Export statement                           | Import statement                          |
| -----------      | -----------                                | -----------                               |
| Default  | `export default function Button() {}` | `import Button from './Button.js';`     |
| Named    | `export function Button() {}`         | `import { Button } from './Button.js';` |

<Hint>

Don't forget to import your components where they are called. Doesn't `Gallery` use `Profile`, too?
////
فراموش نکنید که کامپوننت های خود را در جایی که فراخوانی می شوند وارد کنید. آیا «Gallery» از «Profile» نیز استفاده نمی‌کند؟

</Hint>

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <div>
      <Profile />
    </div>
  );
}
```

```js Gallery.js active
// Move me to Profile.js!
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

After you get it working with one kind of exports, make it work with the other kind.
////
بعد از اینکه آن را با یک نوع صادرات کار کردید، آن را با نوع دیگر کار کنید.
<Solution>

This is the solution with named exports:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js Gallery.js
import { Profile } from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

This is the solution with default exports:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import Profile from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js Gallery.js
import Profile from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export default function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

</Solution>

</Challenges>
