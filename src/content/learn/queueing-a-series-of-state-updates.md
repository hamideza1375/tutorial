---
title: Queueing a Series of State Updates(صف بندی یک سری از آپدیت state)(منتظر کردن یک سری به‌روزرسانی وضعیت)
---

<Intro>

Setting a state variable will queue another render. But sometimes you might want to perform multiple operations on the value before queueing the next render. To do this, it helps to understand how React batches state updates.

///////////////
با تنظیم یک متغیر state variable رندر دیگری در صف قرار می گیرد. اما گاهی اوقات ممکن است بخواهید قبل از اینکه رندر بعدی را در صف قرار دهید چندین عملیات روی مقدار انجام دهید. برای انجام این کار، درک نحوه وضعیت به‌روزرسانی‌های دسته‌ای React کمک می‌کند.

</Intro>

<YouWillLearn>

* What "batching" is and how React uses it to process multiple state updates
* How to apply several updates to the same state variable in a row

//////////////////

* «بچینگ» چیست و چگونه React از آن برای پردازش به‌روزرسانی‌های چند حالته استفاده می‌کند
* نحوه اعمال چندین به روز رسانی به یک متغیر state variable در یک ردیف

</YouWillLearn>

## React batches state updates(React به‌روزرسانی‌های وضعیت را دسته‌بندی می‌کند.)(React بروزرسانی های state را دسته بندی میکند) {/*react-batches-state-updates*/}

You might expect that clicking the "+3" button will increment the counter three times because it calls `setNumber(number + 1)` three times:
//////
ممکن است انتظار داشته باشید که با کلیک بر روی دکمه "+3" شمارنده سه بار افزایش یابد زیرا "setNumber(شماره + 1)" را سه بار صدا می کند:

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

However, as you might recall from the previous section, [each render's state values are fixed](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time), so the value of `number` inside the first render's event handler is always `0`, no matter how many times you call `setNumber(1)`:
/////
با این حال، همانطور که ممکن است به یاد داشته باشید از بخش قبل، مقادیر وضعیت هر رندر ثابت هستند، بنابراین مقدار عدد در داخل رویداد هندلر رندر اول همیشه 0 است، هر چند که چندین بار setNumber(1) را فراخوانی کنید.

```js
setNumber(0 + 1);
setNumber(0 + 1);
setNumber(0 + 1);
```

But there is one other factor at play here. **React waits until *all* code in the event handlers has run before processing your state updates.** This is why the re-render only happens *after* all these `setNumber()` calls.
//////
اما یک عامل دیگر در اینجا نقش دارد. React منتظر می‌ماند تا تمام کد در رویدادهای هندلر اجرا شود، پیش از پردازش به‌روزرسانی‌های state شما. به همین دلیل، رندر مجدد فقط پس از تمام شدن تماس‌های setNumber() انجام می‌شود.

This might remind you of a waiter taking an order at the restaurant. A waiter doesn't run to the kitchen at the mention of your first dish! Instead, they let you finish your order, let you make changes to it, and even take orders from other people at the table.
/////
این ممکن است شما را به یاد گارسونی بیاندازد که در رستوران سفارش می دهد. گارسون با ذکر اولین غذای شما به آشپزخانه نمی دود! در عوض، آنها به شما اجازه می دهند تا سفارش خود را تمام کنید، به شما اجازه می دهند تغییراتی در آن ایجاد کنید، و حتی از سایر افراد حاضر در میز سفارش بگیرید.

<Illustration src="/images/docs/illustrations/i_react-batching.png"  alt="An elegant cursor at a restaurant places and order multiple times with React, playing the part of the waiter. After she calls setState() multiple times, the waiter writes down the last one she requested as her final order." />

This lets you update multiple state variables--even from multiple components--without triggering too many [re-renders.](/learn/render-and-commit#re-renders-when-state-updates) But this also means that the UI won't be updated until _after_ your event handler, and any code in it, completes. This behavior, also known as **batching,** makes your React app run much faster. It also avoids dealing with confusing "half-finished" renders where only some of the variables have been updated.
/////
این به شما اجازه می‌دهد که چندین متغیر state را - حتی از چندین کامپوننت - به‌روز کنید، بدون ایجاد خیلی از رندرهای مجدد. اما این به این معنی است که رابط کاربری پس از هندلر رویداد شما و هر کدی که در آن وجود دارد، به‌روز نخواهد شد. این رفتار، همچنین با نام دسته‌بندی، برنامه React شما را بسیار سریع‌تر اجرا می‌کند. همچنین، این از برخورد با رندرهای “نیمه‌تمام” که فقط برخی از متغیرها به‌روز شده‌اند، جلوگیری می‌کند.

**React does not batch across *multiple* intentional events like clicks**--each click is handled separately. Rest assured that React only does batching when it's generally safe to do. This ensures that, for example, if the first button click disables a form, the second click would not submit it again.
/////
React به‌روزرسانی‌های state را دسته‌بندی نمی‌کند در رویدادهای چندگانه مثل کلیک - هر کلیک به‌صورت جداگانه پردازش می‌شود. با اطمینان کامل، React فقط در مواردی که به‌طور کلی ایمن است، دسته‌بندی می‌کند. این اطمینان می‌دهد که به‌عنوان مثال، اگر کلیک اول دکمه یک فرم را غیرفعال کند، کلیک دوم آن را دوباره ارسال نمی‌کند.

## Updating the same state multiple times before the next render(چند بار به روز رسانی یک state قبل از رندر بعدی) {/*updating-the-same-state-multiple-times-before-the-next-render*/}

It is an uncommon use case, but if you would like to update the same state variable multiple times before the next render, instead of passing the *next state value* like `setNumber(number + 1)`, you can pass a *function* that calculates the next state based on the previous one in the queue, like `setNumber(n => n + 1)`. It is a way to tell React to "do something with the state value" instead of just replacing it.
/////
این یک مورد کاربرد نادر است، اما اگر می‌خواهید قبل از رندر بعدی چندین بار وضعیت متغیر را به‌روز کنید، به جای انتقال مقدار وضعیت بعدی مانند setNumber(number + 1)، می‌توانید یک تابع را که بر اساس وضعیت قبلی در صف محاسبه می‌کند، به عنوان وضعیت بعدی انتقال دهید، مانند setNumber(n => n + 1). این یک راه برای اطلاع دادن به React برای “انجام کاری با مقدار وضعیت” به جای جایگزینی آن است.

Try incrementing the counter now:

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
      }}>+3</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Here, `n => n + 1` is called an **updater function.** When you pass it to a state setter:
/////
در اینجا n => n + 1 تابع به روز رسانی نامیده می شود. هنگامی که آن را به یک set کننده state ارسال می کنید:

1. React queues this function to be processed after all the other code in the event handler has run.
2. During the next render, React goes through the queue and gives you the final updated state.
/////
1. React این تابع را برای پردازش پس از اجرای کلیه کد در هندلر رویداد، در صف قرار می‌دهد.
2. در رندر بعدی، React از صف عبور می‌کند و وضعیت به‌روزرسانی شده نهایی را به شما می‌دهد.

```js
setNumber(n => n + 1);
setNumber(n => n + 1);
setNumber(n => n + 1);
```

Here's how React works through these lines of code while executing the event handler:
/////
در اینجا نحوه عملکرد React از طریق این خطوط کد هنگام اجرای کنترل کننده رویداد آمده است:

1. `setNumber(n => n + 1)`: `n => n + 1` is a function. React adds it to a queue.
1. `setNumber(n => n + 1)`: `n => n + 1` is a function. React adds it to a queue.
1. `setNumber(n => n + 1)`: `n => n + 1` is a function. React adds it to a queue.

/////
1. `setNumber(n => n + 1)`: `n => n + 1` یک تابع است. React آن را به یک صف اضافه می کند
1. `setNumber(n => n + 1)`: `n => n + 1` یک تابع است. React آن را به یک صف اضافه می کند
1. `setNumber(n => n + 1)`: `n => n + 1` یک تابع است. React آن را به یک صف اضافه می کند

When you call `useState` during the next render, React goes through the queue. The previous `number` state was `0`, so that's what React passes to the first updater function as the `n` argument. Then React takes the return value of your previous updater function and passes it to the next updater as `n`, and so on:
/////
وقتی که در رندر بعدی useState را فراخوانی می‌کنید، React از صف عبور می‌کند. وضعیت عدد قبلی 0 بود، بنابراین React آن را به عنوان آرگومان n به تابع به‌روزرسانی‌کننده اول منتقل می‌کند. سپس React مقدار بازگشتی تابع به‌روزرسانی‌کننده قبلی را به عنوان n به به‌روزرسانی‌کننده بعدی منتقل می‌کند و به همین ترتیب ادامه می‌دهد:

|  queued update | `n` | returns |
|--------------|---------|-----|
| `n => n + 1` | `0` | `0 + 1 = 1` |
| `n => n + 1` | `1` | `1 + 1 = 2` |
| `n => n + 1` | `2` | `2 + 1 = 3` |

React stores `3` as the final result and returns it from `useState`.

This is why clicking "+3" in the above example correctly increments the value by 3.
/////
React 3 را به عنوان نتیجه نهایی ذخیره می‌کند و آن را از useState بازمی‌گرداند.

این به دلیل این است که با کلیک بر روی “+3” در مثال بالا، مقدار به درستی با 3 افزایش پیدا می‌کند.

### What happens if you update state after replacing it(چه اتفاقی می‌افتد اگر پس از جایگزینی آن، State را به‌روز کنید؟) {/*what-happens-if-you-update-state-after-replacing-it*/}

What about this event handler? What do you think `number` will be in the next render?
/////
این هندلر رویداد چه اتفاقی خواهد افتاد؟ فکر می‌کنید number در رندر بعدی چه مقداری خواهد داشت؟

```js
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
}}>
```

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
      }}>Increase the number</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Here's what this event handler tells React to do:
//////
در اینجا چیزی است که این کنترل کننده رویداد به React می گوید که انجام دهد:

1. `setNumber(number + 5)`: `number` is `0`, so `setNumber(0 + 5)`. React adds *"replace with `5`"* to its queue.
2. `setNumber(n => n + 1)`: `n => n + 1` is an updater function. React adds *that function* to its queue.

///////////
1. setNumber(number + 5): عدد 0 است، بنابراین setNumber(0 + 5) را فراخوانی می‌کنیم. React “replace with 5” را به صف خود اضافه می‌کند.
2. setNumber(n => n + 1): n => n + 1 یک تابع به‌روزرسانی‌کننده است. React آن تابع را به صف خود اضافه می‌کند.

During the next render, React goes through the state queue:
/////
در رندر بعدی، React از صف state خارج میشود  :

|   queued update       | `n` | returns |
|--------------|---------|-----|
| "replace with `5`" | `0` (unused) | `5` |
| `n => n + 1` | `5` | `5 + 1 = 6` |

React stores `6` as the final result and returns it from `useState`. 
/////
React شش 6 را به عنوان نتیجه نهایی ذخیره می کند و آن را از useState برمی گرداند.

<Note>

You may have noticed that `setState(5)` actually works like `setState(n => 5)`, but `n` is unused!
/////
ممکن است متوجه شده باشید که setState(5) در واقع مانند setState (n => 5) کار می کند، اما n استفاده نشده است!

</Note>

### What happens if you replace state after updating it(اگر state را پس از به‌روزرسانی جایگزین کنید، چه اتفاقی می‌افتد) {/*what-happens-if-you-replace-state-after-updating-it*/}

Let's try one more example. What do you think `number` will be in the next render?
/////
بیایید یک مثال دیگر را امتحان کنیم. به نظر شما `number` در رندر بعدی چه خواهد بود؟

```js
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
  setNumber(42);
}}>
```

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
        setNumber(42);
      }}>Increase the number</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Here's how React works through these lines of code while executing this event handler:
//////
در اینجا نحوه عملکرد React از طریق این خطوط کد در هنگام اجرای این کنترلر رخ داده آمده است:

1. `setNumber(number + 5)`: `number` is `0`, so `setNumber(0 + 5)`. React adds *"replace with `5`"* to its queue.
2. `setNumber(n => n + 1)`: `n => n + 1` is an updater function. React adds *that function* to its queue.
3. `setNumber(42)`: React adds *"replace with `42`"* to its queue.

//////
1. `setNumber(number + 5)`: `number` هست `0`, بنابراین `setNumber(0 + 5)`. React *"replace with `5`"* را به صف خود اضافه می کند.
2. `setNumber(n => n + 1)`: ` => n + 1` یک تابع به روز رسانی است. React *این تابع* را به صف خود اضافه می کند.
3. `setNumber(42)`: React *"replace with `42`"* را به صف خود اضافه می کند.

در رندر بعدی، React از صف state عبور می کند:

|   queued update       | `n` | returns |
|--------------|---------|-----|
| "replace with `5`" | `0` (unused) | `5` |
| `n => n + 1` | `5` | `5 + 1 = 6` |
| "replace with `42`" | `6` (unused) | `42` |

Then React stores `42` as the final result and returns it from `useState`.
/////
سپس React 42 را به عنوان نتیجه نهایی ذخیره می کند و آن را از useState برمی گرداند.

To summarize, here's how you can think of what you're passing to the `setNumber` state setter:
/////
به طور خلاصه، در اینجا نحوه فکر کردن به آنچه که به تنظیم کننده حالت `setNumber` منتقل می کنید، آمده است:

* **An updater function** (e.g. `n => n + 1`) gets added to the queue.
* **Any other value** (e.g. number `5`) adds "replace with `5`" to the queue, ignoring what's already queued.

//////
* **یک تابع به‌روزرسانی** (به عنوان مثال ` => n + 1`) به صف اضافه می‌شود.
* **هر مقدار دیگری** (مثلاً عدد "5") "جایگزینی با "5" را به صف اضافه می‌کند، بدون توجه به آنچه قبلاً در صف قرار گرفته است.

After the event handler completes, React will trigger a re-render. During the re-render, React will process the queue. Updater functions run during rendering, so **updater functions must be [pure](/learn/keeping-components-pure)** and only *return* the result. Don't try to set state from inside of them or run other side effects. In Strict Mode, React will run each updater function twice (but discard the second result) to help you find mistakes.
/////
پس از اتمام هندلر رویداد، React یک رندر مجدد را فراخوانی می‌کند. در طول رندر مجدد، React صف را پردازش می‌کند. توابع به‌روزرسانی‌کننده در طول رندرینگ اجرا می‌شوند، بنابراین توابع به‌روزرسانی‌کننده باید خالص باشند و فقط نتیجه را برگردانند. سعی نکنید که از داخل آن‌ها وضعیت را تنظیم کنید یا اجرای اثرات جانبی دیگری را داشته باشید. در حالت Strict، React هر تابع به‌روزرسانی‌کننده را دو بار اجرا می‌کند (اما نتیجه دوم را دور می‌اندازد) تا به شما در پیدا کردن اشتباهات کمک کند.

### Naming conventions(قراردادهای نامگذاری) {/*naming-conventions*/}

It's common to name the updater function argument by the first letters of the corresponding state variable:
//////
معمول است که آرگومان تابع updater را با حروف اول متغیر حالت مربوطه نامگذاری کنید:

```js
setEnabled(e => !e);
setLastName(ln => ln.reverse());
setFriendCount(fc => fc * 2);
```

If you prefer more verbose code, another common convention is to repeat the full state variable name, like `setEnabled(enabled => !enabled)`, or to use a prefix like `setEnabled(prevEnabled => !prevEnabled)`.
///////
اگر کد پرمخاطب تری را ترجیح می دهید، یکی دیگر از قراردادهای رایج تکرار نام متغیر حالت کامل است، مانند setEnabled(enabled => !enabled)، یا استفاده از پیشوندی مانند setEnabled(prevEnabled => !prevEnabled).

<Recap>

* Setting state does not change the variable in the existing render, but it requests a new render.
* React processes state updates after event handlers have finished running. This is called batching.
* To update some state multiple times in one event, you can use `setNumber(n => n + 1)` updater function.

////////////////////

* ست کردن متغیر state را در رندر موجود تغییر نمی دهد، اما یک رندر جدید درخواست می کند.
* پردازش‌های React به‌روزرسانی‌ها را پس از پایان اجرای کنترل‌کننده‌های رویداد نشان می‌دهند. به این میگن بچینگ.
* برای به‌روزرسانی چند حالت در یک رویداد، می‌توانید از تابع به‌روزرسانی setNumber(n => n + 1) استفاده کنید.

</Recap>



<Challenges>

#### Fix a request counter {/*fix-a-request-counter*/}

You're working on an art marketplace app that lets the user submit multiple orders for an art item at the same time. Each time the user presses the "Buy" button, the "Pending" counter should increase by one. After three seconds, the "Pending" counter should decrease, and the "Completed" counter should increase.

However, the "Pending" counter does not behave as intended. When you press "Buy", it decreases to `-1` (which should not be possible!). And if you click fast twice, both counters seem to behave unpredictably.

Why does this happen? Fix both counters.



<Sandpack>

```js
import { useState } from 'react';

export default function RequestTracker() {
  const [pending, setPending] = useState(0);
  const [completed, setCompleted] = useState(0);

  async function handleClick() {
    setPending(pending + 1);
    await delay(3000);
    setPending(pending - 1);
    setCompleted(completed + 1);
  }

  return (
    <>
      <h3>
        Pending: {pending}
      </h3>
      <h3>
        Completed: {completed}
      </h3>
      <button onClick={handleClick}>
        Buy     
      </button>
    </>
  );
}

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}
```

</Sandpack>

<Solution>

Inside the `handleClick` event handler, the values of `pending` and `completed` correspond to what they were at the time of the click event. For the first render, `pending` was `0`, so `setPending(pending - 1)` becomes `setPending(-1)`, which is wrong. Since you want to *increment* or *decrement* the counters, rather than set them to a concrete value determined during the click, you can instead pass the updater functions:

<Sandpack>

```js
import { useState } from 'react';

export default function RequestTracker() {
  const [pending, setPending] = useState(0);
  const [completed, setCompleted] = useState(0);

  async function handleClick() {
    setPending(p => p + 1);
    await delay(3000);
    setPending(p => p - 1);
    setCompleted(c => c + 1);
  }

  return (
    <>
      <h3>
        Pending: {pending}
      </h3>
      <h3>
        Completed: {completed}
      </h3>
      <button onClick={handleClick}>
        Buy     
      </button>
    </>
  );
}

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}
```

</Sandpack>

This ensures that when you increment or decrement a counter, you do it in relation to its *latest* state rather than what the state was at the time of the click.

</Solution>

#### Implement the state queue yourself {/*implement-the-state-queue-yourself*/}

In this challenge, you will reimplement a tiny part of React from scratch! It's not as hard as it sounds.

Scroll through the sandbox preview. Notice that it shows **four test cases.** They correspond to the examples you've seen earlier on this page. Your task is to implement the `getFinalState` function so that it returns the correct result for each of those cases. If you implement it correctly, all four tests should pass.

You will receive two arguments: `baseState` is the initial state (like `0`), and the `queue` is an array which contains a mix of numbers (like `5`) and updater functions (like `n => n + 1`) in the order they were added.

Your task is to return the final state, just like the tables on this page show!

<Hint>

If you're feeling stuck, start with this code structure:

```js
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  for (let update of queue) {
    if (typeof update === 'function') {
      // TODO: apply the updater function
    } else {
      // TODO: replace the state
    }
  }

  return finalState;
}
```

Fill out the missing lines!

</Hint>

<Sandpack>

```js processQueue.js active
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  // TODO: do something with the queue...

  return finalState;
}
```

```js App.js
import { getFinalState } from './processQueue.js';

function increment(n) {
  return n + 1;
}
increment.toString = () => 'n => n+1';

export default function App() {
  return (
    <>
      <TestCase
        baseState={0}
        queue={[1, 1, 1]}
        expected={1}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          increment,
          increment,
          increment
        ]}
        expected={3}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
        ]}
        expected={6}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
          42,
        ]}
        expected={42}
      />
    </>
  );
}

function TestCase({
  baseState,
  queue,
  expected
}) {
  const actual = getFinalState(baseState, queue);
  return (
    <>
      <p>Base state: <b>{baseState}</b></p>
      <p>Queue: <b>[{queue.join(', ')}]</b></p>
      <p>Expected result: <b>{expected}</b></p>
      <p style={{
        color: actual === expected ?
          'green' :
          'red'
      }}>
        Your result: <b>{actual}</b>
        {' '}
        ({actual === expected ?
          'correct' :
          'wrong'
        })
      </p>
    </>
  );
}
```

</Sandpack>

<Solution>

This is the exact algorithm described on this page that React uses to calculate the final state:

<Sandpack>

```js processQueue.js active
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  for (let update of queue) {
    if (typeof update === 'function') {
      // Apply the updater function.
      finalState = update(finalState);
    } else {
      // Replace the next state.
      finalState = update;
    }
  }

  return finalState;
}
```

```js App.js
import { getFinalState } from './processQueue.js';

function increment(n) {
  return n + 1;
}
increment.toString = () => 'n => n+1';

export default function App() {
  return (
    <>
      <TestCase
        baseState={0}
        queue={[1, 1, 1]}
        expected={1}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          increment,
          increment,
          increment
        ]}
        expected={3}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
        ]}
        expected={6}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
          42,
        ]}
        expected={42}
      />
    </>
  );
}

function TestCase({
  baseState,
  queue,
  expected
}) {
  const actual = getFinalState(baseState, queue);
  return (
    <>
      <p>Base state: <b>{baseState}</b></p>
      <p>Queue: <b>[{queue.join(', ')}]</b></p>
      <p>Expected result: <b>{expected}</b></p>
      <p style={{
        color: actual === expected ?
          'green' :
          'red'
      }}>
        Your result: <b>{actual}</b>
        {' '}
        ({actual === expected ?
          'correct' :
          'wrong'
        })
      </p>
    </>
  );
}
```

</Sandpack>

Now you know how this part of React works!

</Solution>

</Challenges>