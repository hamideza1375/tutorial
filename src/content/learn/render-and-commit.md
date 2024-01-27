---
title: Render and Commit
---

<Intro>

Before your components are displayed on screen, they must be rendered by React. Understanding the steps in this process will help you think about how your code executes and explain its behavior.
/////
قبل از نمایش کامپوننت‌های شما در صفحه، آن‌ها باید توسط React رندر شوند. درک مراحل این فرایند به شما کمک می‌کند تا در مورد اجرای کد خود فکر کنید و رفتار آن را توضیح دهید  .

</Intro>

<YouWillLearn>

* What rendering means in React
* When and why React renders a component
* The steps involved in displaying a component on screen
* Why rendering does not always produce a DOM update

/////
* رندرینگ در React به چه معناست
* زمان و چرا React یک کامپوننت را  رندر میکند 
* مراحل مربوط به نمایش یک کامپوننت بر روی صفحه نمایش
* چرا رندر همیشه به روز رسانی DOM تولید نمی کند

</YouWillLearn>

Imagine that your components are cooks in the kitchen, assembling tasty dishes from ingredients. In this scenario, React is the waiter who puts in requests from customers and brings them their orders. This process of requesting and serving UI has three steps:
/////
"تصور کنید که کامپوننت‌های شما در آشپزخانه، غذاهای خوشمزه‌ای را از مواد تهیه می‌کنند. در این سناریو، React مانند پیشخدمتی است که درخواست‌های مشتریان را دریافت کرده و سفارشات آن‌ها را به آشپزخانه می‌رساند. این فرایند از سه مرحله شامل درخواست کردن و سرو کردن UI تشکیل شده است."12. امیدوارم این پاسخ به شما کمک کرده باشد.

1. **Triggering** a render (delivering the guest's order to the kitchen)
2. **Rendering** the component (preparing the order in the kitchen)
3. **Committing** to the DOM (placing the order on the table)

///////
1. **راه اندازی** رندر (تحویل سفارش مهمان به آشپزخانه)
2. ** رندر ** کامپوننت (تهیه سفارش در آشپزخانه)
3. **تعهد ** به DOM (قرار دادن سفارش روی میز)

<IllustrationBlock sequential>
  <Illustration caption="Trigger" alt="React as a server in a restaurant, fetching orders from the users and delivering them to the Component Kitchen." src="/images/docs/illustrations/i_render-and-commit1.png" />
  <Illustration caption="Render" alt="The Card Chef gives React a fresh Card component." src="/images/docs/illustrations/i_render-and-commit2.png" />
  <Illustration caption="Commit" alt="React delivers the Card to the user at their table." src="/images/docs/illustrations/i_render-and-commit3.png" />
</IllustrationBlock>

## Step 1: Trigger a render(راه انداختن render) {/*step-1-trigger-a-render*/}

There are two reasons for a component to render:

1. It's the component's **initial render.**
2. The component's (or one of its ancestors') **state has been updated.**

/////
"دو دلیل برای رندر شدن یک کامپوننت وجود دارد:

رندر اولیه کامپوننت است.
وضعیت کامپوننت (یا یکی از اجداد آن) به‌روز شده است.

### Initial render(رندر اولیه) {/*initial-render*/}

When your app starts, you need to trigger the initial render. Frameworks and sandboxes sometimes hide this code, but it's done by calling [`createRoot`](/reference/react-dom/client/createRoot) with the target DOM node, and then calling its `render` method with your component:

//////
"رندر اولیه زمانی که برنامه‌ی شما شروع به کار می‌کند، باید رندر اولیه را فراخوانی کنید. برخی از فریم‌ورک‌ها و محیط‌های شنی، این کد را پنهان می‌کنند، اما با فراخوانی createRoot با گره‌ی مقصد DOM و سپس فراخوانی متد render با کامپوننت خود، این کار انجام می‌شود."1. امیدوارم این پاسخ به شما کمک کرده باشد.

<Sandpack>

```js index.js active
import Image from './Image.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Image />);
```

```js Image.js
export default function Image() {
  return (
    <img
      src="https://i.imgur.com/ZF6s192.jpg"
      alt="'Floralis Genérica' by Eduardo Catalano: a gigantic metallic flower sculpture with reflective petals"
    />
  );
}
```

</Sandpack>

Try commenting out the `root.render()` call and see the component disappear!

/////

سعی کنید برای فراخوانی root.render() نظر بدهید و ببینید که کامپوننت ناپدید می شود!

### Re-renders when state updates(رندر مجدد زمان بروزرسانی state) {/*re-renders-when-state-updates*/}

Once the component has been initially rendered, you can trigger further renders by updating its state with the [`set` function.](/reference/react/useState#setstate) Updating your component's state automatically queues a render. (You can imagine these as a restaurant guest ordering tea, dessert, and all sorts of things after putting in their first order, depending on the state of their thirst or hunger.)

/////
“بعد از رندر اولیه‌ی کامپوننت، می‌توانید با استفاده از تابع set وضعیت آن را به‌روز کنید و رندرهای بعدی را فراخوانی کنید. به‌روز کردن وضعیت کامپوننت شما، به‌صورت خودکار یک رندر را در صف قرار می‌دهد. (می‌توانید این را مانند میهمانی در رستوران تصور کنید که مشتریان پس از سفارش اولیه‌ی خود، چای، دسر و هرچه که بستگی به وضعیت تشنگی و گرسنگی آن‌ها دارد، سفارش می‌دهند.)” 12. امیدوارم این پاسخ به شما کمک کرده باشد.

<IllustrationBlock sequential>
  <Illustration caption="State update..." alt="React as a server in a restaurant, serving a Card UI to the user, represented as a patron with a cursor for their head. They patron expresses they want a pink card, not a black one!" src="/images/docs/illustrations/i_rerender1.png" />
  <Illustration caption="...triggers..." alt="React returns to the Component Kitchen and tells the Card Chef they need a pink Card." src="/images/docs/illustrations/i_rerender2.png" />
  <Illustration caption="...render!" alt="The Card Chef gives React the pink Card." src="/images/docs/illustrations/i_rerender3.png" />
</IllustrationBlock>

## Step 2: React renders your components {/*step-2-react-renders-your-components*/}

After you trigger a render, React calls your components to figure out what to display on screen. **"Rendering" is React calling your components.**

* **On initial render,** React will call the root component.
* **For subsequent renders,** React will call the function component whose state update triggered the render.

This process is recursive: if the updated component returns some other component, React will render _that_ component next, and if that component also returns something, it will render _that_ component next, and so on. The process will continue until there are no more nested components and React knows exactly what should be displayed on screen.

In the following example, React will call `Gallery()` and  `Image()` several times:

//////
پس از اجرای یک رندر، React برای نمایش روی صفحه، اجزای شما را فراخوانی می‌کند. “رندر” به معنای فراخوانی اجزای شما در React است.

در رندر اولیه، React اجزای ریشه را فراخوانی می‌کند. برای رندرهای بعدی، React اجزای تابع را که به‌روزرسانی وضعیت آن‌ها رندر را فراخوانی کرده، فراخوانی می‌کند. این فرایند بازگشتی است: اگر اجزای به‌روزرسانی شده، یک اجزای دیگر را برگردانند، React اجزای آن را نیز رندر می‌کند و اگر آن اجزا هم چیزی را برگردانند، اجزای بعدی را نیز رندر می‌کند و به همین ترتیب ادامه می‌دهد. این فرایند تا زمانی ادامه پیدا می‌کند که دیگر اجزای تو در تویی و React دقیقاً می‌داند چه چیزی باید روی صفحه نمایش داده شود.

<Sandpack>

```js Gallery.js active
export default function Gallery() {
  return (
    <section>
      <h1>Inspiring Sculptures</h1>
      <Image />
      <Image />
      <Image />
    </section>
  );
}

function Image() {
  return (
    <img
      src="https://i.imgur.com/ZF6s192.jpg"
      alt="'Floralis Genérica' by Eduardo Catalano: a gigantic metallic flower sculpture with reflective petals"
    />
  );
}
```

```js index.js
import Gallery from './Gallery.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Gallery />);
```

```css
img { margin: 0 10px 10px 0; }
```

</Sandpack>

* **During the initial render,** React will [create the DOM nodes](https://developer.mozilla.org/docs/Web/API/Document/createElement) for `<section>`, `<h1>`, and three `<img>` tags. 
* **During a re-render,** React will calculate which of their properties, if any, have changed since the previous render. It won't do anything with that information until the next step, the commit phase.
/////

این بخش از آموزش React را به فارسی ترجمه می‌کنم:

در رندر اولیه، React برای `<section>`، `<h1>` و سه برچسب `<img>`، گره‌های DOM را ایجاد می‌کند. در رندر مجدد، React محاسبه می‌کند که کدام یک از خصوصیات آن‌ها، در صورت وجود، از رندر قبلی تغییر کرده‌اند. این اطلاعات را تا مرحله بعد، یعنی مرحله commit، نگه نمی‌دارد.
<Pitfall>

Rendering must always be a [pure calculation](/learn/keeping-components-pure):
همیشه رندرینگ باید یک محاسبه خالص باشد:

* **Same inputs, same output.** Given the same inputs, a component should always return the same JSX. (When someone orders a salad with tomatoes, they should not receive a salad with onions!)
* **It minds its own business.** It should not change any objects or variables that existed before rendering. (One order should not change anyone else's order.)

Otherwise, you can encounter confusing bugs and unpredictable behavior as your codebase grows in complexity. When developing in "Strict Mode", React calls each component's function twice, which can help surface mistakes caused by impure functions.

/////
این بخش از آموزش React را به فارسی ترجمه می‌کنم:

همان ورودی‌ها، همان خروجی. با دادن ورودی‌های یکسان، یک کامپوننت باید همیشه JSX یکسانی را برگرداند. (وقتی کسی سالادی با گوجه‌فرنگی سفارش می‌دهد، نباید سالادی با پیاز دریافت کند!)

کامپوننت باید به کار خود تمرکز کند. نباید هیچ شی یا متغیری که قبل از رندر وجود داشته باشد، تغییر کند. (یک سفارش نباید سفارش کسی دیگر را تغییر دهد.)

در غیر این صورت، ممکن است با باگ‌های گیج‌کننده و رفتارهای پیش‌بین‌ناپذیری در هنگام رشد پایگاه کد خود مواجه شوید. در حالت “Strict Mode”، React هر تابع کامپوننت را دو بار فراخوانی می‌کند که می‌تواند به شناسایی اشتباهات ناشی از توابع ناخالص کمک کند.

</Pitfall>

<DeepDive>

#### Optimizing performance(بهینه سازی عملکرد) {/*optimizing-performance*/}

The default behavior of rendering all components nested within the updated component is not optimal for performance if the updated component is very high in the tree. If you run into a performance issue, there are several opt-in ways to solve it described in the [Performance](https://reactjs.org/docs/optimizing-performance.html) section. **Don't optimize prematurely!**

////
این بخش از آموزش React را به فارسی ترجمه می‌کنم:

اگر کامپوننت به‌روزرسانی شده در درخت بسیار بالا باشد، رفتار پیش‌فرض رندرینگ تمام اجزای تو در توی آن، برای عملکرد بهینه مناسب نیست. اگر با مشکل عملکرد مواجه شدید، چندین روش opt-in برای حل آن در بخش عملکرد توضیح داده شده است. بهینه‌سازی زودهنگام نکنید!
</DeepDive>

## Step 3: React commits changes to the DOM(مرحله 3: React تغییرات را در DOM انجام می دهد) {/*step-3-react-commits-changes-to-the-dom*/}

After rendering (calling) your components, React will modify the DOM. 

* **For the initial render,** React will use the [`appendChild()`](https://developer.mozilla.org/docs/Web/API/Node/appendChild) DOM API to put all the DOM nodes it has created on screen. 
* **For re-renders,** React will apply the minimal necessary operations (calculated while rendering!) to make the DOM match the latest rendering output.

**React only changes the DOM nodes if there's a difference between renders.** For example, here is a component that re-renders with different props passed from its parent every second. Notice how you can add some text into the `<input>`, updating its `value`, but the text doesn't disappear when the component re-renders:

////
این بخش از آموزش React را به فارسی ترجمه می‌کنم:

مرحله 3: React تغییرات را در DOM انجام می‌دهد پس از رندرینگ (فراخوانی) اجزای شما، React DOM را تغییر می‌دهد.

برای رندر اولیه، React از API DOM appendChild() برای قرار دادن تمام گره‌های DOMی که ایجاد کرده است، روی صفحه استفاده می‌کند. برای رندرهای مجدد، React عملیات لازم حداقلی را (محاسبه شده در هنگام رندرینگ!) برای تطبیق DOM با آخرین خروجی رندرینگ اعمال می‌کند. React فقط در صورت وجود تفاوت بین رندرها، گره‌های DOM را تغییر می‌دهد. به عنوان مثال، اینجا یک کامپوننت وجود دارد که با استفاده از پارامترهای مختلفی که از والد آن به آن منتقل می‌شوند، هر ثانیه مجدداً رندر می‌شود. توجه کنید که می‌توانید متنی را در `<input>` اضافه کنید و مقدار آن را به‌روز کنید، اما متن پس از رندر مجدد کامپوننت، ناپدید نمی‌شود:

<Sandpack>

```js Clock.js active
export default function Clock({ time }) {
  return (
    <>
      <h1>{time}</h1>
      <input />
    </>
  );
}
```

```js App.js hidden
import { useState, useEffect } from 'react';
import Clock from './Clock.js';

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}

export default function App() {
  const time = useTime();
  return (
    <Clock time={time.toLocaleTimeString('fa')} />
  );
}
```

</Sandpack>

This works because during this last step, React only updates the content of `<h1>` with the new `time`. It sees that the `<input>` appears in the JSX in the same place as last time, so React doesn't touch the `<input>`—or its `value`!
/////

این کار به خاطر این است که در این مرحله آخر، React فقط محتوای `<h1>` را با زمان جدید به‌روز می‌کند. React می‌بیند که `<input>` در JSX در همان محل قبلی ظاهر شده است، بنابراین React `<input>` یا مقدار آن را لمس نمی‌کند!

## Epilogue: Browser paint {/*epilogue-browser-paint*/}


After rendering is done and React updated the DOM, the browser will repaint the screen. Although this process is known as "browser rendering", we'll refer to it as "painting" to avoid confusion throughout the docs.

/////
پس از اینکه رندر انجام شد و React DOM را به روز کرد، مرورگر صفحه را دوباره رنگ می کند. اگرچه این فرآیند به عنوان "پردازش مرورگر" شناخته می شود، اما برای جلوگیری از سردرگمی در اسناد، از آن به عنوان "نقاشی" یاد می کنیم.

/////
پایان: نقاشی مرورگر پس از انجام رندرینگ و به‌روزرسانی DOM توسط React، مرورگر صفحه را مجدداً نقاشی می‌کند. این فرآیند را به عنوان “رندرینگ مرورگر” شناخته می‌شود، اما به منظور جلوگیری از ابهام در سند، به آن “نقاشی” می‌گوییم.

<Illustration alt="A browser painting 'still life with card element'." src="/images/docs/illustrations/i_browser-paint.png" />

<Recap>

* Any screen update in a React app happens in three steps:
  1. Trigger
  2. Render
  3. Commit
* You can use Strict Mode to find mistakes in your components
* React does not touch the DOM if the rendering result is the same as last time

//////


* هر به‌روزرسانی صفحه در یک برنامه React، در سه مرحله انجام می‌شود:
  1. Trigger
  2. Render
  3. Commit
* شما می‌توانید از حالت Strict برای پیدا کردن اشتباهات در کامپوننت‌های خود استفاده کنید.
* اگر نتیجه رندرینگ همانند نتیجه رندرینگ قبلی باشد، React به DOM دست نمی‌زند.


</Recap>

////////////////////////////////////////

این کد، سه مرحله‌ی به‌روزرسانی صفحه در یک برنامه React را نمایش می‌دهد:

```javascript
import React, { useState } from 'react';

function App() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={handleClick}>Increment</button>
    </div>
  );
}

export default App;
```

این کد یک کامپوننت React ساده است که یک شمارنده را نگه می‌دارد. هر بار که کاربر روی دکمه "Increment" کلیک می‌کند، مقدار شمارنده افزایش می‌یابد. این کد سه مرحله‌ی به‌روزرسانی صفحه را نشان می‌دهد:

1. Trigger: کلیک کردن کاربر روی دکمه "Increment"، مقدار شمارنده را تغییر می‌دهد.
2. Render: React، کامپوننت را با مقدار جدید شمارنده رندر می‌کند.
3. Commit: React، تغییرات را در DOM اعمال می‌کند و مقدار شمارنده را به‌روزرسانی می‌کند.

این کد را می‌توانید در [CodeSandbox](https://codesandbox.io/s/react-counter-example-9z7jv) ببینید..
