---
title: 'Removing Effect Dependencies'
---

<Intro>

When you write an Effect, the linter will verify that you've included every reactive value (like props and state) that the Effect reads in the list of your Effect's dependencies. This ensures that your Effect remains synchronized with the latest props and state of your component. Unnecessary dependencies may cause your Effect to run too often, or even create an infinite loop. Follow this guide to review and remove unnecessary dependencies from your Effects.
////////////
ترجمه: وقتی یک افکت می‌نویسید، لینتر بررسی می‌کند که هر مقدار واکنشی (مانند props و state) را که افکت در لیست وابستگی‌های آن می‌خواند، شامل شده است. این اطمینان می‌دهد که افکت شما با آخرین props و state کامپوننت شما همگام باشد. وابستگی‌های نامورازم ممکن است باعث اجرای افکت شما بیش از حد یا حتی ایجاد حلقه بی‌نهایت شود. از این راهنما برای بررسی و حذف وابستگی‌های نامورازم از افکت‌های خود استفاده کنید.¹

</Intro>

<YouWillLearn>

- How to fix infinite Effect dependency loops
- What to do when you want to remove a dependency
- How to read a value from your Effect without "reacting" to it
- How and why to avoid object and function dependencies
- Why suppressing the dependency linter is dangerous, and what to do instead
///////////
- چگونه حلقه‌های وابستگی اثر بی‌نهایت را برطرف کنیم
- چه کاری باید انجام دهید وقتی می‌خواهید یک وابستگی را حذف کنید
- چگونه مقداری را از اثر خود بخوانید بدون اینکه به آن "واکنش" دهید
- چگونه و چرا باید از وابستگی‌
</YouWillLearn>

## Dependencies should match the code {/*dependencies-should-match-the-code*/}

When you write an Effect, you first specify how to [start and stop](/learn/lifecycle-of-reactive-effects#the-lifecycle-of-an-effect) whatever you want your Effect to be doing:
//////////
وقتی که می‌خواهید یک Effect بنویسید، ابتدا باید مشخص کنید که چگونه هر آنچه را که می‌خواهید Effect شما انجام دهد، [شروع و پایان](/learn/lifecycle-of-reactive-effects#the-lifecycle-of-an-effect) داده شود..

```js {5-7}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  	// ...
}
```

Then, if you leave the Effect dependencies empty (`[]`), the linter will suggest the correct dependencies:
//////////////
سپس، اگر وابستگی‌های Effect را خالی بگذارید (`[]`), لینتر وابستگی‌های صحیح را پیشنهاد می‌دهد.

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // <-- Fix the mistake here!
  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Fill them in according to what the linter says:
آنها را مطابق آنچه که لینتر می گوید پر کنید:

```js {6}
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
}
```

[Effects "react" to reactive values.](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) Since `roomId` is a reactive value (it can change due to a re-render), the linter verifies that you've specified it as a dependency. If `roomId` receives a different value, React will re-synchronize your Effect. This ensures that the chat stays connected to the selected room and "reacts" to the dropdown:
///////////////
ترجمه کن: [اثرها به مقادیر واکنش‌پذیر "واکنش" می‌دهند.](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) از آنجایی که `roomId` یک مقدار واکنش‌پذیر است (ممکن است به دلیل رندر مجدد تغییر کند)، لینتر بررسی می‌کند که آن را به عنوان وابستگی مشخص کرده‌اید. اگر `roomId` مقدار متفاوتی دریافت کند، React اثر شما را مجدداً همگام می‌کند. این اطمینان می‌دهد که چت به اتاق انتخاب شده متصل می‌ماند و به "کشویی" واکنش می‌دهد.

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

### To remove a dependency, prove that it's not a dependency {/*to-remove-a-dependency-prove-that-its-not-a-dependency*/}

Notice that you can't "choose" the dependencies of your Effect. Every <CodeStep step={2}>reactive value</CodeStep> used by your Effect's code must be declared in your dependency list. The dependency list is determined by the surrounding code:
///////////
ترجمه کن: توجه کنید که نمی‌توانید وابستگی‌های اثر خود را "انتخاب" کنید. هر <CodeStep step={2}>مقدار واکنش‌پذیر</CodeStep> استفاده شده توسط کد اثر شما باید در لیست وابستگی‌های شما مشخص شود. لیست وابستگی‌ها توسط کد اطراف تعیین می‌شود:

```js [[2, 3, "roomId"], [2, 5, "roomId"], [2, 8, "roomId"]]
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) { // This is a reactive value
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // This Effect reads that reactive value
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ So you must specify that reactive value as a dependency of your Effect
  // ...
}
```

[Reactive values](/learn/lifecycle-of-reactive-effects#all-variables-declared-in-the-component-body-are-reactive) include props and all variables and functions declared directly inside of your component. Since `roomId` is a reactive value, you can't remove it from the dependency list. The linter wouldn't allow it:
////////////
ترجمه کن: [مقادیر واکنش‌پذیر](/learn/lifecycle-of-reactive-effects#all-variables-declared-in-the-component-body-are-reactive) شامل props و تمام متغیرها و توابعی است که به طور مستقیم در داخل کامپوننت شما اعلام شده‌اند. از آنجایی که `roomId` یک مقدار واکنش‌پذیر است، نمی‌توانید آن را از لیست وابستگی حذف کنید. لینتر اجازه نمی‌دهد:

```js {8}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // 🔴 React Hook useEffect has a missing dependency: 'roomId'
  // ...
}
```

And the linter would be right! Since `roomId` may change over time, this would introduce a bug in your code.
///////////
و لینتر درست می‌گوید! چرا که `roomId` ممکن است در طول زمان تغییر کند، این باعث ورود باگ به کد شما می‌شود.


**To remove a dependency, "prove" to the linter that it *doesn't need* to be a dependency.** For example, you can move `roomId` out of your component to prove that it's not reactive and won't change on re-renders:
////////////
**برای حذف یک وابستگی، به لینتر "اثبات" کنید که به آن نیازی نیست.** به عنوان مثال، می‌توانید `roomId` را از کامپوننت خود خارج کنید تا ثابت کنید که غیر واکنش‌پذیر است و در رندرهای مجدد تغییر نمی‌کند:

```js {2,9}
const serverUrl = 'https://localhost:1234';
const roomId = 'music'; // Not a reactive value anymore

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // ✅ All dependencies declared
  // ...
}
```

Now that `roomId` is not a reactive value (and can't change on a re-render), it doesn't need to be a dependency:
//////////////////
حالا که `roomId` یک مقدار واکنش‌پذیر نیست (و در رندر مجدد تغییر نمی‌کند)، نیازی به وابستگی ندارد.

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'music';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

This is why you could now specify an [empty (`[]`) dependency list.](/learn/lifecycle-of-reactive-effects#what-an-effect-with-empty-dependencies-means) Your Effect *really doesn't* depend on any reactive value anymore, so it *really doesn't* need to re-run when any of the component's props or state change.
///////////////
به همین دلیل، می‌توانید حالا یک [لیست وابستگی خالی (`[]`) را مشخص کنید.](/learn/lifecycle-of-reactive-effects#what-an-effect-with-empty-dependencies-means) اثر شما *واقعاً* به هیچ مقدار واکنش‌پذیری بیشتر وابسته نیست، بنابراین *واقعاً* نیازی به اجرای مجدد ندارد هنگامی که هر یک از props یا state کامپوننت تغییر کند.

### To change the dependencies, change the code {/*to-change-the-dependencies-change-the-code*/}

You might have noticed a pattern in your workflow:

1. First, you **change the code** of your Effect or how your reactive values are declared.
2. Then, you follow the linter and adjust the dependencies to **match the code you have changed.**
3. If you're not happy with the list of dependencies, you **go back to the first step** (and change the code again).
///////////////////////////////
شاید الگویی در جریان کار خود مشاهده کرده باشید:

1. ابتدا، **کد اثر خود را تغییر می‌دهید** یا نحوه تعریف مقادیر واکنش‌پذیر خود را تغییر می‌دهید.
2. سپس، شما لینتر را دنبال می‌کنید و وابستگی‌ها را به **کدی که تغییر داده‌اید، تطبیق می‌دهید.**
3. اگر با لیست وابستگی‌ها راضی نیستید، **به مرحله اول باز می‌گردید** (و کد را دوباره تغییر می‌دهید).

The last part is important. **If you want to change the dependencies, change the surrounding code first.** You can think of the dependency list as [a list of all the reactive values used by your Effect's code.](/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency) You don't *choose* what to put on that list. The list *describes* your code. To change the dependency list, change the code.
///////////////
ترجمه کن: بخش آخر این موضوع مهم است. **اگر می‌خواهید وابستگی‌ها را تغییر دهید، ابتدا کد اطراف را تغییر دهید.** می‌توانید لیست وابستگی‌ها را به عنوان [لیستی از تمام مقادیر واکنش‌پذیر استفاده شده توسط کد اثر خود](/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency) در نظر بگیرید. شما *انتخاب نمی‌کنید* که چه چیزی را در آن لیست قرار دهید. لیست *توصیف* کد شماست. برای تغییر لیست وابستگی‌ها، کد را تغییر دهید.

This might feel like solving an equation. You might start with a goal (for example, to remove a dependency), and you need to "find" the code matching that goal. Not everyone finds solving equations fun, and the same thing could be said about writing Effects! Luckily, there is a list of common recipes that you can try below.
///////////////
این ممکن است مانند حل یک معادله باشد. ممکن است با یک هدف (برای مثال، حذف یک وابستگی) شروع کنید و باید کدی را که با آن هدف همخوانی دارد "پیدا" کنید. همه افراد به حل معادلات علاقه ندارند و همین موضو

<Pitfall>

If you have an existing codebase, you might have some Effects that suppress the linter like this:
اگر یک پایگاه کد موجود دارید، ممکن است برخی از افکت‌ها داشته باشید که به صورت زیر، لینتر را سرکوب می‌کنند:

```js {3-4}
useEffect(() => {
  // ...
  // 🔴 Avoid suppressing the linter like this:
  // eslint-ignore-next-line react-hooks/exhaustive-deps
}, []);
```

**When dependencies don't match the code, there is a very high risk of introducing bugs.** By suppressing the linter, you "lie" to React about the values your Effect depends on.
ترجمه شده: **وقتی وابستگی‌ها با کد همخوانی ندارند، خطر ورود باگ بسیار بالاست.** با سرکوب کردن لینتر، به React در مورد مقادیری که اثر شما به آن‌ها وابسته است، "دروغ" می‌گویید.

Instead, use the techniques below.
در عوض، از تکنیک های زیر استفاده کنید.

</Pitfall>

<DeepDive>

#### Why is suppressing the dependency linter so dangerous? {/*why-is-suppressing-the-dependency-linter-so-dangerous*/}

Suppressing the linter leads to very unintuitive bugs that are hard to find and fix. Here's one example:
//////////////
سرکوب کردن لینتر باعث ورود باگ‌های بسیار ناهمخوانی‌زا و سخت‌یاب برای رفع آن‌ها می‌شود. یک مثال از این نوع باگ‌ها به شرح زیر است:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  function onTick() {
	setCount(count + increment);
  }

  useEffect(() => {
    const id = setInterval(onTick, 1000);
    return () => clearInterval(id);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Every second, increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

```css
button { margin: 10px; }
```

</Sandpack>

Let's say that you wanted to run the Effect "only on mount". You've read that [empty (`[]`) dependencies](/learn/lifecycle-of-reactive-effects#what-an-effect-with-empty-dependencies-means) do that, so you've decided to ignore the linter, and forcefully specified `[]` as the dependencies.
////////////
بگذارید فرض کنیم می‌خواهید اثر را "فقط در زمان نصب" اجرا کنید. شما خوانده‌اید که [وابستگی‌های خالی (`[]`)](/learn/lifecycle-of-reactive-effects#what-an-effect-with-empty-dependencies-means) این کار را انجام می‌دهند، بنابراین تصمیم گرفته‌اید که لینتر را نادیده بگیرید و به زور `[]` را به عنوان وابستگی‌ها مشخص کنید.

This counter was supposed to increment every second by the amount configurable with the two buttons. However, since you "lied" to React that this Effect doesn't depend on anything, React forever keeps using the `onTick` function from the initial render. [During that render,](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time) `count` was `0` and `increment` was `1`. This is why `onTick` from that render always calls `setCount(0 + 1)` every second, and you always see `1`. Bugs like this are harder to fix when they're spread across multiple components.
/////////////
این شما را به باگ‌های بسیار ناهمخوانی‌زا و سخت‌یاب می‌اندازد. با سرکوب کردن لینتر، به React در مورد مقادیری که اثر شما به آن‌ها وابسته است، "دروغ" می‌گویید. در این مثال، شمارنده هر ثانیه باید با مقداری که با دو دکمه قابل پیکربندی است، افزایش یابد. با این حال، از آنجایی که به React دروغ گفته‌اید که این اثر به هیچ چیز وابسته نیست، React همیشه از تابع `onTick` از رندر اولیه استفاده می‌کند. [در طول آن رندر،](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time) `count` برابر با `0` و `increment` برابر با `1` بود. به همین دلیل، `onTick` از آن رندر همیشه هر ثانیه `setCount(0 + 1)` را فراخوانی می‌کند و همیشه `1` را مشاهده می‌کنید. باگ‌هایی مانند این سخت‌تر در رفع آن‌ها هستند زمانی که در چندین کامپوننت پخش شده‌اند.

There's always a better solution than ignoring the linter! To fix this code, you need to add `onTick` to the dependency list. (To ensure the interval is only setup once, [make `onTick` an Effect Event.](/learn/separating-events-from-effects#reading-latest-props-and-state-with-effect-events))
////////////
همیشه راه‌حل بهتری از سرکوب کردن لینتر وجود دارد! برای رفع این کد، باید `onTick` را به لیست وابستگی‌ها اضافه کنید. (برای اطمینان از اینکه فاصله زمانی فقط یکبار تنظیم می‌شود، [از `onTick` یک رویداد اثر بسازید.](/learn/separating-events-from-effects#reading-latest-props-and-state-with-effect-events))

**We recommend treating the dependency lint error as a compilation error. If you don't suppress it, you will never see bugs like this.** The rest of this page documents the alternatives for this and other cases.
///////////////////
**ما توصیه می‌کنیم خطای لینتر وابستگی را به عنوان خطای کامپایل در نظر بگیرید. اگر آن را سرکوب نکنید، هرگز باگ‌هایی مانند این را نخواهید دید.** بقیه این صفحه را به بررسی جایگزین‌های این مورد و موارد دیگر اختصاص داده‌ایم.

</DeepDive>

## Removing unnecessary dependencies {/*removing-unnecessary-dependencies*/}

Every time you adjust the Effect's dependencies to reflect the code, look at the dependency list. Does it make sense for the Effect to re-run when any of these dependencies change? Sometimes, the answer is "no":

* You might want to re-execute *different parts* of your Effect under different conditions.
* You might want to only read the *latest value* of some dependency instead of "reacting" to its changes.
* A dependency may change too often *unintentionally* because it's an object or a function.

To find the right solution, you'll need to answer a few questions about your Effect. Let's walk through them.
//////////////////////
هر بار که وابستگی‌های اثر را تنظیم می‌کنید تا با کد همخوانی داشته باشد، به لیست وابستگی‌ها نگاه کنید. آیا برای اجرای مجدد اثر، منطقی است که هر یک از این وابستگی‌ها تغییر کند؟ گاهی اوقات پاسخ "خیر" است:

* شاید بخواهید *بخش‌های مختلفی* از اثر خود را در شرایط مختلف اجرا کنید.
* شاید فقط *آخرین مقدار* برخی وابستگی‌ها را بخوانید به جای "واکنش" به تغییرات آن‌ها.
* یک وابستگی ممکن است به دلیل اینکه یک شیء یا تابع است، *بی‌قصد* بسیار زیاد تغییر کند.

برای پیدا کردن راه‌حل مناسب، باید به چند سوال در مورد اثر خود پاسخ دهید. بیایید با هم به آن‌ها بپردازیم.

### Should this code move to an event handler? {/*should-this-code-move-to-an-event-handler*/}

The first thing you should think about is whether this code should be an Effect at all.

Imagine a form. On submit, you set the `submitted` state variable to `true`. You need to send a POST request and show a notification. You've put this logic inside an Effect that "reacts" to `submitted` being `true`:
/////////////
اولین چیزی که باید به آن فکر کنید این است که آیا این کد در واقع باید یک اثر باشد یا خیر.

یک فرم تصور کنید. در زمان ارسال، متغیر وضعیت `submitted` را به `true` تنظیم می‌کنید. شما باید یک درخواست POST ارسال کنید و یک اعلان نمایش دهید. این منطق را در یک اثر قرار داده‌اید که به `submitted` بودن `true` "واکنش"
```js {6-8}
function Form() {
  const [submitted, setSubmitted] = useState(false);

  useEffect(() => {
    if (submitted) {
      // 🔴 Avoid: Event-specific logic inside an Effect
      post('/api/register');
      showNotification('Successfully registered!');
    }
  }, [submitted]);

  function handleSubmit() {
    setSubmitted(true);
  }

  // ...
}
```

Later, you want to style the notification message according to the current theme, so you read the current theme. Since `theme` is declared in the component body, it is a reactive value, so you add it as a dependency:
///////////////
ترجمه شده: بعداً، می‌خواهید پیام اعلان را بر اساس تم فعلی طراحی کنید، بنابراین تم فعلی را می‌خوانید. از آنجایی که `theme` در بدنه کامپوننت اعلام شده است، یک مقدار واکنش‌پذیر است، بنابراین آن را به عنوان وابستگی اضافه می‌کنید:


```js {3,9,11}
function Form() {
  const [submitted, setSubmitted] = useState(false);
  const theme = useContext(ThemeContext);

  useEffect(() => {
    if (submitted) {
      // 🔴 Avoid: Event-specific logic inside an Effect
      post('/api/register');
      showNotification('Successfully registered!', theme);
    }
  }, [submitted, theme]); // ✅ All dependencies declared

  function handleSubmit() {
    setSubmitted(true);
  }  

  // ...
}
```

By doing this, you've introduced a bug. Imagine you submit the form first and then switch between Dark and Light themes. The `theme` will change, the Effect will re-run, and so it will display the same notification again!
///////////////
با این کار شما یک باگ معرفی کرده اید. تصور کنید ابتدا فرم را ارسال می کنید و سپس بین تم های تیره و روشن جابجا می شوید. "موضوع" تغییر می کند، افکت دوباره اجرا می شود، و بنابراین دوباره همان اعلان را نمایش می دهد!

**The problem here is that this shouldn't be an Effect in the first place.** You want to send this POST request and show the notification in response to *submitting the form,* which is a particular interaction. To run some code in response to particular interaction, put that logic directly into the corresponding event handler:
////////////////
**مشکل در اینجا این است که این باید در ابتدا یک Effect نباشد.** شما می خواهید این درخواست POST را ارسال کنید و در پاسخ به *ارسال فرم*، اعلان را نشان دهید که یک تعامل خاص است. برای اجرای کد در پاسخ به تعامل خاص، آن منطق را مستقیماً در دستگیره رویداد مربوطه قرار دهید:

```js {6-7}
function Form() {
  const theme = useContext(ThemeContext);

  function handleSubmit() {
    // ✅ Good: Event-specific logic is called from event handlers
    post('/api/register');
    showNotification('Successfully registered!', theme);
  }  

  // ...
}
```

Now that the code is in an event handler, it's not reactive--so it will only run when the user submits the form. Read more about [choosing between event handlers and Effects](/learn/separating-events-from-effects#reactive-values-and-reactive-logic) and [how to delete unnecessary Effects.](/learn/you-might-not-need-an-effect)
/////////////
حال که کد در یک دستگیره رویداد است، واکنش نشان نمی دهد - بنابراین فقط زمانی اجرا می شود که کاربر فرم را ارسال می کند. بیشتر درباره [انتخاب بین دستگیره های رویداد و Effects](/learn/separating-events-from-effects#reactive-values-and-reactive-logic) و [چگونگی حذف Effects غیر ضروری](/learn/you-might-not-need-an-effect) بخوانید..

### Is your Effect doing several unrelated things? {/*is-your-effect-doing-several-unrelated-things*/}

The next question you should ask yourself is whether your Effect is doing several unrelated things.

Imagine you're creating a shipping form where the user needs to choose their city and area. You fetch the list of `cities` from the server according to the selected `country` to show them in a dropdown:
/////////////////
سوال بعدی که باید از خودتان بپرسید این است که آیا Effect شما چندین کار غیرمرتبط را انجام می دهد.

تصور کنید که در حال ایجاد یک فرم حمل و نقل هستید که کاربر باید شهر و منطقه خود را انتخاب کند. شما لیست `شهرها` را بر اساس `کشور` انتخاب شده از سرور بازیابی می کنید تا آنها را در یک کشوی کشویی نشان دهید:

```js
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  const [city, setCity] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]); // ✅ All dependencies declared

  // ...
```

This is a good example of [fetching data in an Effect.](/learn/you-might-not-need-an-effect#fetching-data) You are synchronizing the `cities` state with the network according to the `country` prop. You can't do this in an event handler because you need to fetch as soon as `ShippingForm` is displayed and whenever the `country` changes (no matter which interaction causes it).

Now let's say you're adding a second select box for city areas, which should fetch the `areas` for the currently selected `city`. You might start by adding a second `fetch` call for the list of areas inside the same Effect:
///////////////////
این یک مثال خوب از [دریافت داده در Effect](/learn/you-might-not-need-an-effect#fetching-data) است. شما وضعیت `cities` را با شبکه بر اساس `country` همگام می کنید. نمی توانید این کار را در یک دستگیره رویداد انجام دهید زیرا باید همان زمانی که `ShippingForm` نمایش داده می شود و هر زمان که `country` تغییر می کند (بدون توجه به تعاملی که آن را ایجاد می کند) بازیابی کنید.

حال فرض کنید که دومین جعبه انتخاب برای مناطق شهری را اضافه می کنید که باید `areas` را برای `city` انتخاب شده در حال حاضر بازیابی کند. ممکن است با اضافه کردن یک دومین تماس `fetch` برای لیست مناطق در داخل همان Effect شروع کنید:

```js {15-24,28}
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    // 🔴 Avoid: A single Effect synchronizes two independent processes
    if (city) {
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
    }
    return () => {
      ignore = true;
    };
  }, [country, city]); // ✅ All dependencies declared

  // ...
```

However, since the Effect now uses the `city` state variable, you've had to add `city` to the list of dependencies. That, in turn, introduced a problem: when the user selects a different city, the Effect will re-run and call `fetchCities(country)`. As a result, you will be unnecessarily refetching the list of cities many times.
///////////
با این حال، از آنجایی که Effect در حال حاضر از متغیر وضعیت `city` استفاده می کند، شما باید `city` را به لیست وابستگی ها اضافه کنید. این باعث مشکلی شده است: هنگامی که کاربر شهر دیگری را انتخاب می کند، Effect مجدداً اجرا خواهد شد و `fetchCities(country)` را فراخوانی خواهد کرد. به عبارت دیگر، شما بسیاری از بارهای لیست شهرها را بدون دلیل تکرار می کنید.

**The problem with this code is that you're synchronizing two different unrelated things:**

1. You want to synchronize the `cities` state to the network based on the `country` prop.
1. You want to synchronize the `areas` state to the network based on the `city` state.

Split the logic into two Effects, each of which reacts to the prop that it needs to synchronize with:
///////////////////
**مشکل در این کد این است که دو چیز غیرمرتبط متفاوت را همگام می کنید:**

1. شما می خواهید وضعیت `cities` را بر اساس `country` prop با شبکه همگام کنید.
1. شما می خواهید وضعیت `areas` را بر اساس وضعیت `city` با شبکه همگام کنید.

منطق را به دو Effect تقسیم کنید، هر کدام از آنها به پروپی که باید با آن همگام شود واکنش نشان می دهد:

```js {19-33}
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]); // ✅ All dependencies declared

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  useEffect(() => {
    if (city) {
      let ignore = false;
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [city]); // ✅ All dependencies declared

  // ...
```

Now the first Effect only re-runs if the `country` changes, while the second Effect re-runs when the `city` changes. You've separated them by purpose: two different things are synchronized by two separate Effects. Two separate Effects have two separate dependency lists, so they won't trigger each other unintentionally.

The final code is longer than the original, but splitting these Effects is still correct. [Each Effect should represent an independent synchronization process.](/learn/lifecycle-of-reactive-effects#each-effect-represents-a-separate-synchronization-process) In this example, deleting one Effect doesn't break the other Effect's logic. This means they *synchronize different things,* and it's good to split them up. If you're concerned about duplication, you can improve this code by [extracting repetitive logic into a custom Hook.](/learn/reusing-logic-with-custom-hooks#when-to-use-custom-hooks)
//////////////
حالا Effect اول فقط در صورت تغییر `country` اجرا می شود، در حالی که Effect دوم هنگام تغییر `city` اجرا می شود. شما آنها را بر اساس هدف جدا کرده اید: دو چیز متفاوت توسط دو Effect جداگانه همگام می شوند. دو Effect جداگانه دارای لیست وابستگی های جداگانه هستند، بنابراین آنها به طور ناخودآگاه یکدیگر را فعال نمی کنند.

کد نهایی بلندتر از کد اصلی است، اما جداسازی این Effects هنوز صحیح است. [هر Effect باید یک فرآیند همگام سازی مستقل را نشان دهد.](/learn/lifecycle-of-reactive-effects#each-effect-represents-a-separate-synchronization-process) در این مثال، حذف یک Effect منطق Effect دیگر را نمی شکند. این به این معنی است که آنها *چیزهای متفاوتی را همگام می کنند* و خوب است آنها را جدا کنید. اگر نگران تکرار باشید، می توانید با [استخراج منطق تکراری در یک Hook سفارشی](/learn/reusing-logic-with-custom-hooks#when-to-use-custom-hooks)، این کد را بهبود بخشید..

### Are you reading some state to calculate the next state? {/*are-you-reading-some-state-to-calculate-the-next-state*/}

This Effect updates the `messages` state variable with a newly created array every time a new message arrives:
///////////
این افکت هر بار که پیام جدیدی می رسد، متغیر state «پیام ها» را با آرایه ای تازه ایجاد شده به روز می کند:

```js {2,6-8}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages([...messages, receivedMessage]);
    });
    // ...
```

It uses the `messages` variable to [create a new array](/learn/updating-arrays-in-state) starting with all the existing messages and adds the new message at the end. However, since `messages` is a reactive value read by an Effect, it must be a dependency:
//////////////
این از متغیر `messages` برای [ایجاد یک آرایه جدید](/learn/updating-arrays-in-state) با شروع از تمام پیام های موجود استفاده می کند و پیام جدید را در انتها اضافه می کند. با این حال، از آنجایی که `messages` یک مقدار واکنشی است که توسط یک Effect خوانده می شود، باید به عنوان وابستگی در نظر گرفته شود.

```js {7,10}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages([...messages, receivedMessage]);
    });
    return () => connection.disconnect();
  }, [roomId, messages]); // ✅ All dependencies declared
  // ...
```

And making `messages` a dependency introduces a problem.

Every time you receive a message, `setMessages()` causes the component to re-render with a new `messages` array that includes the received message. However, since this Effect now depends on `messages`, this will *also* re-synchronize the Effect. So every new message will make the chat re-connect. The user would not like that!

To fix the issue, don't read `messages` inside the Effect. Instead, pass an [updater function](/reference/react/useState#updating-state-based-on-the-previous-state) to `setMessages`:
////////////////////
این باعث می شود که هر بار که پیامی دریافت می کنید، `setMessages()` باعث می شود که کامپوننت با یک آرایه جدید `messages` که شامل پیام دریافت شده است، دوباره رندر شود. با این حال، از آنجایی که این Effect اکنون به `messages` وابسته است، این همچنین باعث می شود که Effect دوباره همگام سازی شود. بنابراین هر پیام جدید باعث می شود چت دوباره متصل شود. کاربر از این خبر خوش نخواهد بود!

برای رفع مشکل، `messages` را درون Effect خوانده نکنید. به جای آن، یک [تابع بروزرسانی](/reference/react/useState#updating-state-based-on-the-previous-state) را به `setMessages` منتقل کنید:


```js {7,10}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages(msgs => [...msgs, receivedMessage]);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```

**Notice how your Effect does not read the `messages` variable at all now.** You only need to pass an updater function like `msgs => [...msgs, receivedMessage]`. React [puts your updater function in a queue](/learn/queueing-a-series-of-state-updates) and will provide the `msgs` argument to it during the next render. This is why the Effect itself doesn't need to depend on `messages` anymore. As a result of this fix, receiving a chat message will no longer make the chat re-connect.
////////////////////
ترجمه کن: **توجه کنید که Effect شما دیگر متغیر `messages` را به هیچ وجه خوانده نمی کند.** شما فقط نیاز دارید تا یک تابع بروزرسانی مانند `msgs => [...msgs, receivedMessage]` را منتقل کنید. React [تابع بروزرسانی شما را در یک صف قرار می دهد](/learn/queueing-a-series-of-state-updates) و در طول رندر بعدی، `msgs` را به آن ارائه خواهد داد. به همین دلیل Effect خود نیازی به وابستگی به `messages` ندارد. به عنوان نتیجه این رفع مشکل، دریافت پیام چت دیگر چت را دوباره متصل نخواهد کرد.

### Do you want to read a value without "reacting" to its changes? {/*do-you-want-to-read-a-value-without-reacting-to-its-changes*/}

<Wip>

This section describes an **experimental API that has not yet been released** in a stable version of React.
///////////////
این بخش **API آزمایشی را توصیف می کند که هنوز** در نسخه پایدار React منتشر نشده است.

</Wip>

Suppose that you want to play a sound when the user receives a new message unless `isMuted` is `true`:
/////////////
فرض کنید وقتی کاربر پیام جدیدی دریافت می‌کند می‌خواهید صدایی پخش کنید، مگر اینکه «isMuted» «true» باشد:

```js {3,10-12}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages(msgs => [...msgs, receivedMessage]);
      if (!isMuted) {
        playSound();
      }
    });
    // ...
```

Since your Effect now uses `isMuted` in its code, you have to add it to the dependencies:
از آنجایی که افکت شما اکنون از «isMuted» در کد خود استفاده می کند، باید آن را به وابستگی ها اضافه کنید:

```js {10,15}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages(msgs => [...msgs, receivedMessage]);
      if (!isMuted) {
        playSound();
      }
    });
    return () => connection.disconnect();
  }, [roomId, isMuted]); // ✅ All dependencies declared
  // ...
```

The problem is that every time `isMuted` changes (for example, when the user presses the "Muted" toggle), the Effect will re-synchronize, and reconnect to the chat. This is not the desired user experience! (In this example, even disabling the linter would not work--if you do that, `isMuted` would get "stuck" with its old value.)

To solve this problem, you need to extract the logic that shouldn't be reactive out of the Effect. You don't want this Effect to "react" to the changes in `isMuted`. [Move this non-reactive piece of logic into an Effect Event:](/learn/separating-events-from-effects#declaring-an-effect-event)
///////////////
مشکل این است که هر بار که `isMuted` تغییر می کند (به عنوان مثال، هنگامی که کاربر دکمه "بی صدا" را فشار می دهد)، Effect دوباره همگام سازی و به چت متصل خواهد شد. این تجربه کاربر مطلوب نیست! (در این مثال، حتی غیرفعال کردن لینتر نیز کار نمی کند - اگر این کار را انجام دهید، `isMuted` با مقدار قدیمی خود "گیر می کند".)

برای حل این مشکل، باید منطقی را که نباید واکنشی باشد، از Effect خارج کنید. شما نمی خواهید این Effect به تغییرات در `isMuted` واکنش نشان دهد. [این قسمتی از منطق غیر واکنشی را به یک رویداد Effect منتقل کنید:](/learn/separating-events-from-effects#declaring-an-effect-event)

```js {1,7-12,18,21}
import { useState, useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  const onMessage = useEffectEvent(receivedMessage => {
    setMessages(msgs => [...msgs, receivedMessage]);
    if (!isMuted) {
      playSound();
    }
  });

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      onMessage(receivedMessage);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```

Effect Events let you split an Effect into reactive parts (which should "react" to reactive values like `roomId` and their changes) and non-reactive parts (which only read their latest values, like `onMessage` reads `isMuted`). **Now that you read `isMuted` inside an Effect Event, it doesn't need to be a dependency of your Effect.** As a result, the chat won't re-connect when you toggle the "Muted" setting on and off, solving the original issue!
//////////////////
رویدادهای Effect به شما اجازه می دهند یک Effect را به بخش های واکنشی (که باید به مقادیر واکنشی مانند `roomId` و تغییرات آنها "واکنش" نشان دهند) و بخش های غیر واکنشی (که فقط مقادیر جدید خود را می خوانند، مانند `onMessage` که `isMuted` را می خواند) تقسیم کنید. **حال که شما `isMuted` را در یک رویداد Effect می خوانید، نیازی به وابستگی آن به Effect شما نیست.** به عنوان نتیجه، هنگامی که شما تنظیم "بی صدا" را روشن و خاموش می کنید، چت دوباره متصل نخواهد شد و مشکل اصلی حل خواهد شد!

#### Wrapping an event handler from the props {/*wrapping-an-event-handler-from-the-props*/}

You might run into a similar problem when your component receives an event handler as a prop:
هنگامی که component شما یک کنترلر رویداد را به عنوان یک پشتیبانی دریافت می کند، ممکن است با مشکل مشابهی مواجه شوید:

```js {1,8,11}
function ChatRoom({ roomId, onReceiveMessage }) {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      onReceiveMessage(receivedMessage);
    });
    return () => connection.disconnect();
  }, [roomId, onReceiveMessage]); // ✅ All dependencies declared
  // ...
```

Suppose that the parent component passes a *different* `onReceiveMessage` function on every render:
//////////////
فرض کنید که component والد در هر رندر، یک تابع *متفاوت* «onReceiveMessage» را ارسال می کند:

```js {3-5}
<ChatRoom
  roomId={roomId}
  onReceiveMessage={receivedMessage => {
    // ...
  }}
/>
```

Since `onReceiveMessage` is a dependency, it would cause the Effect to re-synchronize after every parent re-render. This would make it re-connect to the chat. To solve this, wrap the call in an Effect Event:
///////////////
زیرا `onReceiveMessage` یک وابستگی است، باعث می شود Effect پس از هر بار رندر مجدد والدین دوباره همگام سازی شود. این باعث می شود که دوباره به چت متصل شود. برای حل این مشکل، تماس را در یک رویداد Effect قرار دهید:

```js {4-6,12,15}
function ChatRoom({ roomId, onReceiveMessage }) {
  const [messages, setMessages] = useState([]);

  const onMessage = useEffectEvent(receivedMessage => {
    onReceiveMessage(receivedMessage);
  });

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      onMessage(receivedMessage);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```

Effect Events aren't reactive, so you don't need to specify them as dependencies. As a result, the chat will no longer re-connect even if the parent component passes a function that's different on every re-render.
/////////////
رویدادهای Effect به شما اجازه می دهند یک Effect را به بخش های واکنشی (که باید به مقادیر واکنشی مانند `roomId` و تغییرات آنها "واکنش" نشان دهند) و بخش های غیر واکنشی (که فقط مقادیر جدید خود را می خوانند، مانند `onMessage` که `isMuted` را می خواند) تقسیم کنید. **حال که شما `onReceiveMessage` را در یک رویداد Effect استفاده می کنید، نیازی به وابستگی آن به Effect شما نیست.** به عنوان نتیجه، حتی اگر کامپوننت والدین یک تابع متفاوت را در هر بار رندر مجدد ارسال کند، چت دیگر دوباره متصل نخواهد شد.

#### Separating reactive and non-reactive code {/*separating-reactive-and-non-reactive-code*/}

In this example, you want to log a visit every time `roomId` changes. You want to include the current `notificationCount` with every log, but you *don't* want a change to `notificationCount` to trigger a log event.
//////////
در این مثال، شما می خواهید هر بار که `roomId` تغییر کند، یک بازدید را ثبت کنید. شما می خواهید با هر لاگ، `notificationCount` فعلی را شامل کنید، اما نمی خواهید تغییر `notificationCount` باعث رخداد لاگ شود.
برای حل این مشکل، باید منطقی را که نباید واکنشی باشد، از Effect خارج کنید. شما نمی خواهید این Effect به تغییرات در `notificationCount` واکنش نشان دهد. [این قسمتی از منطق غیر واکنشی را به یک رویداد Effect منتقل کنید:](/learn/separating-events-from-effects#declaring-an-effect-event)

The solution is again to split out the non-reactive code into an Effect Event:
راه حل دوباره این است که کد غیر واکنشی را به یک رویداد افکت تقسیم کنید:

```js {2-4,7}
function Chat({ roomId, notificationCount }) {
  const onVisit = useEffectEvent(visitedRoomId => {
    logVisit(visitedRoomId, notificationCount);
  });

  useEffect(() => {
    onVisit(roomId);
  }, [roomId]); // ✅ All dependencies declared
  // ...
}
```

You want your logic to be reactive with regards to `roomId`, so you read `roomId` inside of your Effect. However, you don't want a change to `notificationCount` to log an extra visit, so you read `notificationCount` inside of the Effect Event. [Learn more about reading the latest props and state from Effects using Effect Events.](/learn/separating-events-from-effects#reading-latest-props-and-state-with-effect-events)
///////////////////
شما می خواهید منطق خود را در مورد `roomId` واکنشی کنید، بنابراین `roomId` را در داخل Effect خود می خوانید. با این حال، شما نمی خواهید تغییر `notificationCount` باعث ثبت بازدید اضافی شود، بنابراین `notificationCount` را در داخل رویداد Effect می خوانید. [بیشتر در مورد خواندن آخرین props و state از Effects با استفاده از Effect Events بیاموزید.](/learn/separating-events-from-effects#reading-latest-props-and-state-with-effect-events)

### Does some reactive value change unintentionally? {/*does-some-reactive-value-change-unintentionally*/}

Sometimes, you *do* want your Effect to "react" to a certain value, but that value changes more often than you'd like--and might not reflect any actual change from the user's perspective. For example, let's say that you create an `options` object in the body of your component, and then read that object from inside of your Effect:
/////////////////
گاهی اوقات، شما می خواهید Effect خود را به یک مقدار خاص "واکنش" دهید، اما آن مقدار بیشتر از آنچه که می خواهید تغییر می کند - و ممکن است هیچ تغییر واقعی از دیدگاه کاربر را نشان ندهد. به عنوان مثال، فرض کنید که شما یک شیء `options` را در بدنه کامپوننت خود ایجاد کرده اید، و سپس آن شیء را از داخل Effect خود می خوانید:

```js {3-6,9}
function ChatRoom({ roomId }) {
  // ...
  const options = {
    serverUrl: serverUrl,
    roomId: roomId
  };

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    // ...
```

This object is declared in the component body, so it's a [reactive value.](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) When you read a reactive value like this inside an Effect, you declare it as a dependency. This ensures your Effect "reacts" to its changes:
////////////
این شیء در بدنه کامپوننت اعلام شده است، بنابراین یک [مقدار واکنشی](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) است. هنگامی که یک مقدار واکنشی را مانند این درون Effect خوانده می کنید، آن را به عنوان وابستگی اعلام می کنید. این باعث می شود که Effect شما به تغییرات آن "واکنش" نشان دهد:.

```js {3,6}
  // ...
  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]); // ✅ All dependencies declared
  // ...
```

It is important to declare it as a dependency! This ensures, for example, that if the `roomId` changes, your Effect will re-connect to the chat with the new `options`. However, there is also a problem with the code above. To see it, try typing into the input in the sandbox below, and watch what happens in the console:
////////////////
این شیء در بدنه کامپوننت اعلام شده است، بنابراین یک [مقدار واکنشی](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) است. هنگامی که یک مقدار واکنشی را مانند این درون Effect خوانده می کنید، آن را به عنوان وابستگی اعلام می کنید. این باعث می شود که Effect شما به تغییرات آن "واکنش" نشان دهد:.

به عنوان مثال، شما می خواهید منطق خود را در مورد `roomId` واکنشی کنید، بنابراین `roomId` را در داخل Effect خود می خوانید. با این حال، شما نمی خواهید تغییر `notificationCount` باعث ثبت بازدید اضافی شود، بنابراین `notificationCount` را در داخل رویداد Effect می خوانید. [بیشتر در مورد خواندن آخرین props و state از Effects با استفاده از Effect Events بیاموزید.](/learn/separating-events-from-effects#reading-latest-props-and-state-with-effect-events)

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  // Temporarily disable the linter to demonstrate the problem
  // eslint-disable-next-line react-hooks/exhaustive-deps
  const options = {
    serverUrl: serverUrl,
    roomId: roomId
  };

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]);

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input value={message} onChange={e => setMessage(e.target.value)} />
    </>
  );
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

In the sandbox above, the input only updates the `message` state variable. From the user's perspective, this should not affect the chat connection. However, every time you update the `message`, your component re-renders. When your component re-renders, the code inside of it runs again from scratch.
////////////
در محیط بالا، ورودی فقط متغیر وضعیت `message` را به روز می کند. از دیدگاه کاربر، این باید ارتباط گفتگو را تحت تاثیر قرار ندهد. با این حال، هر بار که `message` را به روز می کنید، اجزای شما مجدداً رندر می شوند. هنگامی که اجزای شما مجدداً رندر می شوند، کد درون آن از ابتدا اجرا می شود.

A new `options` object is created from scratch on every re-render of the `ChatRoom` component. React sees that the `options` object is a *different object* from the `options` object created during the last render. This is why it re-synchronizes your Effect (which depends on `options`), and the chat re-connects as you type.
///////////

**This problem only affects objects and functions. In JavaScript, each newly created object and function is considered distinct from all the others. It doesn't matter that the contents inside of them may be the same!**
////////////////////
این مشکل فقط بر روی اشیاء و توابع تاثیر می گذارد. در جاوااسکریپت، هر شیء و تابع جدیدی که ایجاد می شود، به عنوان متمایز از همه دیگران در نظر گرفته می شود. این مهم نیست که محتوای درون آنها ممکن است یکسان باشد! ¹
//////////////////
یک شیء `options` جدید در هر بار رندر مجدد از اجزای `ChatRoom` ایجاد می شود. React می بیند که شیء `options` با شیء `options` ایجاد شده در زمان رندر قبلی *متفاوت است*. به همین دلیل، اثر شما (که به `options` وابسته است) دوباره همگام سازی می شود و در حالی که شما تایپ می کنید، گفتگو مجدداً برقرار می شود. ¹

```js {7-8}
// During the first render
const options1 = { serverUrl: 'https://localhost:1234', roomId: 'music' };

// During the next render
const options2 = { serverUrl: 'https://localhost:1234', roomId: 'music' };

// These are two different objects!
console.log(Object.is(options1, options2)); // false
```

**Object and function dependencies can make your Effect re-synchronize more often than you need.** 
///////////
**وابستگی های شی و تابع می تواند باعث شود که افکت شما بیشتر از آنچه نیاز دارید دوباره همگام شود.**

This is why, whenever possible, you should try to avoid objects and functions as your Effect's dependencies. Instead, try moving them outside the component, inside the Effect, or extracting primitive values out of them.
//////////////
بنابراین، هر زمان که امکان پذیر باشد، باید سعی کنید از اشیاء و توابع به عنوان وابستگی های اثر خود دوری کنید. به جای آن، سعی کنید آنها را خارج از اجزای شما، درون اثر، یا استخراج مقادیر ابتدایی از آنها قرار دهید. ¹

#### Move static objects and functions outside your component {/*move-static-objects-and-functions-outside-your-component*/}

If the object does not depend on any props and state, you can move that object outside your component:
////////////////
اگر شیء به هیچ پروپ و وضعیتی وابسته نباشد، می توانید آن شیء را خارج از اجزای خود قرار دهید. ¹

```js {1-4,13}
const options = {
  serverUrl: 'https://localhost:1234',
  roomId: 'music'
};

function ChatRoom() {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, []); // ✅ All dependencies declared
  // ...
```

This way, you *prove* to the linter that it's not reactive. It can't change as a result of a re-render, so it doesn't need to be a dependency. Now re-rendering `ChatRoom` won't cause your Effect to re-synchronize.
////////////
به این روش، شما به لینتر *اثبات* می کنید که آن راکتیو نیست. به عنوان نتیجه از یک رندر مجدد تغییر نمی کند، بنابراین نیازی به وابستگی ندارد. حالا رندر مجدد `ChatRoom` باعث همگام سازی مجدد اثر شما نخواهد شد. ¹

This works for functions too:
این برای توابع نیز کار می کند:

```js {1-6,12}
function createOptions() {
  return {
    serverUrl: 'https://localhost:1234',
    roomId: 'music'
  };
}

function ChatRoom() {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const options = createOptions();
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, []); // ✅ All dependencies declared
  // ...
```

Since `createOptions` is declared outside your component, it's not a reactive value. This is why it doesn't need to be specified in your Effect's dependencies, and why it won't ever cause your Effect to re-synchronize.
///////////////
از آنجایی که `createOptions` خارج از اجزای شما اعلام شده است، مقدار راکتیو نیست. به همین دلیل، نیازی به مشخص کردن آن در وابستگی های اثر شما نیست و هرگز باعث همگام سازی مجدد اثر شما نخواهد شد. ¹

#### Move dynamic objects and functions inside your Effect {/*move-dynamic-objects-and-functions-inside-your-effect*/}

If your object depends on some reactive value that may change as a result of a re-render, like a `roomId` prop, you can't pull it *outside* your component. You can, however, move its creation *inside* of your Effect's code:
///////////////
اگر شیء شما به برخی از مقادیر راکتیو وابسته است که ممکن است به عنوان نتیجه یک رندر مجدد تغییر کنند، مانند پروپ `roomId`، نمی توانید آن را *خارج* از اجزای خود بکشید. با این حال، می توانید ایجاد آن را *درون* کد اثر خود قرار دهید: ¹

```js {7-10,11,14}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```

Now that `options` is declared inside of your Effect, it is no longer a dependency of your Effect. Instead, the only reactive value used by your Effect is `roomId`. Since `roomId` is not an object or function, you can be sure that it won't be *unintentionally* different. In JavaScript, numbers and strings are compared by their content:
///////////////////////
اکنون که "options" در داخل افکت شما اعلام شده است، دیگر وابستگی به افکت شما نیست. در عوض، تنها مقدار واکنشی که توسط Effect شما استفاده می‌شود، «roomId» است. از آنجایی که «roomId» یک شی یا تابع نیست، می‌توانید مطمئن باشید که *ناخواسته* متفاوت نخواهد بود. در جاوا اسکریپت، اعداد و رشته ها با محتوایشان مقایسه می شوند:
/////////////////////////
اکنون که options درون اثر شما اعلام شده است، دیگر وابستگی اثر شما نیست. به جای آن، تنها مقدار راکتیوی که توسط اثر شما استفاده می شود، roomId است. از آنجایی که roomId یک شیء یا تابع نیست، مطمئن باشید که به طور ناخودآگاه متفاوت نخواهد بود. در جاوااسکریپت، اعداد و رشته ها بر اساس محتوای خود مقایسه می شوند1

```js {7-8}
// During the first render
const roomId1 = 'music';

// During the next render
const roomId2 = 'music';

// These two strings are the same!
console.log(Object.is(roomId1, roomId2)); // true
```

Thanks to this fix, the chat no longer re-connects if you edit the input:
به لطف این اصلاح، اگر ورودی را ویرایش کنید، چت دیگر دوباره وصل نمی شود:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input value={message} onChange={e => setMessage(e.target.value)} />
    </>
  );
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

However, it *does* re-connect when you change the `roomId` dropdown, as you would expect.
با این حال، همانطور که انتظار دارید، هنگامی که منوی کشویی «roomId» را تغییر می‌دهید * دوباره متصل می‌شود.

This works for functions, too:
این برای توابع نیز کار می کند:

```js {7-12,14}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    function createOptions() {
      return {
        serverUrl: serverUrl,
        roomId: roomId
      };
    }

    const options = createOptions();
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```

You can write your own functions to group pieces of logic inside your Effect. As long as you also declare them *inside* your Effect, they're not reactive values, and so they don't need to be dependencies of your Effect.
////////////////////
شما می توانید توابع خود را برای گروه بندی قطعات منطق داخل اثر خود بنویسید. تا زمانی که آنها را همچنین *درون* اثر خود اعلام کنید، آنها مقادیر راکتیو نیستند و بنابراین نیازی به وابستگی های اثر شما ندارند. ¹

#### Read primitive values from objects {/*read-primitive-values-from-objects*/}

Sometimes, you may receive an object from props:
گاهی اوقات، ممکن است یک شی از props دریافت کنید:

```js {1,5,8}
function ChatRoom({ options }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]); // ✅ All dependencies declared
  // ...
```

The risk here is that the parent component will create the object during rendering:
خطر در اینجا این است که component والد شی را در حین رندر ایجاد می کند:

```js {3-6}
<ChatRoom
  roomId={roomId}
  options={{
    serverUrl: serverUrl,
    roomId: roomId
  }}
/>
```

This would cause your Effect to re-connect every time the parent component re-renders. To fix this, read information from the object *outside* the Effect, and avoid having object and function dependencies:
//////////////
این باعث می شود اثر شما هر بار که اجزای والد را مجدداً رندر می کند، مجدداً به گفتگو متصل شود. برای رفع این مشکل، اطلاعات را از شیء *خارج* از اثر بخوانید و از وابستگی های شیء و تابع خودداری کنید. ¹

```js {4,7-8,12}
function ChatRoom({ options }) {
  const [message, setMessage] = useState('');

  const { roomId, serverUrl } = options;
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ All dependencies declared
  // ...
```

The logic gets a little repetitive (you read some values from an object outside an Effect, and then create an object with the same values inside the Effect). But it makes it very explicit what information your Effect *actually* depends on. If an object is re-created unintentionally by the parent component, the chat would not re-connect. However, if `options.roomId` or `options.serverUrl` really are different, the chat would re-connect.
////////////////////
منطق کاری کمی تکراری می شود (شما برخی از مقادیر را از یک شیء خارج از اثر می خوانید و سپس یک شیء با همان مقادیر درون اثر ایجاد می کنید). اما این باعث می شود که وابستگی های اثر شما *واقعاً* به چه اطلاعاتی وابسته است، بسیار صریح می شود. اگر یک شیء به طور ناخودآگاه توسط اجزای والد مجدداً ایجاد شود، گفتگو مجدداً برقرار نمی شود. با این حال، اگر `options.roomId` یا `options.serverUrl` واقعاً متفاوت باشند، گفتگو مجدداً برقرار خواهد شد. ¹

#### Calculate primitive values from functions {/*calculate-primitive-values-from-functions*/}

The same approach can work for functions. For example, suppose the parent component passes a function:
///////////////
همین رویکرد می تواند برای توابع کار کند. به عنوان مثال، فرض کنید component والد تابعی را ارسال می کند:

```js {3-8}
<ChatRoom
  roomId={roomId}
  getOptions={() => {
    return {
      serverUrl: serverUrl,
      roomId: roomId
    };
  }}
/>
```

To avoid making it a dependency (and causing it to re-connect on re-renders), call it outside the Effect. This gives you the `roomId` and `serverUrl` values that aren't objects, and that you can read from inside your Effect:
////////////////
برای جلوگیری از تبدیل آن به وابستگی (و باعث شدن اتصال مجدد در رندرهای مجدد)، آن را خارج از اثر فراخوانی کنید. این شما را با مقادیر `roomId` و `serverUrl` که اشیاء نیستند و می توانید از درون اثر خود بخوانید، تامین می کند. ¹

```js {1,4}
function ChatRoom({ getOptions }) {
  const [message, setMessage] = useState('');

  const { roomId, serverUrl } = getOptions();
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ All dependencies declared
  // ...
```

This only works for [pure](/learn/keeping-components-pure) functions because they are safe to call during rendering. If your function is an event handler, but you don't want its changes to re-synchronize your Effect, [wrap it into an Effect Event instead.](#do-you-want-to-read-a-value-without-reacting-to-its-changes)
///////////////
این تنها برای توابع [pure](/learn/keeping-components-pure) کار می کند زیرا امن است که در طول رندرینگ فراخوانی شود. اگر تابع شما یک رویداد دهنده است، اما نمی خواهید تغییرات آن را باز همگام سازی کنید، به جای آن آن را به یک رویداد اثر بپیوندید. [wrap it into an Effect Event instead.](do-you-want-to-read-a-value-without-reacting-to-its-changes)

<Recap>

- Dependencies should always match the code.
- When you're not happy with your dependencies, what you need to edit is the code.
- Suppressing the linter leads to very confusing bugs, and you should always avoid it.
- To remove a dependency, you need to "prove" to the linter that it's not necessary.
- If some code should run in response to a specific interaction, move that code to an event handler.
- If different parts of your Effect should re-run for different reasons, split it into several Effects.
- If you want to update some state based on the previous state, pass an updater function.
- If you want to read the latest value without "reacting" it, extract an Effect Event from your Effect.
- In JavaScript, objects and functions are considered different if they were created at different times.
- Try to avoid object and function dependencies. Move them outside the component or inside the Effect.
///////////////
- وابستگی ها همیشه باید با کد مطابقت داشته باشند.
- هنگامی که از وابستگی های خود راضی نیستید، آنچه باید ویرایش کنید کد است.
- سرکوب کردن لینتر منجر به باگ های بسیار گیج کننده می شود و همیشه باید از آن اجتناب کنید.
- برای حذف یک وابستگی، باید به لینتر "اثبات" کنید که آن لازم نیست.
- اگر بخشی از کد شما باید در پاسخ به یک تعامل خاص اجرا شود، آن کد را به یک رویداد اثر منتقل کنید.
- اگر بخش های مختلفی از اثر شما برای دلایل مختلف باید مجدداً اجرا شوند، آن را به چندین اثر تقسیم کنید.
- اگر می خواهید بر اساس حالت قبلی، برخی از وضعیت ها را به روز کنید، یک تابع به روز کننده را منتقل کنید.
- اگر می خواهید آخرین مقدار را بدون "واکنش" خواندن، یک رویداد اثر را از اثر خود استخراج کنید.
- در جاوا اسکریپت، اشیاء و توابع در صورت ایجاد در زمان های مختلف، متفاوت در نظر گرفته می شوند.
- سعی کنید از وابستگی های شیء و تابع اجتناب کنید. آنها را خارج از کامپوننت یا درون اثر منتقل کنید.

</Recap>

<Challenges>

#### Fix a resetting interval {/*fix-a-resetting-interval*/}

This Effect sets up an interval that ticks every second. You've noticed something strange happening: it seems like the interval gets destroyed and re-created every time it ticks. Fix the code so that the interval doesn't get constantly re-created.

<Hint>

It seems like this Effect's code depends on `count`. Is there some way to not need this dependency? There should be a way to update the `count` state based on its previous value without adding a dependency on that value.

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('✅ Creating an interval');
    const id = setInterval(() => {
      console.log('⏰ Interval tick');
      setCount(count + 1);
    }, 1000);
    return () => {
      console.log('❌ Clearing an interval');
      clearInterval(id);
    };
  }, [count]);

  return <h1>Counter: {count}</h1>
}
```

</Sandpack>

<Solution>

You want to update the `count` state to be `count + 1` from inside the Effect. However, this makes your Effect depend on `count`, which changes with every tick, and that's why your interval gets re-created on every tick.

To solve this, use the [updater function](/reference/react/useState#updating-state-based-on-the-previous-state) and write `setCount(c => c + 1)` instead of `setCount(count + 1)`:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('✅ Creating an interval');
    const id = setInterval(() => {
      console.log('⏰ Interval tick');
      setCount(c => c + 1);
    }, 1000);
    return () => {
      console.log('❌ Clearing an interval');
      clearInterval(id);
    };
  }, []);

  return <h1>Counter: {count}</h1>
}
```

</Sandpack>

Instead of reading `count` inside the Effect, you pass a `c => c + 1` instruction ("increment this number!") to React. React will apply it on the next render. And since you don't need to read the value of `count` inside your Effect anymore, so you can keep your Effect's dependencies empty (`[]`). This prevents your Effect from re-creating the interval on every tick.

</Solution>

#### Fix a retriggering animation {/*fix-a-retriggering-animation*/}

In this example, when you press "Show", a welcome message fades in. The animation takes a second. When you press "Remove", the welcome message immediately disappears. The logic for the fade-in animation is implemented in the `animation.js` file as plain JavaScript [animation loop.](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) You don't need to change that logic. You can treat it as a third-party library. Your Effect creates an instance of `FadeInAnimation` for the DOM node, and then calls `start(duration)` or `stop()` to control the animation. The `duration` is controlled by a slider. Adjust the slider and see how the animation changes.

This code already works, but there is something you want to change. Currently, when you move the slider that controls the `duration` state variable, it retriggers the animation. Change the behavior so that the Effect does not "react" to the `duration` variable. When you press "Show", the Effect should use the current `duration` on the slider. However, moving the slider itself should not by itself retrigger the animation.

<Hint>

Is there a line of code inside the Effect that should not be reactive? How can you move non-reactive code out of the Effect?

</Hint>

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect, useRef } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { FadeInAnimation } from './animation.js';

function Welcome({ duration }) {
  const ref = useRef(null);

  useEffect(() => {
    const animation = new FadeInAnimation(ref.current);
    animation.start(duration);
    return () => {
      animation.stop();
    };
  }, [duration]);

  return (
    <h1
      ref={ref}
      style={{
        opacity: 0,
        color: 'white',
        padding: 50,
        textAlign: 'center',
        fontSize: 50,
        backgroundImage: 'radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%)'
      }}
    >
      Welcome
    </h1>
  );
}

export default function App() {
  const [duration, setDuration] = useState(1000);
  const [show, setShow] = useState(false);

  return (
    <>
      <label>
        <input
          type="range"
          min="100"
          max="3000"
          value={duration}
          onChange={e => setDuration(Number(e.target.value))}
        />
        <br />
        Fade in duration: {duration} ms
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome duration={duration} />}
    </>
  );
}
```

```js animation.js
export class FadeInAnimation {
  constructor(node) {
    this.node = node;
  }
  start(duration) {
    this.duration = duration;
    if (this.duration === 0) {
      // Jump to end immediately
      this.onProgress(1);
    } else {
      this.onProgress(0);
      // Start animating
      this.startTime = performance.now();
      this.frameId = requestAnimationFrame(() => this.onFrame());
    }
  }
  onFrame() {
    const timePassed = performance.now() - this.startTime;
    const progress = Math.min(timePassed / this.duration, 1);
    this.onProgress(progress);
    if (progress < 1) {
      // We still have more frames to paint
      this.frameId = requestAnimationFrame(() => this.onFrame());
    }
  }
  onProgress(progress) {
    this.node.style.opacity = progress;
  }
  stop() {
    cancelAnimationFrame(this.frameId);
    this.startTime = null;
    this.frameId = null;
    this.duration = 0;
  }
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
```

</Sandpack>

<Solution>

Your Effect needs to read the latest value of `duration`, but you don't want it to "react" to changes in `duration`. You use `duration` to start the animation, but starting animation isn't reactive. Extract the non-reactive line of code into an Effect Event, and call that function from your Effect.

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect, useRef } from 'react';
import { FadeInAnimation } from './animation.js';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

function Welcome({ duration }) {
  const ref = useRef(null);

  const onAppear = useEffectEvent(animation => {
    animation.start(duration);
  });

  useEffect(() => {
    const animation = new FadeInAnimation(ref.current);
    onAppear(animation);
    return () => {
      animation.stop();
    };
  }, []);

  return (
    <h1
      ref={ref}
      style={{
        opacity: 0,
        color: 'white',
        padding: 50,
        textAlign: 'center',
        fontSize: 50,
        backgroundImage: 'radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%)'
      }}
    >
      Welcome
    </h1>
  );
}

export default function App() {
  const [duration, setDuration] = useState(1000);
  const [show, setShow] = useState(false);

  return (
    <>
      <label>
        <input
          type="range"
          min="100"
          max="3000"
          value={duration}
          onChange={e => setDuration(Number(e.target.value))}
        />
        <br />
        Fade in duration: {duration} ms
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome duration={duration} />}
    </>
  );
}
```

```js animation.js
export class FadeInAnimation {
  constructor(node) {
    this.node = node;
  }
  start(duration) {
    this.duration = duration;
    this.onProgress(0);
    this.startTime = performance.now();
    this.frameId = requestAnimationFrame(() => this.onFrame());
  }
  onFrame() {
    const timePassed = performance.now() - this.startTime;
    const progress = Math.min(timePassed / this.duration, 1);
    this.onProgress(progress);
    if (progress < 1) {
      // We still have more frames to paint
      this.frameId = requestAnimationFrame(() => this.onFrame());
    }
  }
  onProgress(progress) {
    this.node.style.opacity = progress;
  }
  stop() {
    cancelAnimationFrame(this.frameId);
    this.startTime = null;
    this.frameId = null;
    this.duration = 0;
  }
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
```

</Sandpack>

Effect Events like `onAppear` are not reactive, so you can read `duration` inside without retriggering the animation.

</Solution>

#### Fix a reconnecting chat {/*fix-a-reconnecting-chat*/}

In this example, every time you press "Toggle theme", the chat re-connects. Why does this happen? Fix the mistake so that the chat re-connects only when you edit the Server URL or choose a different chat room.

Treat `chat.js` as an external third-party library: you can consult it to check its API, but don't edit it.

<Hint>

There's more than one way to fix this, but ultimately you want to avoid having an object as your dependency.

</Hint>

<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  const options = {
    serverUrl: serverUrl,
    roomId: roomId
  };

  return (
    <div className={isDark ? 'dark' : 'light'}>
      <button onClick={() => setIsDark(!isDark)}>
        Toggle theme
      </button>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom options={options} />
    </div>
  );
}
```

```js ChatRoom.js active
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom({ options }) {
  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]);

  return <h1>Welcome to the {options.roomId} room!</h1>;
}
```

```js chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
label, button { display: block; margin-bottom: 5px; }
.dark { background: #222; color: #eee; }
```

</Sandpack>

<Solution>

Your Effect is re-running because it depends on the `options` object. Objects can be re-created unintentionally, you should try to avoid them as dependencies of your Effects whenever possible.

The least invasive fix is to read `roomId` and `serverUrl` right outside the Effect, and then make the Effect depend on those primitive values (which can't change unintentionally). Inside the Effect, create an object and pass it to `createConnection`:

<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  const options = {
    serverUrl: serverUrl,
    roomId: roomId
  };

  return (
    <div className={isDark ? 'dark' : 'light'}>
      <button onClick={() => setIsDark(!isDark)}>
        Toggle theme
      </button>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom options={options} />
    </div>
  );
}
```

```js ChatRoom.js active
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom({ options }) {
  const { roomId, serverUrl } = options;
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return <h1>Welcome to the {options.roomId} room!</h1>;
}
```

```js chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
label, button { display: block; margin-bottom: 5px; }
.dark { background: #222; color: #eee; }
```

</Sandpack>

It would be even better to replace the object `options` prop with the more specific `roomId` and `serverUrl` props:

<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  return (
    <div className={isDark ? 'dark' : 'light'}>
      <button onClick={() => setIsDark(!isDark)}>
        Toggle theme
      </button>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        serverUrl={serverUrl}
      />
    </div>
  );
}
```

```js ChatRoom.js active
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom({ roomId, serverUrl }) {
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
label, button { display: block; margin-bottom: 5px; }
.dark { background: #222; color: #eee; }
```

</Sandpack>

Sticking to primitive props where possible makes it easier to optimize your components later.

</Solution>

#### Fix a reconnecting chat, again {/*fix-a-reconnecting-chat-again*/}

This example connects to the chat either with or without encryption. Toggle the checkbox and notice the different messages in the console when the encryption is on and off. Try changing the room. Then, try toggling the theme. When you're connected to a chat room, you will receive new messages every few seconds. Verify that their color matches the theme you've picked.

In this example, the chat re-connects every time you try to change the theme. Fix this. After the fix, changing the theme should not re-connect the chat, but toggling encryption settings or changing the room should re-connect.

Don't change any code in `chat.js`. Other than that, you can change any code as long as it results in the same behavior. For example, you may find it helpful to change which props are being passed down.

<Hint>

You're passing down two functions: `onMessage` and `createConnection`. Both of them are created from scratch every time `App` re-renders. They are considered to be new values every time, which is why they re-trigger your Effect.

One of these functions is an event handler. Do you know some way to call an event handler an Effect without "reacting" to the new values of the event handler function? That would come in handy!

Another of these functions only exists to pass some state to an imported API method. Is this function really necessary? What is the essential information that's being passed down? You might need to move some imports from `App.js` to `ChatRoom.js`.

</Hint>

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';
import { showNotification } from './notifications.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [isEncrypted, setIsEncrypted] = useState(false);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <label>
        <input
          type="checkbox"
          checked={isEncrypted}
          onChange={e => setIsEncrypted(e.target.checked)}
        />
        Enable encryption
      </label>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        onMessage={msg => {
          showNotification('New message: ' + msg, isDark ? 'dark' : 'light');
        }}
        createConnection={() => {
          const options = {
            serverUrl: 'https://localhost:1234',
            roomId: roomId
          };
          if (isEncrypted) {
            return createEncryptedConnection(options);
          } else {
            return createUnencryptedConnection(options);
          }
        }}
      />
    </>
  );
}
```

```js ChatRoom.js active
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function ChatRoom({ roomId, createConnection, onMessage }) {
  useEffect(() => {
    const connection = createConnection();
    connection.on('message', (msg) => onMessage(msg));
    connection.connect();
    return () => connection.disconnect();
  }, [createConnection, onMessage]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js chat.js
export function createEncryptedConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ 🔐 Connecting to "' + roomId + '" room... (encrypted)');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ 🔐 Disconnected from "' + roomId + '" room (encrypted)');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}

export function createUnencryptedConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room (unencrypted)...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room (unencrypted)');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

```js notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label, button { display: block; margin-bottom: 5px; }
```

</Sandpack>

<Solution>

There's more than one correct way to solve this, but here is one possible solution.

In the original example, toggling the theme caused different `onMessage` and `createConnection` functions to be created and passed down. Since the Effect depended on these functions, the chat would re-connect every time you toggle the theme.

To fix the problem with `onMessage`, you needed to wrap it into an Effect Event:

```js {1,2,6}
export default function ChatRoom({ roomId, createConnection, onMessage }) {
  const onReceiveMessage = useEffectEvent(onMessage);

  useEffect(() => {
    const connection = createConnection();
    connection.on('message', (msg) => onReceiveMessage(msg));
    // ...
```

Unlike the `onMessage` prop, the `onReceiveMessage` Effect Event is not reactive. This is why it doesn't need to be a dependency of your Effect. As a result, changes to `onMessage` won't cause the chat to re-connect.

You can't do the same with `createConnection` because it *should* be reactive. You *want* the Effect to re-trigger if the user switches between an encrypted and an unencryption connection, or if the user switches the current room. However, because `createConnection` is a function, you can't check whether the information it reads has *actually* changed or not. To solve this, instead of passing `createConnection` down from the `App` component, pass the raw `roomId` and `isEncrypted` values:

```js {2-3}
      <ChatRoom
        roomId={roomId}
        isEncrypted={isEncrypted}
        onMessage={msg => {
          showNotification('New message: ' + msg, isDark ? 'dark' : 'light');
        }}
      />
```

Now you can move the `createConnection` function *inside* the Effect instead of passing it down from the `App`:

```js {1-4,6,10-20}
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';

export default function ChatRoom({ roomId, isEncrypted, onMessage }) {
  const onReceiveMessage = useEffectEvent(onMessage);

  useEffect(() => {
    function createConnection() {
      const options = {
        serverUrl: 'https://localhost:1234',
        roomId: roomId
      };
      if (isEncrypted) {
        return createEncryptedConnection(options);
      } else {
        return createUnencryptedConnection(options);
      }
    }
    // ...
```

After these two changes, your Effect no longer depends on any function values:

```js {1,8,10,21}
export default function ChatRoom({ roomId, isEncrypted, onMessage }) { // Reactive values
  const onReceiveMessage = useEffectEvent(onMessage); // Not reactive

  useEffect(() => {
    function createConnection() {
      const options = {
        serverUrl: 'https://localhost:1234',
        roomId: roomId // Reading a reactive value
      };
      if (isEncrypted) { // Reading a reactive value
        return createEncryptedConnection(options);
      } else {
        return createUnencryptedConnection(options);
      }
    }

    const connection = createConnection();
    connection.on('message', (msg) => onReceiveMessage(msg));
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, isEncrypted]); // ✅ All dependencies declared
```

As a result, the chat re-connects only when something meaningful (`roomId` or `isEncrypted`) changes:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

import { showNotification } from './notifications.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [isEncrypted, setIsEncrypted] = useState(false);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <label>
        <input
          type="checkbox"
          checked={isEncrypted}
          onChange={e => setIsEncrypted(e.target.checked)}
        />
        Enable encryption
      </label>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        isEncrypted={isEncrypted}
        onMessage={msg => {
          showNotification('New message: ' + msg, isDark ? 'dark' : 'light');
        }}
      />
    </>
  );
}
```

```js ChatRoom.js active
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';

export default function ChatRoom({ roomId, isEncrypted, onMessage }) {
  const onReceiveMessage = useEffectEvent(onMessage);

  useEffect(() => {
    function createConnection() {
      const options = {
        serverUrl: 'https://localhost:1234',
        roomId: roomId
      };
      if (isEncrypted) {
        return createEncryptedConnection(options);
      } else {
        return createUnencryptedConnection(options);
      }
    }

    const connection = createConnection();
    connection.on('message', (msg) => onReceiveMessage(msg));
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, isEncrypted]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js chat.js
export function createEncryptedConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ 🔐 Connecting to "' + roomId + '" room... (encrypted)');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ 🔐 Disconnected from "' + roomId + '" room (encrypted)');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}

export function createUnencryptedConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room (unencrypted)...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room (unencrypted)');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

```js notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label, button { display: block; margin-bottom: 5px; }
```

</Sandpack>

</Solution>

</Challenges>
