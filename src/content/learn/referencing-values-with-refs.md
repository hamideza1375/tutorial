---
title: 'Referencing Values with Refs (ارجاع مقادیر با Refs)'
---

<Intro>

When you want a component to "remember" some information, but you don't want that information to [trigger new renders](/learn/render-and-commit), you can use a *ref*.
///////////////////////////
وقتی می‌خواهید یک کامپوننت به یاد داشته باشد که بعضی اطلاعات را، اما نمی‌خواهید آن اطلاعات باعث [رندرهای جدید](/learn/render-and-commit) شود، می‌توانید از یک *ref* استفاده کنید.

</Intro>

<YouWillLearn>

- How to add a ref to your component
- How to update a ref's value
- How refs are different from state
- How to use refs safely

/////////////////
- چگونه یک ref به کامپوننت خود اضافه کنید
- نحوه به روز رسانی مقدار ref
- چگونه ref ها با state متفاوت هستند
- نحوه استفاده ایمن از refs

</YouWillLearn>


## اضافه کردن یک ref به کامپوننت {/*adding-a-ref-to-your-component*/}

You can add a ref to your component by importing the `useRef` Hook from React:
شما می‌توانید با وارد کردن `useRef` از React یک ref به کامپوننت خود اضافه کنید:

```js
import { useRef } from 'react';
```
Inside your component, call the `useRef` Hook and pass the initial value that you want to reference as the only argument. For example, here is a ref to the value `0`:
//////////////////////
در داخل کامپوننت خود، `useRef` را فراخوانی کرده و مقدار اولیه‌ای که می‌خواهید به آن ارجاع دهید را به عنوان تنها آرگومان ارسال کنید. به عنوان مثال، اینجا یک ref به مقدار `0` وجود دارد:

```js
const ref = useRef(0);
```
`useRef` returns an object like this:
`useRef` یک شیء مانند زیر را برمی‌گرداند:

```js
{ 
  current: 0 // مقداری که به useRef ارسال کرده‌اید
}
```

<Illustration src="/images/docs/illustrations/i_ref.png" alt="An arrow with 'current' written on it stuffed into a pocket with 'ref' written on it." />


You can access the current value of that ref through the `ref.current` property. This value is intentionally mutable, meaning you can both read and write to it. It's like a secret pocket of your component that React doesn't track. (This is what makes it an "escape hatch" from React's one-way data flow--more on that below!)
///////////////////////////
می‌توانید به مقدار فعلی آن ref از طریق خاصیت `ref.current` دسترسی پیدا کنید. این مقدار قابل تغییر است، به این معنی که می‌توانید به آن هم خواندن و هم نوشتن کنید. این مانند یک جیب مخفی کامپوننت شماست که React آن را پیگیری نمی‌کند. (این چیزی است که آن را از جریان داده یک‌طرفه React جدا می‌کند - بیشتر در ادامه!)

Here, a button will increment `ref.current` on every click:
در اینجا، یک دکمه هر بار با کلیک بر روی آن، `ref.current` را افزایش می‌دهد:

```js
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```
The ref points to a number, but, like [state](/learn/state-a-components-memory), you could point to anything: a string, an object, or even a function. Unlike state, ref is a plain JavaScript object with the `current` property that you can read and modify.
/////////////////////
این ref به یک عدد اشاره می‌کند، اما مانند [state](/learn/state-a-components-memory)، می‌توانید به هر چیزی اشاره کنید: یک رشته، یک شیء، یا حتی یک تابع.  برخلاف state، ref یک شیء جاوا اسکریپت ساده با ویژگی «current» است که می‌توانید آن را بخوانید و تغییر دهید.


Note that **the component doesn't re-render with every increment.** Like state, refs are retained by React between re-renders. However, setting state re-renders a component. Changing a ref does not!
//////////////////////////
توجه داشته باشید که **کامپوننت با هر افزایش مقدار ref رندر مجدد نمی‌شود.** مانند state، refs توسط React بین رندرهای مجدد نگه داشته می‌شوند. با این حال، تنظیم state باعث رندر مجدد کامپوننت می‌شود. تغییر ref این اتفاق را نمی‌افزاید.


## مثال: ساخت یک ساعت‌شمار {/*example-building-a-stopwatch*/}

You can combine refs and state in a single component. For example, let's make a stopwatch that the user can start or stop by pressing a button. In order to display how much time has passed since the user pressed "Start", you will need to keep track of when the Start button was pressed and what the current time is. **This information is used for rendering, so you'll keep it in state:**
/////////////////////////////////
شما می‌توانید refs و state را در یک کامپوننت ترکیب کنید. به عنوان مثال، بیایید یک ساعت‌شمار بسازیم که کاربر می‌تواند با فشردن یک دکمه آن را شروع یا متوقف کند. برای نمایش چه مقدار زمانی از زمانی که کاربر دکمه "شروع" را فشرد، گذشته است، شما باید زمانی که دکمه "شروع" فشرده شده و زمان فعلی را پیگیری کنید. **این اطلاعات برای رندرینگ استفاده می‌شوند، بنابراین آنها را در state نگه می‌دارید:**

```js
const [startTime, setStartTime] = useState(null);
const [now, setNow] = useState(null);
```
When the user presses "Start", you'll use [`setInterval`](https://developer.mozilla.org/docs/Web/API/setInterval) in order to update the time every 10 milliseconds:
/////////////////////////
وقتی کاربر دکمه "شروع" را فشرد، شما از [`setInterval`](https://developer.mozilla.org/docs/Web/API/setInterval) برای به‌روزرسانی زمان هر ۱۰ میلی‌ثانیه استفاده خواهید کرد:


<Sandpack>

```js
import { useState } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);

  function handleStart() {
    // Start counting.
    setStartTime(Date.now());
    setNow(Date.now());

    setInterval(() => {
      // Update the current time every 10ms.
      setNow(Date.now());
    }, 10);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>
        Start
      </button>
    </>
  );
}
```

</Sandpack>

When the "Stop" button is pressed, you need to cancel the existing interval so that it stops updating the `now` state variable. You can do this by calling [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval), but you need to give it the interval ID that was previously returned by the `setInterval` call when the user pressed Start. You need to keep the interval ID somewhere. **Since the interval ID is not used for rendering, you can keep it in a ref:**
//////////////////////////////
وقتی دکمه "متوقف" فشرده شود، شما باید با استفاده از [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval)، interval موجود را لغو کنید تا دیگر `now` را به‌روز نکند. شما می‌توانید این کار را با دادن شناسه interval که پیش‌تر توسط `setInterval` برگشت داده شده بود وقتی کاربر دکمه شروع را فشرد، انجام دهید. شما باید شناسه interval را جایی نگه دارید. **از آنجایی که شناسه interval برای رندرینگ استفاده نمی‌شود، می‌توانید آن را در یک ref نگه دارید:**

<Sandpack>


```js
import { useState, useRef } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);
  const intervalRef = useRef(null);

  function handleStart() {
    setStartTime(Date.now());
    setNow(Date.now());

    clearInterval(intervalRef.current);
    intervalRef.current = setInterval(() => {
      setNow(Date.now());
    }, 10);
  }

  function handleStop() {
    clearInterval(intervalRef.current);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>
        Start
      </button>
      <button onClick={handleStop}>
        Stop
      </button>
    </>
  );
}
```

</Sandpack>

When a piece of information is used for rendering, keep it in state. When a piece of information is only needed by event handlers and changing it doesn't require a re-render, using a ref may be more efficient.
//////////////////////////////
وقتی یک قطعه اطلاعات برای رندرینگ استفاده می‌شود، آن را در state نگه دارید. وقتی یک قطعه اطلاعات فقط توسط event handlers نیاز است و تغییر آن به رندر مجدد نیاز ندارد، استفاده از ref ممکن است کارآمدتر باشد.

## Differences between refs and state(تفاوت بین refs و state) {/*differences-between-refs-and-state*/}

Perhaps you're thinking refs seem less "strict" than state—you can mutate them instead of always having to use a state setting function, for instance. But in most cases, you'll want to use state. Refs are an "escape hatch" you won't need often. Here's how state and refs compare:

| refs                                                                                  | state                                                                                                                     |
| ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `useRef(initialValue)` returns `{ current: initialValue }`                            | `useState(initialValue)` returns the current value of a state variable and a state setter function ( `[value, setValue]`) |
| Doesn't trigger re-render when you change it.                                         | Triggers re-render when you change it.                                                                                    |
| Mutable—you can modify and update `current`'s value outside of the rendering process. | "Immutable"—you must use the state setting function to modify state variables to queue a re-render.                       |
| You shouldn't read (or write) the `current` value during rendering. | You can read state at any time. However, each render has its own [snapshot](/learn/state-as-a-snapshot) of state which does not change.

Here is a counter button that's implemented with state:
////////////////////////////

شاید فکر می‌کنید که refs کمتر "سختگیرانه" از state به نظر می‌رسند - به عنوان مثال، شما می‌توانید آنها را جایگزین کنید و به جای همیشه استفاده از یک تابع تنظیم کننده state، آنها را تغییر دهید. اما در بیشتر موارد، شما باید از state استفاده کنید. Refs یک “escape hatch”"فرار" هستند که به آنها بسیار نیاز نخواهید داشت. در ادامه، نحوه مقایسه state و refs را مشاهده می‌کنید:

| refs                                                                                  | state                                                                                                                     |
| ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `useRef(initialValue)` بازگشت `{ current: initialValue }`                            | `useState(initialValue)` مقدار فعلی یک متغیر state و یک تابع تنظیم کننده state ( `[value, setValue]`) را برمی‌گرداند |
| هنگام تغییر آن، رندر مجدد را فراخوانی نمی‌کند.                                         | هنگام تغییر آن، رندر مجدد را فراخوانی می‌کند.                                                                                    |
| قابل تغییر - شما می‌توانید مقدار `current` را خارج از فرآیند رندرینگ تغییر دهید و به روز کنید. | "غیرقابل تغییر" - شما باید از تابع تنظیم کننده state برای تغییر متغیرهای state برای صف کردن رندر استفاده کنید.                       |
| شما نباید مقدار `current` را در طول رندرینگ بخوانید (یا بنویسید). | شما می‌توانید هر زمانی state را بخوانید. با این حال، هر رندرینگ دارای [snapshot](/learn/state-as-a-snapshot) خود است که تغییر نمی‌کند.

اینجا یک دکمه شمارنده است که با state پیاده‌سازی شده است:

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      You clicked {count} times
    </button>
  );
}
```

</Sandpack>

Because the `count` value is displayed, it makes sense to use a state value for it. When the counter's value is set with `setCount()`, React re-renders the component and the screen updates to reflect the new count.

If you tried to implement this with a ref, React would never re-render the component, so you'd never see the count change! See how clicking this button **does not update its text**:
///////////////
زمانی که مقدار `count` نمایش داده می‌شود، استفاده از یک مقدار state برای آن منطقی است. وقتی مقدار شمارنده با `setCount()` تنظیم می‌شود، React کامپوننت را مجدداً رندر می‌کند و صفحه برای نمایش مقدار جدید به‌روزرسانی می‌شود.

اگر با استفاده از یک ref این کار را پیاده‌سازی کنید، React هرگز کامپوننت را مجدداً رندر نمی‌کند، بنابراین شما هرگز تغییر شمارنده را نخواهید دید! ببینید که با کلیک بر روی این دکمه، **متن آن به‌روز نمی‌شود**:

<Sandpack>

```js
import { useRef } from 'react';

export default function Counter() {
  let countRef = useRef(0);

  function handleClick() {
    // This doesn't re-render the component!
    countRef.current = countRef.current + 1;
  }

  return (
    <button onClick={handleClick}>
      You clicked {countRef.current} times
    </button>
  );
}
```

</Sandpack>

This is why reading `ref.current` during render leads to unreliable code. If you need that, use state instead.
//////////////
این دلیل است که خواندن `ref.current` در طول رندرینگ منجر به کد نامطمئن می‌شود. اگر به آن نیاز دارید، به جای آن از state استفاده کنید.

<DeepDive>

#### How does useRef work inside? {/*how-does-use-ref-work-inside*/}

Although both `useState` and `useRef` are provided by React, in principle `useRef` could be implemented _on top of_ `useState`. You can imagine that inside of React, `useRef` is implemented like this:
///////////////
`useState` و `useRef` هر دو توسط React ارائه شده‌اند، اما در اصل می‌توان `useRef` را _بالای_ `useState` پیاده‌سازی کرد. می‌توانید تصور کنید که در داخل React، `useRef` به این شکل پیاده‌سازی شده است:

```js
// Inside of React
function useRef(initialValue) {
  const [ref, unused] = useState({ current: initialValue });
  return ref;
}
```

During the first render, `useRef` returns `{ current: initialValue }`. This object is stored by React, so during the next render the same object will be returned. Note how the state setter is unused in this example. It is unnecessary because `useRef` always needs to return the same object!

React provides a built-in version of `useRef` because it is common enough in practice. But you can think of it as a regular state variable without a setter. If you're familiar with object-oriented programming, refs might remind you of instance fields--but instead of `this.something` you write `somethingRef.current`.
/////////////////
در طول رندرینگ اول، `useRef` `{ current: initialValue }` را برمی‌گرداند. این شیء توسط React ذخیره می‌شود، بنابراین در رندرینگ بعدی، همان شیء برگشت داده خواهد شد. توجه کنید که تابع تنظیم کننده state در این مثال استفاده نشده است. این غیرضروری است زیرا `useRef` همیشه باید همان شیء را برگرداند!

React یک نسخه داخلی از `useRef` ارائه می‌دهد زیرا در عمل به اندازه کافی رایج است. اما می‌توانید به عنوان یک متغیر state معمولی بدون یک تابع تنظیم کننده به آن فکر کنید. اگر با برنامه‌نویسی شیءگرا آشنایی دارید، refs ممکن است به شما یادآور فیلدهای نمونه باشد - اما به جای `this.something` شما `somethingRef.current` را می‌نویسید.

</DeepDive>

## چه زمانی از refs استفاده کنیم {/*when-to-use-refs*/}

Typically, you will use a ref when your component needs to "step outside" React and communicate with external APIs—often a browser API that won't impact the appearance of the component. Here are a few of these rare situations:

- Storing [timeout IDs](https://developer.mozilla.org/docs/Web/API/setTimeout)
- Storing and manipulating [DOM elements](https://developer.mozilla.org/docs/Web/API/Element), which we cover on [the next page](/learn/manipulating-the-dom-with-refs)
- Storing other objects that aren't necessary to calculate the JSX.

If your component needs to store some value, but it doesn't impact the rendering logic, choose refs.
///////////////////////
به طور معمول، وقتی کامپوننت شما نیاز دارد که از React "خارج شود" و با API های خارجی ارتباط برقرار کند - اغلب API مرورگری که بر روی ظاهر کامپوننت تأثیری ندارد - از refs استفاده می‌کنید. در ادامه چند مورد از این موارد نادر را مشاهده می‌کنید:

- ذخیره [timeout IDs](https://developer.mozilla.org/docs/Web/API/setTimeout)
- ذخیره و تغییر [DOM elements](https://developer.mozilla.org/docs/Web/API/Element)، که در [صفحه بعدی](/learn/manipulating-the-dom-with-refs) به آنها می‌پردازیم
- ذخیره سایر شیءهایی که برای محاسبه JSX لازم نیستند.

اگر کامپوننت شما نیاز به ذخیره برخی از مقادیر دارد، اما منطق رندرینگ را تحت تأثیر قرار نمی‌دهد، از refs استفاده کنید.

## بهترین روش‌های استفاده از refs {/*best-practices-for-refs*/}

Following these principles will make your components more predictable:

- **Treat refs as an escape hatch.** Refs are useful when you work with external systems or browser APIs. If much of your application logic and data flow relies on refs, you might want to rethink your approach.
- **Don't read or write `ref.current` during rendering.** If some information is needed during rendering, use [state](/learn/state-a-components-memory) instead. Since React doesn't know when `ref.current` changes, even reading it while rendering makes your component's behavior difficult to predict. (The only exception to this is code like `if (!ref.current) ref.current = new Thing()` which only sets the ref once during the first render.)

Limitations of React state don't apply to refs. For example, state acts like a [snapshot for every render](/learn/state-as-a-snapshot) and [doesn't update synchronously.](/learn/queueing-a-series-of-state-updates) But when you mutate the current value of a ref, it changes immediately:
//////////////////////////////

دنبال کردن این اصول باعث می‌شود کامپوننت‌های شما قابل پیش‌بینی‌تر شوند:

- **Refs را به عنوان یک فرار بپذیرید.** Refs هنگام کار با سیستم‌های خارجی یا API های مرورگر مفید هستند. اگر بخش قابل توجهی از منطق برنامه و جریان داده شما بر روی refs تکیه دارد، ممکن است بخواهید رویکرد خود را بازبینی کنید.
- **در طول رندرینگ، `ref.current` را نخوانید یا نوشتید.** اگر برای رندرینگ اطلاعاتی لازم است، به جای آن از [state](/learn/state-a-components-memory) استفاده کنید. از آنجایی که React نمی‌داند کی `ref.current` تغییر می‌کند، حتی خواندن آن در طول رندرینگ باعث می‌شود که رفتار کامپوننت شما سخت‌پیش‌بینی شود. (تنها استثنا این است که کدی مانند `if (!ref.current) ref.current = new Thing()` که فقط یک بار در طول رندرینگ اول ref را تنظیم می‌کند.)

محدودیت‌های state React برای refs اعمال نمی‌شود. به عنوان مثال، state مانند [snapshot برای هر رندر](/learn/state-as-a-snapshot) عمل می‌کند و [همزمان به‌روز نمی‌شود.](/learn/queueing-a-series-of-state-updates) اما وقتی مقدار فعلی یک ref را تغییر می‌دهید، فوراً تغییر می‌کند:


```js
ref.current = 5;
console.log(ref.current); // 5
```

This is because **the ref itself is a regular JavaScript object,** and so it behaves like one.

You also don't need to worry about [avoiding mutation](/learn/updating-objects-in-state) when you work with a ref. As long as the object you're mutating isn't used for rendering, React doesn't care what you do with the ref or its contents.
////////////////////////
این دلیل است که **خود ref یک شیء معمولی JavaScript است** و بنابراین مانند یک شیء عادی عمل می‌کند.

همچنین نیازی به [جلوگیری از تغییر](/learn/updating-objects-in-state) نیست وقتی با یک ref کار می‌کنید. تا زمانی که شیءی که در حال تغییر آن هستید برای رندرینگ استفاده نمی‌شود، React اهمیتی به آنچه که با ref یا محتوای آن انجام می‌دهید نمی‌دهد.

## Refs و DOM {/*refs-and-the-dom*/}

You can point a ref to any value. However, the most common use case for a ref is to access a DOM element. For example, this is handy if you want to focus an input programmatically. When you pass a ref to a `ref` attribute in JSX, like `<div ref={myRef}>`, React will put the corresponding DOM element into `myRef.current`. Once the element is removed from the DOM, React will update `myRef.current` to be `null`. You can read more about this in [Manipulating the DOM with Refs.](/learn/manipulating-the-dom-with-refs)
/////////////////////
شما می‌توانید به هر مقداری اشاره کنید. با این حال، مورد استفاده رایج‌ترین برای یک ref دسترسی به یک المان DOM است. به عنوان مثال، این کاربردی است اگر می‌خواهید به صورت برنامه‌ای به یک ورودی تمرکز کنید. وقتی یک ref را به یک ویژگی `ref` در JSX مانند `<div ref={myRef}>` منتقل می‌کنید، React المان DOM مربوطه را در `myRef.current` قرار می‌دهد. هنگامی که المان از DOM حذف می‌شود، React `myRef.current` را به `null` به‌روزرسانی می‌کند. شما می‌توانید بیشتر درباره این موضوع در [Manipulating the DOM with Refs](/learn/manipulating-the-dom-with-refs) بخوانید..

<Recap>

- Refs are an escape hatch to hold onto values that aren't used for rendering. You won't need them often.
- A ref is a plain JavaScript object with a single property called `current`, which you can read or set.
- You can ask React to give you a ref by calling the `useRef` Hook.
- Like state, refs let you retain information between re-renders of a component.
- Unlike state, setting the ref's `current` value does not trigger a re-render.
- Don't read or write `ref.current` during rendering. This makes your component hard to predict.
//////////////////
- Refs یک فرار هستند برای نگه داشتن مقادیری که برای رندرینگ استفاده نمی‌شوند. شما به آنها اغلب نیاز نخواهید داشت.
- یک ref یک شیء معمولی JavaScript با یک خاصیت به نام `current` است که می‌توانید آن را بخوانید یا تنظیم کنید.
- شما می‌توانید از React بخواهید که یک ref را با فراخوانی Hook `useRef` به شما بدهد.
- مانند state، refs به شما اجازه می‌دهند اطلاعات را بین رندرینگ‌های یک کامپوننت نگه دارید.
- برخلاف state، تنظیم مقدار `current` ref رندر مجدد را فراخوانی نمی‌کند.
- در طول رندرینگ، `ref.current` را نخوانید یا نوشتید. این باعث می‌شود که رفتار کامپوننت شما سخت‌پیش‌بینی شود.

</Recap>



<Challenges>

#### Fix a broken chat input {/*fix-a-broken-chat-input*/}

Type a message and click "Send". You will notice there is a three second delay before you see the "Sent!" alert. During this delay, you can see an "Undo" button. Click it. This "Undo" button is supposed to stop the "Sent!" message from appearing. It does this by calling [`clearTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/clearTimeout) for the timeout ID saved during `handleSend`. However, even after "Undo" is clicked, the "Sent!" message still appears. Find why it doesn't work, and fix it.

<Hint>

Regular variables like `let timeoutID` don't "survive" between re-renders because every render runs your component (and initializes its variables) from scratch. Should you keep the timeout ID somewhere else?

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  let timeoutID = null;

  function handleSend() {
    setIsSending(true);
    timeoutID = setTimeout(() => {
      alert('Sent!');
      setIsSending(false);
    }, 3000);
  }

  function handleUndo() {
    setIsSending(false);
    clearTimeout(timeoutID);
  }

  return (
    <>
      <input
        disabled={isSending}
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        disabled={isSending}
        onClick={handleSend}>
        {isSending ? 'Sending...' : 'Send'}
      </button>
      {isSending &&
        <button onClick={handleUndo}>
          Undo
        </button>
      }
    </>
  );
}
```

</Sandpack>

<Solution>

Whenever your component re-renders (such as when you set state), all local variables get initialized from scratch. This is why you can't save the timeout ID in a local variable like `timeoutID` and then expect another event handler to "see" it in the future. Instead, store it in a ref, which React will preserve between renders.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  const timeoutRef = useRef(null);

  function handleSend() {
    setIsSending(true);
    timeoutRef.current = setTimeout(() => {
      alert('Sent!');
      setIsSending(false);
    }, 3000);
  }

  function handleUndo() {
    setIsSending(false);
    clearTimeout(timeoutRef.current);
  }

  return (
    <>
      <input
        disabled={isSending}
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        disabled={isSending}
        onClick={handleSend}>
        {isSending ? 'Sending...' : 'Send'}
      </button>
      {isSending &&
        <button onClick={handleUndo}>
          Undo
        </button>
      }
    </>
  );
}
```

</Sandpack>

</Solution>


#### Fix a component failing to re-render {/*fix-a-component-failing-to-re-render*/}

This button is supposed to toggle between showing "On" and "Off". However, it always shows "Off". What is wrong with this code? Fix it.

<Sandpack>

```js
import { useRef } from 'react';

export default function Toggle() {
  const isOnRef = useRef(false);

  return (
    <button onClick={() => {
      isOnRef.current = !isOnRef.current;
    }}>
      {isOnRef.current ? 'On' : 'Off'}
    </button>
  );
}
```

</Sandpack>

<Solution>

In this example, the current value of a ref is used to calculate the rendering output: `{isOnRef.current ? 'On' : 'Off'}`. This is a sign that this information should not be in a ref, and should have instead been put in state. To fix it, remove the ref and use state instead:

<Sandpack>

```js
import { useState } from 'react';

export default function Toggle() {
  const [isOn, setIsOn] = useState(false);

  return (
    <button onClick={() => {
      setIsOn(!isOn);
    }}>
      {isOn ? 'On' : 'Off'}
    </button>
  );
}
```

</Sandpack>

</Solution>

#### Fix debouncing {/*fix-debouncing*/}

In this example, all button click handlers are ["debounced".](https://redd.one/blog/debounce-vs-throttle) To see what this means, press one of the buttons. Notice how the message appears a second later. If you press the button while waiting for the message, the timer will reset. So if you keep clicking the same button fast many times, the message won't appear until a second *after* you stop clicking. Debouncing lets you delay some action until the user "stops doing things".

This example works, but not quite as intended. The buttons are not independent. To see the problem, click one of the buttons, and then immediately click another button. You'd expect that after a delay, you would see both button's messages. But only the last button's message shows up. The first button's message gets lost.

Why are the buttons interfering with each other? Find and fix the issue.

<Hint>

The last timeout ID variable is shared between all `DebouncedButton` components. This is why clicking one button resets another button's timeout. Can you store a separate timeout ID for each button?

</Hint>

<Sandpack>

```js
let timeoutID;

function DebouncedButton({ onClick, children }) {
  return (
    <button onClick={() => {
      clearTimeout(timeoutID);
      timeoutID = setTimeout(() => {
        onClick();
      }, 1000);
    }}>
      {children}
    </button>
  );
}

export default function Dashboard() {
  return (
    <>
      <DebouncedButton
        onClick={() => alert('Spaceship launched!')}
      >
        Launch the spaceship
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Soup boiled!')}
      >
        Boil the soup
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Lullaby sung!')}
      >
        Sing a lullaby
      </DebouncedButton>
    </>
  )
}
```

```css
button { display: block; margin: 10px; }
```

</Sandpack>

<Solution>

A variable like `timeoutID` is shared between all components. This is why clicking on the second button resets the first button's pending timeout. To fix this, you can keep timeout in a ref. Each button will get its own ref, so they won't conflict with each other. Notice how clicking two buttons fast will show both messages.

<Sandpack>

```js
import { useRef } from 'react';

function DebouncedButton({ onClick, children }) {
  const timeoutRef = useRef(null);
  return (
    <button onClick={() => {
      clearTimeout(timeoutRef.current);
      timeoutRef.current = setTimeout(() => {
        onClick();
      }, 1000);
    }}>
      {children}
    </button>
  );
}

export default function Dashboard() {
  return (
    <>
      <DebouncedButton
        onClick={() => alert('Spaceship launched!')}
      >
        Launch the spaceship
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Soup boiled!')}
      >
        Boil the soup
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Lullaby sung!')}
      >
        Sing a lullaby
      </DebouncedButton>
    </>
  )
}
```

```css
button { display: block; margin: 10px; }
```

</Sandpack>

</Solution>

#### Read the latest state {/*read-the-latest-state*/}

In this example, after you press "Send", there is a small delay before the message is shown. Type "hello", press Send, and then quickly edit the input again. Despite your edits, the alert would still show "hello" (which was the value of state [at the time](/learn/state-as-a-snapshot#state-over-time) the button was clicked).

Usually, this behavior is what you want in an app. However, there may be occasional cases where you want some asynchronous code to read the *latest* version of some state. Can you think of a way to make the alert show the *current* input text rather than what it was at the time of the click?

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');

  function handleSend() {
    setTimeout(() => {
      alert('Sending: ' + text);
    }, 3000);
  }

  return (
    <>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        onClick={handleSend}>
        Send
      </button>
    </>
  );
}
```

</Sandpack>

<Solution>

State works [like a snapshot](/learn/state-as-a-snapshot), so you can't read the latest state from an asynchronous operation like a timeout. However, you can keep the latest input text in a ref. A ref is mutable, so you can read the `current` property at any time. Since the current text is also used for rendering, in this example, you will need *both* a state variable (for rendering), *and* a ref (to read it in the timeout). You will need to update the current ref value manually.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const textRef = useRef(text);

  function handleChange(e) {
    setText(e.target.value);
    textRef.current = e.target.value;
  }

  function handleSend() {
    setTimeout(() => {
      alert('Sending: ' + textRef.current);
    }, 3000);
  }

  return (
    <>
      <input
        value={text}
        onChange={handleChange}
      />
      <button
        onClick={handleSend}>
        Send
      </button>
    </>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
