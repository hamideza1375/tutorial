---
title: 'You Might Not Need an Effect'
---

<Intro>

Effects are an escape hatch from the React paradigm. They let you "step outside" of React and synchronize your components with some external system like a non-React widget, network, or the browser DOM. If there is no external system involved (for example, if you want to update a component's state when some props or state change), you shouldn't need an Effect. Removing unnecessary Effects will make your code easier to follow, faster to run, and less error-prone.
//////////////////
 Effects یک فرار از پارادایم React هستند. آنها به شما اجازه می‌دهند که از React خارج شوید و کامپوننت‌های خود را با یک سیستم خارجی مانند یک ویجت non-React، شبکه یا DOM مرورگر همگام کنید. اگر هیچ سیستم خارجی درگیر نیست (برای مثال، اگر می‌خواهید state یک کامپوننت را هنگام تغییر props یا state به‌روز کنید)، به یک Effect نیاز ندارید. حذف Effects اضافی باعث می‌شود کد شما راحت‌تر قابل پی‌گیری، سریع‌تر اجرا شود و کمتر خطا داشته باشد.

</Intro>

<YouWillLearn>

* Why and how to remove unnecessary Effects from your components
* How to cache expensive computations without Effects
* How to reset and adjust component state without Effects
* How to share logic between event handlers
* Which logic should be moved to event handlers
* How to notify parent components about changes

////////////////////
* چرا و چگونه افکت‌های اضافی را از کامپوننت‌های خود حذف کنید
* چگونه محاسبات گران قیمت را بدون استفاده از افکت‌ها کش کنید
* چگونه state کامپوننت را بدون استفاده از افکت‌ها بازنشانی و تنظیم کنید
* چگونه منطق را بین event handler ها به اشتراک بگذارید
* چه منطقی باید به event handler ها منتقل شود
* چگونه به کامپوننت‌های والد درباره تغییرات اطلاع دهید

</YouWillLearn>

## How to remove unnecessary Effects(نحوه حذف افکت های غیر ضروری) {/*how-to-remove-unnecessary-effects*/}

There are two common cases in which you don't need Effects:
دو مورد رایج وجود دارد که در آنها به افکت نیاز ندارید:

* **You don't need Effects to transform data for rendering.** For example, let's say you want to filter a list before displaying it. You might feel tempted to write an Effect that updates a state variable when the list changes. However, this is inefficient. When you update the state, React will first call your component functions to calculate what should be on the screen. Then React will ["commit"](/learn/render-and-commit) these changes to the DOM, updating the screen. Then React will run your Effects. If your Effect *also* immediately updates the state, this restarts the whole process from scratch! To avoid the unnecessary render passes, transform all the data at the top level of your components. That code will automatically re-run whenever your props or state change.
////////////////////////
ترجمه: * برای تبدیل داده‌ها برای رندر کردن، به Effects نیاز ندارید. به عنوان مثال، فرض کنید می‌خواهید قبل از نمایش لیستی آن را فیلتر کنید. ممکن است وسواس داشته باشید که یک Effect بنویسید که وقتی لیست تغییر می‌کند، یک متغیر state را به‌روز کند. با این حال، این کار ناکارآمد است. هنگامی که state را به‌روز می‌کنید، React ابتدا توابع کامپوننت شما را صدا می‌زند تا محاسبه کند که چه چیزی باید روی صفحه نمایش باشد. سپس React این تغییرات را به DOM “commit” می‌کند و صفحه را به‌روز می‌کند. سپس React Effects شما را اجرا می‌کند. اگر Effect شما همچنین state را به‌روز کند، این کار فرآیند کلی را از ابتدا شروع می‌کند! برای جلوگیری از انتقال‌های رندر اضافی، تمام داده‌ها را در سطح بالای کامپوننت‌های خود تبدیل کنید. این کد هر زمان که props یا state شما تغییر کند، به صورت خودکار دوباره اجرا می‌شود.

* **You don't need Effects to handle user events.** For example, let's say you want to send an `/api/buy` POST request and show a notification when the user buys a product. In the Buy button click event handler, you know exactly what happened. By the time an Effect runs, you don't know *what* the user did (for example, which button was clicked). This is why you'll usually handle user events in the corresponding event handlers.
///////////////////////////
ترجمه: * برای کنترل رویدادهای کاربر، به Effects نیاز ندارید. به عنوان مثال، فرض کنید می‌خواهید یک درخواست POST /api/buy را ارسال کنید و هنگام خرید کاربر، یک اعلان را نمایش دهید. در event handler دکمه Buy، دقیقاً می‌دانید چه اتفاقی افتاده است. تا زمانی که یک Effect اجرا می‌شود، شما نمی‌دانید کاربر چه کاری انجام داده است (برای مثال، کدام دکمه فشرده شده است). به همین دلیل، معمولاً رویدادهای کاربر را در event handler های مربوطه کنترل می‌کنید.

You *do* need Effects to [synchronize](/learn/synchronizing-with-effects#what-are-effects-and-how-are-they-different-from-events) with external systems. For example, you can write an Effect that keeps a jQuery widget synchronized with the React state. You can also fetch data with Effects: for example, you can synchronize the search results with the current search query. Keep in mind that modern [frameworks](/learn/start-a-new-react-project#production-grade-react-frameworks) provide more efficient built-in data fetching mechanisms than writing Effects directly in your components.
/////////////////////
برای همگام‌سازی با سیستم‌های خارجی، به Effects نیاز دارید. به عنوان مثال، می‌توانید یک Effect بنویسید که یک ویجت jQuery را با state React همگام نگه دارد. همچنین می‌توانید با Effects داده‌ها را دریافت کنید: به عنوان مثال، می‌توانید نتایج جستجو را با کوئری جستجوی فعلی همگام کنید. باید به خاطر داشته باشید که فریمورک‌های مدرن ¹ مکانیزم‌های بهتر واکشی داده داخلی واکشی داده را ارائه می‌دهند تا به جای نوشتن Effects مستقیماً در کامپوننت‌های خود، از آن‌ها استفاده کنید.

To help you gain the right intuition, let's look at some common concrete examples!
برای کمک به شما در به دست آوردن شهود مناسب، بیایید به چند نمونه عینی رایج نگاه کنیم!

### Updating state based on props or state {/*updating-state-based-on-props-or-state*/}

Suppose you have a component with two state variables: `firstName` and `lastName`. You want to calculate a `fullName` from them by concatenating them. Moreover, you'd like `fullName` to update whenever `firstName` or `lastName` change. Your first instinct might be to add a `fullName` state variable and update it in an Effect:
/////////////////
فرض کنید یک کامپوننت با دو متغیر state `firstName` و `lastName` دارید. می‌خواهید با ادغام آن‌ها، یک `fullName` بسازید. همچنین، می‌خواهید `fullName` هر زمان که `firstName` یا `lastName` تغییر کنند، به‌روز شود. اولین حس شما ممکن است این باشد که یک متغیر state `fullName` اضافه کنید و آن را در یک Effect به‌روز کنید.

```js {5-9}
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```

This is more complicated than necessary. It is inefficient too: it does an entire render pass with a stale value for `fullName`, then immediately re-renders with the updated value. Remove the state variable and the Effect:
//////////////////
این مورد از ضرورت بیشتری برخوردار است. همچنین ناکارآمد است: یک پاس رندر کامل با یک مقدار قدیمی برای `fullName` انجام می‌دهد، سپس با مقدار به‌روز شده، فوراً دوباره رندر می‌کند. متغیر state و Effect را حذف کنید.

```js {4-5}
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

**When something can be calculated from the existing props or state, [don't put it in state.](/learn/choosing-the-state-structure#avoid-redundant-state) Instead, calculate it during rendering.** This makes your code faster (you avoid the extra "cascading" updates), simpler (you remove some code), and less error-prone (you avoid bugs caused by different state variables getting out of sync with each other). If this approach feels new to you, [Thinking in React](/learn/thinking-in-react#step-3-find-the-minimal-but-complete-representation-of-ui-state) explains what should go into state.
///////////////////////////
این متن به فارسی می‌گوید: **هرگاه چیزی از props یا state موجود محاسبه شود، آن را در state قرار ندهید.** به جای آن، آن را در هنگام رندر کردن محاسبه کنید. این باعث می‌شود کد شما سریع‌تر شود (شما از بروزرسانی‌های اضافی "پیوندی" جلوگیری می‌کنید)، ساده‌تر شود (شما برخی از کدها را حذف می‌کنید) و کمتر از خطاها رنج ببرید (شما از باگ‌هایی که توسط متغیرهای state مختلف با یکدیگر هماهنگ نیستند جلوگیری می‌کنید). اگر این رویکرد برای شما جدید است، [Thinking in React](/learn/thinking-in-react#step-3-find-the-minimal-but-complete-representation-of-ui-state) توضیح می‌دهد که چه چیزی باید در state قرار داده شود.

### Caching expensive calculations {/*caching-expensive-calculations*/}

This component computes `visibleTodos` by taking the `todos` it receives by props and filtering them according to the `filter` prop. You might feel tempted to store the result in state and update it from an Effect:
////////////////////
این متن به فارسی می‌گوید: **هرگاه چیزی از props یا state موجود محاسبه شود، آن را در state قرار ندهید.** به جای آن، آن را در هنگام رندر کردن محاسبه کنید. این باعث می‌شود کد شما سریع‌تر شود (شما از بروزرسانی‌های اضافی "پیوندی" جلوگیری می‌کنید)، ساده‌تر شود (شما برخی از کدها را حذف می‌کنید) و کمتر از خطاها رنج ببرید (شما از باگ‌هایی که توسط متغیرهای state مختلف با یکدیگر هماهنگ نیستند جلوگیری می‌کنید). اگر این رویکرد برای شما جدید است، [Thinking in React](/learn/thinking-in-react#step-3-find-the-minimal-but-complete-representation-of-ui-state) توضیح می‌دهد که چه چیزی باید در state .


```js {4-8}
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [visibleTodos, setVisibleTodos] = useState([]);
  useEffect(() => {
    setVisibleTodos(getFilteredTodos(todos, filter));
  }, [todos, filter]);

  // ...
}
```

Like in the earlier example, this is both unnecessary and inefficient. First, remove the state and the Effect:
///////////////////
مانند مثال قبلی، این هم غیر ضروری و هم ناکارآمد است. ابتدا حالت و افکت را حذف کنید:


```js {3-4}
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ This is fine if getFilteredTodos() is not slow.
  const visibleTodos = getFilteredTodos(todos, filter);
  // ...
}
```

Usually, this code is fine! But maybe `getFilteredTodos()` is slow or you have a lot of `todos`. In that case you don't want to recalculate `getFilteredTodos()` if some unrelated state variable like `newTodo` has changed.
///////////////
این کد به طور معمول درست است! اما ممکن است `getFilteredTodos()` کند باشد یا شما تعداد زیادی از `todos` داشته باشید. در این صورت، اگر یک متغیر state نا مرتبط مانند `newTodo` تغییر کرده باشد، نمی‌خواهید `getFilteredTodos()` را دوباره محاسبه کنید.

You can cache (or ["memoize"](https://en.wikipedia.org/wiki/Memoization)) an expensive calculation by wrapping it in a [`useMemo`](/reference/react/useMemo) Hook:
/////////////////
می‌توانید یک محاسبه گران قیمت را با قرار دادن آن در یک [`useMemo`](/reference/react/useMemo) در حافظه پنهان (یا ["memoize"](https://en.wikipedia.org/wiki/Memoization) ذخیره کنید:

```js {5-8}
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  const visibleTodos = useMemo(() => {
    // ✅ Does not re-run unless todos or filter change
    return getFilteredTodos(todos, filter);
  }, [todos, filter]);
  // ...
}
```

Or, written as a single line:

```js {5-6}
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ Does not re-run getFilteredTodos() unless todos or filter change
  const visibleTodos = useMemo(() => getFilteredTodos(todos, filter), [todos, filter]);
  // ...
}
```

**This tells React that you don't want the inner function to re-run unless either `todos` or `filter` have changed.** React will remember the return value of `getFilteredTodos()` during the initial render. During the next renders, it will check if `todos` or `filter` are different. If they're the same as last time, `useMemo` will return the last result it has stored. But if they are different, React will call the inner function again (and store its result).
////////////////////
این دستور به React می‌گوید که تابع داخلی را مجدداً اجرا نکند مگر اینکه `todos` یا `filter` تغییر کرده باشند. React در زمان رندر اول، مقدار بازگشتی `getFilteredTodos()` را به خاطر می‌سپارد. در رندرهای بعدی، اگر `todos` یا `filter` متفاوت باشند، React بررسی می‌کند. اگر مقدار آن‌ها مانند زمان گذشته باشد، `useMemo` مقدار آخرین نتیجه را که ذخیره شده است، باز می‌گرداند. اما اگر متفاوت باشند، React تابع داخلی را مجدداً فراخوانی می‌کند (و نتیجه آن را ذخیره می‌کند).

The function you wrap in [`useMemo`](/reference/react/useMemo) runs during rendering, so this only works for [pure calculations.](/learn/keeping-components-pure)
////////////////
تابعی که در [`useMemo`](/reference/react/useMemo) می‌پیچید در حین رندر اجرا می‌شود، بنابراین این فقط برای [محاسبات خالص] کار می‌کند.(/learn/keeping-components-pure)

<DeepDive>

#### How to tell if a calculation is expensive? {/*how-to-tell-if-a-calculation-is-expensive*/}

In general, unless you're creating or looping over thousands of objects, it's probably not expensive. If you want to get more confidence, you can add a console log to measure the time spent in a piece of code:
////////////////////////
بطور کلی، مگر اینکه شما در حال ایجاد یا حلقه زدن بر روی هزاران شی باشید، احتمالاً هزینه‌بر نیست. اگر می‌خواهید اطمینان بیشتری پیدا کنید، می‌توانید یک console log اضافه کنید تا زمان صرف شده در یک قطعه کد را اندازه‌گیری کنید.

```js {1,3}
console.time('filter array');
const visibleTodos = getFilteredTodos(todos, filter);
console.timeEnd('filter array');
```

Perform the interaction you're measuring (for example, typing into the input). You will then see logs like `filter array: 0.15ms` in your console. If the overall logged time adds up to a significant amount (say, `1ms` or more), it might make sense to memoize that calculation. As an experiment, you can then wrap the calculation in `useMemo` to verify whether the total logged time has decreased for that interaction or not:
///////////////////////////
با انجام عملیاتی که قصد اندازه‌گیری آن را دارید (مثلاً تایپ در ورودی)، شما لاگ‌هایی مانند `filter array: 0.15ms` را در کنسول خود مشاهده خواهید کرد. اگر زمان کل لاگ‌ها به مقدار قابل توجهی (مثلاً `1ms` یا بیشتر) برسد، ممکن است منطقی باشد که این محاسبه را memoize کنید. به عنوان یک آزمایش، می‌توانید سپس محاسبه را در `useMemo` قرار داده و بررسی کنید که زمان کل لاگ‌ها برای آن عملیات کاهش یافته است یا خیر.

```js
console.time('filter array');
const visibleTodos = useMemo(() => {
  return getFilteredTodos(todos, filter); // Skipped if todos and filter haven't changed
}, [todos, filter]);
console.timeEnd('filter array');
```

`useMemo` won't make the *first* render faster. It only helps you skip unnecessary work on updates.

Keep in mind that your machine is probably faster than your users' so it's a good idea to test the performance with an artificial slowdown. For example, Chrome offers a [CPU Throttling](https://developer.chrome.com/blog/new-in-devtools-61/#throttling) option for this.

Also note that measuring performance in development will not give you the most accurate results. (For example, when [Strict Mode](/reference/react/StrictMode) is on, you will see each component render twice rather than once.) To get the most accurate timings, build your app for production and test it on a device like your users have.
////////////////////
`useMemo` نمی‌تواند رندر اول را سریع‌تر کند. این تنها به شما کمک می‌کند تا کارهای غیرضروری را در بروزرسانی‌ها اجرا نکنید.

در نظر داشته باشید که ممکن است کامپیوتر شما از کامپیوتر کاربران شما سریع‌تر باشد، بنابراین آزمایش کارایی با کاهش سرعت مصنوعی، یک ایده خوب است. به عنوان مثال، Chrome گزینه [CPU Throttling](https://developer.chrome.com/blog/new-in-devtools-61/#throttling) را برای این منظور ارائه می‌دهد.

همچنین توجه داشته باشید که اندازه‌گیری عملکرد در حال توسعه، نتایج دقیق‌تری را به شما نخواهد داد. (به عنوان مثال، وقتی [Strict Mode](/reference/react/StrictMode) روشن است، هر کامپوننت را دو بار به جای یک بار مشاهده خواهید کرد.) برای گرفتن زمان‌بندی دقیق‌تر، برنامه خود را برای تولید ویژه سازی کنید و آن را روی یک دستگاه مانند کاربران خود تست کنید.

</DeepDive>

### Resetting all state when a prop changes {/*resetting-all-state-when-a-prop-changes*/}

This `ProfilePage` component receives a `userId` prop. The page contains a comment input, and you use a `comment` state variable to hold its value. One day, you notice a problem: when you navigate from one profile to another, the `comment` state does not get reset. As a result, it's easy to accidentally post a comment on a wrong user's profile. To fix the issue, you want to clear out the `comment` state variable whenever the `userId` changes:
//////////////
این کامپوننت `ProfilePage` یک `prop` به نام `userId` دریافت می‌کند. این صفحه شامل یک ورودی نظر است و شما از یک متغیر `state` به نام `comment` برای نگهداری مقدار آن استفاده می‌کنید. یک روز، یک مشکل را مشاهده می‌کنید: هنگامی که از یک پروفایل به پروفایل دیگری منتقل می‌شوید، متغیر `state` `comment` بازنشانی نمی‌شود. به عبارت دیگر، آسان است که به طور تصادفی نظر خود را در پروفایل کاربر اشتباهی ارسال کنید. برای رفع این مشکل، می‌خواهید هر زمان که `userId` تغییر کرد، متغیر `state` `comment` را پاک کنید.

```js {4-7}
export default function ProfilePage({ userId }) {
  const [comment, setComment] = useState('');

  // 🔴 Avoid: Resetting state on prop change in an Effect
  useEffect(() => {
    setComment('');
  }, [userId]);
  // ...
}
```

This is inefficient because `ProfilePage` and its children will first render with the stale value, and then render again. It is also complicated because you'd need to do this in *every* component that has some state inside `ProfilePage`. For example, if the comment UI is nested, you'd want to clear out nested comment state too.

Instead, you can tell React that each user's profile is conceptually a _different_ profile by giving it an explicit key. Split your component in two and pass a `key` attribute from the outer component to the inner one:
////////////////////////
این روش نه تنها کارآیی را کاهش می‌دهد، بلکه باعث می‌شود که `ProfilePage` و فرزندانش با مقدار قدیمی رندر شوند و سپس دوباره رندر شوند. همچنین، این روش پیچیده است زیرا شما باید این کار را در *هر* کامپوننتی که دارای برخی از وضعیت‌ها در داخل `ProfilePage` است، انجام دهید. به عنوان مثال، اگر رابط کاربری نظرات تو در تو است، شما باید وضعیت نظرات تو در تو را نیز پاک کنید.

به جای این، می‌توانید به React بگویید که هر پروفایل کاربری به صورت مفهومی یک پروفایل _متفاوت_ است، با ارائه یک کلید صریح. کامپوننت خود را به دو بخش تقسیم کنید و یک ویژگی `key` را از کامپوننت بیرونی به داخلی منتقل کنید:

```js {5,11-12}
export default function ProfilePage({ userId }) {
  return (
    <Profile
      userId={userId}
      key={userId}
    />
  );
}

function Profile({ userId }) {
  // ✅ This and any other state below will reset on key change automatically
  const [comment, setComment] = useState('');
  // ...
}
```

Normally, React preserves the state when the same component is rendered in the same spot. **By passing `userId` as a `key` to the `Profile` component, you're asking React to treat two `Profile` components with different `userId` as two different components that should not share any state.** Whenever the key (which you've set to `userId`) changes, React will recreate the DOM and [reset the state](/learn/preserving-and-resetting-state#option-2-resetting-state-with-a-key) of the `Profile` component and all of its children. Now the `comment` field will clear out automatically when navigating between profiles.

Note that in this example, only the outer `ProfilePage` component is exported and visible to other files in the project. Components rendering `ProfilePage` don't need to pass the key to it: they pass `userId` as a regular prop. The fact `ProfilePage` passes it as a `key` to the inner `Profile` component is an implementation detail.
/////////////////////////
به طور معمول، React وضعیت را هنگامی که کامپوننت مشابه در همان محل رندر می‌شود، حفظ می‌کند. **با ارسال `userId` به عنوان یک `key` به کامپوننت `Profile`، شما از React خواسته‌اید که دو کامپوننت `Profile` با `userId` متفاوت را به عنوان دو کامپوننت متفاوتی که هیچ وضعیتی را به اشتراک نمی‌گذارند، در نظر بگیرد.** هر زمان که کلید (که شما آن را به `userId` تنظیم کرده‌اید) تغییر کند، React DOM را بازسازی کرده و [وضعیت](/learn/preserving-and-resetting-state#option-2-resetting-state-with-a-key) کامپوننت `Profile` و تمام فرزندانش را بازنشانی می‌کند. حالا، زمینه `comment` به طور خودکار هنگامی که بین پروفایل‌ها جابجا می‌شوید، پاک می‌شود.

توجه کنید که در این مثال، تنها کامپوننت بیرونی `ProfilePage` صادر و قابل مشاهده برای سایر فایل‌ها در پروژه است. کامپوننت‌هایی که `ProfilePage` را رندر می‌کنند، نیازی به ارسال کلید به آن ندارند: آن‌ها `userId` را به عنوان یک `prop` معمولی ارسال می‌کنند. این موضوع که `ProfilePage` آن را به عنوان یک `key` به کامپوننت داخلی `Profile` ارسال می‌کند، جزئیات پیاده‌سازی است.

### Adjusting some state when a prop changes {/*adjusting-some-state-when-a-prop-changes*/}

Sometimes, you might want to reset or adjust a part of the state on a prop change, but not all of it.

This `List` component receives a list of `items` as a prop, and maintains the selected item in the `selection` state variable. You want to reset the `selection` to `null` whenever the `items` prop receives a different array:
/////////////////////
این کامپوننت `List` یک لیست از `items` را به عنوان یک `prop` دریافت می‌کند و مورد انتخاب را در متغیر `state` `selection` نگه می‌دارد. شما می‌خواهید هر زمان که `items` یک آرایه متفاوت دریافت کند، `selection` را به `null` بازنشانی کنید.

```js {5-8}
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // 🔴 Avoid: Adjusting state on prop change in an Effect
  useEffect(() => {
    setSelection(null);
  }, [items]);
  // ...
}
```

This, too, is not ideal. Every time the `items` change, the `List` and its child components will render with a stale `selection` value at first. Then React will update the DOM and run the Effects. Finally, the `setSelection(null)` call will cause another re-render of the `List` and its child components, restarting this whole process again.

Start by deleting the Effect. Instead, adjust the state directly during rendering:
/////////////////////
این روش نیز بهینه نیست. هر زمان که `items` تغییر کند، `List` و فرزندانش با مقدار قدیمی `selection` در ابتدا رندر می‌شوند. سپس React DOM را به‌روزرسانی کرده و Effects را اجرا می‌کند. در نهایت، فراخوانی `setSelection(null)` باعث می‌شود که `List` و فرزندانش دوباره رندر شوند و این فرآیند را دوباره شروع کنند.

ابتدا با حذف Effect شروع کنید. به جای آن، وضعیت را مستقیماً در زمان رندرینگ تنظیم کنید.

```js {5-11}
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // Better: Adjust the state while rendering
  const [prevItems, setPrevItems] = useState(items);
  if (items !== prevItems) {
    setPrevItems(items);
    setSelection(null);
  }
  // ...
}
```

[Storing information from previous renders](/reference/react/useState#storing-information-from-previous-renders) like this can be hard to understand, but it’s better than updating the same state in an Effect. In the above example, `setSelection` is called directly during a render. React will re-render the `List` *immediately* after it exits with a `return` statement. React has not rendered the `List` children or updated the DOM yet, so this lets the `List` children skip rendering the stale `selection` value.
///////////////
 "ذخیره اطلاعات از رندرهای قبلی" مانند این، ممکن است سخت باشد، اما بهتر از به‌روزرسانی همان حالت در یک اثر است. در مثال بالا، `setSelection` مستقیماً در طول یک رندر فراخوانی می‌شود. React پس از خروج از یک عبارت `return`، `List` را *بلافاصله* دوباره رندر می‌کند. React هنوز فرزندان `List` را رندر نکرده یا DOM را به‌روز نکرده است، بنابراین این اجازه می‌دهد که فرزندان `List` از رندر کردن مقدار `selection` قدیمی صرف‌نظر کنند.

When you update a component during rendering, React throws away the returned JSX and immediately retries rendering. To avoid very slow cascading retries, React only lets you update the *same* component's state during a render. If you update another component's state during a render, you'll see an error. A condition like `items !== prevItems` is necessary to avoid loops. You may adjust state like this, but any other side effects (like changing the DOM or setting timeouts) should stay in event handlers or Effects to [keep components pure.](/learn/keeping-components-pure)
////////////////////////////
وقتی که شما در طول رندرینگ یک کامپوننت را به‌روزرسانی می‌کنید، React JSX بازگشتی را دور می‌اندازد و بلافاصله دوباره رندرینگ را تلاش می‌کند. برای جلوگیری از تلاش‌های پیاپی کند، React فقط به شما اجازه می‌دهد که در طول یک رندرینگ، حالت همان کامپوننت را به‌روزرسانی کنید. اگر شما در طول یک رندرینگ حالت کامپوننت دیگری را به‌روزرسانی کنید، خطایی را خواهید دید. شرطی مانند `items !== prevItems` برای جلوگیری از حلقه‌ها لازم است. شما می‌توانید حالت را به این شکل تنظیم کنید، اما هرگونه اثرات جانبی دیگر (مانند تغییر DOM یا تنظیم timeout) باید در event handlers یا Effects باشد تا [کامپوننت‌ها را پاک نگه دارید.](/learn/keeping-components-pure)

**Although this pattern is more efficient than an Effect, most components shouldn't need it either.** No matter how you do it, adjusting state based on props or other state makes your data flow more difficult to understand and debug. Always check whether you can [reset all state with a key](#resetting-all-state-when-a-prop-changes) or [calculate everything during rendering](#updating-state-based-on-props-or-state) instead. For example, instead of storing (and resetting) the selected *item*, you can store the selected *item ID:*
//////////////////////////
این الگوی برنامه نویسی نسبت به Effect کارآمدتر است، اما بیشتر کامپوننت ها به آن نیاز ندارند. هر چه روشی را برای تنظیم state بر اساس props یا state دیگر انتخاب کنید، جریان داده شما برای فهم و رفع اشکال بیشتر دشوار خواهد شد. همیشه بررسی کنید که آیا می توانید با یک کلید همه state را بازنشانی کنید یا همه چیز را در هنگام رندرینگ محاسبه کنید. به عنوان مثال، به جای ذخیره (و بازنشانی) مورد انتخاب شده، می توانید شناسه مورد انتخاب شده را ذخیره کنید.

```js {3-5}
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selectedId, setSelectedId] = useState(null);
  // ✅ Best: Calculate everything during rendering
  const selection = items.find(item => item.id === selectedId) ?? null;
  // ...
}
```

Now there is no need to "adjust" the state at all. If the item with the selected ID is in the list, it remains selected. If it's not, the `selection` calculated during rendering will be `null` because no matching item was found. This behavior is different, but arguably better because most changes to `items` preserve the selection.
//////////////////
اکنون دیگر نیازی به "تنظیم" state نیست. اگر مورد با شناسه انتخاب شده در لیست باشد، همچنان انتخاب شده خواهد ماند. اگر نباشد، `selection` محاسبه شده در هنگام رندرینگ به دلیل عدم یافتن مورد مطابق، `null` خواهد بود. این رفتار متفاوت است، اما به نظر می رسد بهتر است زیرا بیشتر تغییرات در `items` حفظ انتخاب را می کنند.

### Sharing logic between event handlers {/*sharing-logic-between-event-handlers*/}

Let's say you have a product page with two buttons (Buy and Checkout) that both let you buy that product. You want to show a notification whenever the user puts the product in the cart. Calling `showNotification()` in both buttons' click handlers feels repetitive so you might be tempted to place this logic in an Effect:
/////////////////////
بیایید بگوییم که شما یک صفحه محصول با دو دکمه (خرید و تسویه حساب) دارید که هر دو به شما اجازه می دهند آن محصول را خریداری کنید. می خواهید هر زمان که کاربر محصول را در سبد خرید قرار می دهد، یک اعلان نمایش داده شود. فراخوانی `showNotification()` در دو کلیک هندلر دکمه ها تکراری به نظر می رسد، بنابراین شما ممکن است تمایل داشته باشید که این منطق را در یک Effect قرار دهید:

```js {2-7}
function ProductPage({ product, addToCart }) {
  // 🔴 Avoid: Event-specific logic inside an Effect
  useEffect(() => {
    if (product.isInCart) {
      showNotification(`Added ${product.name} to the shopping cart!`);
    }
  }, [product]);

  function handleBuyClick() {
    addToCart(product);
  }

  function handleCheckoutClick() {
    addToCart(product);
    navigateTo('/checkout');
  }
  // ...
}
```

This Effect is unnecessary. It will also most likely cause bugs. For example, let's say that your app "remembers" the shopping cart between the page reloads. If you add a product to the cart once and refresh the page, the notification will appear again. It will keep appearing every time you refresh that product's page. This is because `product.isInCart` will already be `true` on the page load, so the Effect above will call `showNotification()`.
/////////////////
این Effect نیازی به آن نیست. همچنین احتمالا باعث بروز باگ خواهد شد. به عنوان مثال، فرض کنید که برنامه شما سبد خرید را بین بارگذاری صفحه ها "یاد می گیرد". اگر یک محصول را به سبد خرید اضافه کرده و صفحه را تازه کنید، اعلان دوباره نمایش داده می شود. هر زمان که صفحه محصول را تازه می کنید، این اعلان همچنان ظاهر خواهد شد. این به این دلیل است که `product.isInCart` در هنگام بارگذاری صفحه، در حالت `true` قرار دارد، بنابراین Effect فوق، `showNotification()` را فراخوانی خواهد کرد.

**When you're not sure whether some code should be in an Effect or in an event handler, ask yourself *why* this code needs to run. Use Effects only for code that should run *because* the component was displayed to the user.** In this example, the notification should appear because the user *pressed the button*, not because the page was displayed! Delete the Effect and put the shared logic into a function called from both event handlers:
//////////////////
ترجمه کن: **هنگامی که مطمئن نیستید کد شما باید در یک Effect یا در یک event handler قرار داشته باشد، خودتان را بپرسید که چرا این کد باید اجرا شود. از Effects فقط برای کدی استفاده کنید که باید به دلیل نمایش کامپوننت به کاربر اجرا شود.** در این مثال، اعلان باید به دلیل اینکه کاربر *دکمه را فشار داد*، نه به دلیل نمایش صفحه، نمایش داده شود! Effect را حذف کرده و منطق مشترک را در یک تابع قرار داده و از هر دو event handler فراخوانی کنید:

```js {2-6,9,13}
function ProductPage({ product, addToCart }) {
  // ✅ Good: Event-specific logic is called from event handlers
  function buyProduct() {
    addToCart(product);
    showNotification(`Added ${product.name} to the shopping cart!`);
  }

  function handleBuyClick() {
    buyProduct();
  }

  function handleCheckoutClick() {
    buyProduct();
    navigateTo('/checkout');
  }
  // ...
}
```

This both removes the unnecessary Effect and fixes the bug.
این هم افکت غیر ضروری را حذف می کند و هم باگ را برطرف می کند.

### Sending a POST request {/*sending-a-post-request*/}

This `Form` component sends two kinds of POST requests. It sends an analytics event when it mounts. When you fill in the form and click the Submit button, it will send a POST request to the `/api/register` endpoint:
/////////////////
این کامپوننت `Form` دو نوع درخواست POST ارسال می کند. زمانی که کامپوننت نمایش داده می شود، یک رویداد تجزیه و تحلیل ارسال می کند. زمانی که فرم را پر کرده و دکمه ارسال را کلیک می کنید، یک درخواست POST به نقطه پایانی `/api/register` ارسال خواهد شد.

```js {5-8,10-16}
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic should run because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  // 🔴 Avoid: Event-specific logic inside an Effect
  const [jsonToSubmit, setJsonToSubmit] = useState(null);
  useEffect(() => {
    if (jsonToSubmit !== null) {
      post('/api/register', jsonToSubmit);
    }
  }, [jsonToSubmit]);

  function handleSubmit(e) {
    e.preventDefault();
    setJsonToSubmit({ firstName, lastName });
  }
  // ...
}
```

Let's apply the same criteria as in the example before.
بیایید همان معیارهای مثال قبل را اعمال کنیم.

The analytics POST request should remain in an Effect. This is because the _reason_ to send the analytics event is that the form was displayed. (It would fire twice in development, but [see here](/learn/synchronizing-with-effects#sending-analytics) for how to deal with that.)
////////////////
**این Effect باید باقی بماند.** این به دلیل این است که _دلیل_ ارسال رویداد تجزیه و تحلیل این است که فرم نمایش داده شده است. (در حالت توسعه، دو بار فرستاده می شود، اما [اینجا](/learn/synchronizing-with-effects#sending-analytics) را برای رفع آن ببینید.)
///////////////
**این Effect نیازی به آن نیست.** این به دلیل این است که _دلیل_ ارسال درخواست POST به `/api/register` این نیست که فرم نمایش داده شده است. شما فقط می خواهید درخواست را در یک لحظه خاص از زمان ارسال کنید: زمانی که کاربر دکمه را فشار می دهد. این فقط _در آن تعامل خاص_ باید اتفاق بیفتد. Effect دوم را حذف کرده و درخواست POST را در event handler قرار دهید:

However, the `/api/register` POST request is not caused by the form being _displayed_. You only want to send the request at one specific moment in time: when the user presses the button. It should only ever happen _on that particular interaction_. Delete the second Effect and move that POST request into the event handler:
////////////////
**این Effect نیازی به آن نیست.** این به دلیل این است که _دلیل_ ارسال درخواست POST به `/api/register` این نیست که فرم نمایش داده شده است. شما فقط می خواهید درخواست را در یک لحظه خاص از زمان ارسال کنید: زمانی که کاربر دکمه را فشار می دهد. این فقط _در آن تعامل خاص_ باید اتفاق بیفتد. Effect دوم را حذف کرده و درخواست POST را در event handler قرار دهید:

```js {12-13}
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic runs because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  function handleSubmit(e) {
    e.preventDefault();
    // ✅ Good: Event-specific logic is in the event handler
    post('/api/register', { firstName, lastName });
  }
  // ...
}
```

When you choose whether to put some logic into an event handler or an Effect, the main question you need to answer is _what kind of logic_ it is from the user's perspective. If this logic is caused by a particular interaction, keep it in the event handler. If it's caused by the user _seeing_ the component on the screen, keep it in the Effect.
/////////////////
هنگامی که مطمئن نیستید کد شما باید در یک event handler یا در یک Effect قرار داشته باشد، سؤال اصلی که باید پاسخ دهید این است که از دید کاربر، این منطق چه نوعی است. اگر این منطق به دلیل یک تعامل خاص ایجاد شده است، آن را در event handler نگه دارید. اگر این منطق به دلیل دیدن کاربر کامپوننت در صفحه ایجاد شده است، آن را در Effect نگه دارید.

### Chains of computations {/*chains-of-computations*/}

Sometimes you might feel tempted to chain Effects that each adjust a piece of state based on other state:
//////////////
گاهی اوقات ممکن است وسوسه شوید که افکت‌هایی را زنجیره‌ای کنید که هر کدام یک حالت را بر اساس حالت دیگر تنظیم می‌کنند:

```js {7-29}
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);
  const [isGameOver, setIsGameOver] = useState(false);

  // 🔴 Avoid: Chains of Effects that adjust the state solely to trigger each other
  useEffect(() => {
    if (card !== null && card.gold) {
      setGoldCardCount(c => c + 1);
    }
  }, [card]);

  useEffect(() => {
    if (goldCardCount > 3) {
      setRound(r => r + 1)
      setGoldCardCount(0);
    }
  }, [goldCardCount]);

  useEffect(() => {
    if (round > 5) {
      setIsGameOver(true);
    }
  }, [round]);

  useEffect(() => {
    alert('Good game!');
  }, [isGameOver]);

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    } else {
      setCard(nextCard);
    }
  }

  // ...
```

There are two problems with this code.
این کد دو مشکل دارد.

One problem is that it is very inefficient: the component (and its children) have to re-render between each `set` call in the chain. In the example above, in the worst case (`setCard` → render → `setGoldCardCount` → render → `setRound` → render → `setIsGameOver` → render) there are three unnecessary re-renders of the tree below.
///////////////////
یکی از مشکلات آن این است که بسیار ناکارآمد است: کامپوننت (و فرزندانش) بین هر فراخوانی `set` در زنجیره باید دوباره رندر شوند. در مثال بالا، در بدترین حالت (`setCard` → render → `setGoldCardCount` → render → `setRound` → render → `setIsGameOver` → render) سه بار رندر نامناسب در زیر درخت رخ می دهد.

Even if it weren't slow, as your code evolves, you will run into cases where the "chain" you wrote doesn't fit the new requirements. Imagine you are adding a way to step through the history of the game moves. You'd do it by updating each state variable to a value from the past. However, setting the `card` state to a value from the past would trigger the Effect chain again and change the data you're showing. Such code is often rigid and fragile.
//////////////
حتی اگر کد شما کند نبود، هنگامی که کد شما تکامل می کند، با مواردی روبرو خواهید شد که "زنجیر" نوشته شده شما با نیازهای جدید سازگار نیست. تصور کنید که شما دارید یک روش برای پیمایش تاریخچه حرکات بازی اضافه می کنید. شما با به روز رسانی هر متغیر state به یک مقدار از گذشته اقدام خواهید کرد. با این حال، تنظیم state `card` به یک مقدار از گذشته، زنجیر Effect را دوباره فعال می کند و داده هایی که نمایش داده می شوند را تغییر می دهد. چنین کدی اغلب سفت و سخت و شکننده است.

In this case, it's better to calculate what you can during rendering, and adjust the state in the event handler:
/////////////
در این مورد، بهتر است آنچه را که می توانید در حین رندرینگ محاسبه کنید و وضعیت را در کنترل کننده رویداد تنظیم کنید:

```js {6-7,14-26}
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);

  // ✅ Calculate what you can during rendering
  const isGameOver = round > 5;

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    }

    // ✅ Calculate all the next state in the event handler
    setCard(nextCard);
    if (nextCard.gold) {
      if (goldCardCount <= 3) {
        setGoldCardCount(goldCardCount + 1);
      } else {
        setGoldCardCount(0);
        setRound(round + 1);
        if (round === 5) {
          alert('Good game!');
        }
      }
    }
  }

  // ...
```

This is a lot more efficient. Also, if you implement a way to view game history, now you will be able to set each state variable to a move from the past without triggering the Effect chain that adjusts every other value. If you need to reuse logic between several event handlers, you can [extract a function](#sharing-logic-between-event-handlers) and call it from those handlers.
////////////////
این روش بسیار کارآمدتر است. همچنین، اگر روشی برای مشاهده تاریخچه بازی پیاده کنید، حالا می‌توانید هر متغیر حالت را به یک حرکت از گذشته تنظیم کنید بدون اینکه زنجیره Effect را که هر مقدار دیگری را تنظیم می‌کند، فعال کنید. اگر نیاز به استفاده مجدد از منطق بین چندین دستگیره رویداد دارید، می‌توانید آن را تعریف کنید و از آن در آن دستگیره‌ها فراخوانی کنید.

Remember that inside event handlers, [state behaves like a snapshot.](/learn/state-as-a-snapshot) For example, even after you call `setRound(round + 1)`, the `round` variable will reflect the value at the time the user clicked the button. If you need to use the next value for calculations, define it manually like `const nextRound = round + 1`.
///////////////
در دستگیره‌های رویداد، [حالت مانند یک عکس العمل فوری است](/learn/state-as-a-snapshot). به عنوان مثال، حتی پس از فراخوانی `setRound(round + 1)`، متغیر `round` مقداری را که در زمان کلیک کردن کاربر بر روی دکمه داشته است، نشان می‌دهد. اگر نیاز به استفاده از مقدار بعدی برای محاسبات دارید، آن را به صورت دستی تعریف کنید، مانند `const nextRound = round + 1`.

In some cases, you *can't* calculate the next state directly in the event handler. For example, imagine a form with multiple dropdowns where the options of the next dropdown depend on the selected value of the previous dropdown. Then, a chain of Effects is appropriate because you are synchronizing with network.
//////////////////
در برخی موارد، نمی‌توانید حالت بعدی را به صورت مستقیم در دستگیره رویداد محاسبه کنید. به عنوان مثال، تصور کنید یک فرم با چندین کشویی دارید که گزینه‌های کشویی بعدی به مقدار انتخاب شده کشویی قبلی وابسته هستند. در این صورت، زنجیره‌ای از Effects مناسب است زیرا با شبکه همگام می‌شوید.

### Initializing the application {/*initializing-the-application*/}

Some logic should only run once when the app loads.
برخی از منطق ها باید فقط یک بار در هنگام بارگیری برنامه اجرا شوند.

You might be tempted to place it in an Effect in the top-level component:
ممکن است وسوسه شوید که آن را در یک افکت در جزء سطح بالا قرار دهید:

```js {2-6}
function App() {
  // 🔴 Avoid: Effects with logic that should only ever run once
  useEffect(() => {
    loadDataFromLocalStorage();
    checkAuthToken();
  }, []);
  // ...
}
```

However, you'll quickly discover that it [runs twice in development.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development) This can cause issues--for example, maybe it invalidates the authentication token because the function wasn't designed to be called twice. In general, your components should be resilient to being remounted. This includes your top-level `App` component.
//////////////
با این حال، به سرعت خواهید فهمید که [در حالت توسعه دو بار اجرا می‌شود](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development). این ممکن است مشکلاتی را ایجاد کند - به عنوان مثال، شاید باعث نامعتبر شدن توکن احراز هویت شود زیرا تابع برای دو بار فراخوانی طراحی نشده است. در کل، کامپوننت‌های شما باید قابلیت اجرای مجدد را داشته باشند. این شامل کامپوننت `App` بالاترین سطح شما نیز می‌شود.

Although it may not ever get remounted in practice in production, following the same constraints in all components makes it easier to move and reuse code. If some logic must run *once per app load* rather than *once per component mount*, add a top-level variable to track whether it has already executed:
///////////////
اگرچه در عمل در محیط تولید ممکن است هرگز دوباره نصب نشود، اما پیروی از محدودیت‌های یکسان در تمام کامپوننت‌ها، انتقال و استفاده مجدد کد را آسان‌تر می‌کند. اگر برخی منطق باید *یک بار در هر بار بارگذاری برنامه* اجرا شود به جای *یک بار در هر بار نصب کامپوننت*، یک متغیر بالاسطح برای پیگیری اینکه آیا قبلاً اجرا شده است، اضافه کنید:

```js {1,5-6,10}
let didInit = false;

function App() {
  useEffect(() => {
    if (!didInit) {
      didInit = true;
      // ✅ Only runs once per app load
      loadDataFromLocalStorage();
      checkAuthToken();
    }
  }, []);
  // ...
}
```

You can also run it during module initialization and before the app renders:
همچنین می توانید آن را در حین اولیه سازی ماژول و قبل از رندر شدن برنامه اجرا کنید:

```js {1,5}
if (typeof window !== 'undefined') { // Check if we're running in the browser.
   // ✅ Only runs once per app load
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```

Code at the top level runs once when your component is imported--even if it doesn't end up being rendered. To avoid slowdown or surprising behavior when importing arbitrary components, don't overuse this pattern. Keep app-wide initialization logic to root component modules like `App.js` or in your application's entry point.
///////////////
کد در سطح بالا یک بار هنگام وارد کردن کامپوننت شما اجرا می‌شود - حتی اگر به رندر شدن نرسید. برای جلوگیری از کاهش سرعت یا رفتار غیرمنتظره هنگام وارد کردن کامپوننت‌های دلخواه، از این الگویی که بیش از حد استفاده نکنید، استفاده کنید. منطق مقدماتی برنامه را در ماژول‌های کامپوننت ریشه مانند `App.js` یا در نقطه ورود برنامه خود قرار دهید.

### Notifying parent components about state changes {/*notifying-parent-components-about-state-changes*/}

Let's say you're writing a `Toggle` component with an internal `isOn` state which can be either `true` or `false`. There are a few different ways to toggle it (by clicking or dragging). You want to notify the parent component whenever the `Toggle` internal state changes, so you expose an `onChange` event and call it from an Effect:
///////////////
بیایید فرض کنیم که شما در حال نوشتن یک کامپوننت `Toggle` با یک حالت داخلی `isOn` هستید که می‌تواند `true` یا `false` باشد. چندین روش مختلف برای تغییر آن وجود دارد (با کلیک کردن یا کشیدن). شما می‌خواهید هر زمان که حالت داخلی `Toggle` تغییر کند، کامپوننت والد را مطلع کنید، بنابراین یک رویداد `onChange` را ارائه می‌دهید و آن را از یک Effect فراخوانی می‌کنید:

```js {4-7}
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  // 🔴 Avoid: The onChange handler runs too late
  useEffect(() => {
    onChange(isOn);
  }, [isOn, onChange])

  function handleClick() {
    setIsOn(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      setIsOn(true);
    } else {
      setIsOn(false);
    }
  }

  // ...
}
```

Like earlier, this is not ideal. The `Toggle` updates its state first, and React updates the screen. Then React runs the Effect, which calls the `onChange` function passed from a parent component. Now the parent component will update its own state, starting another render pass. It would be better to do everything in a single pass.
//////////////
مانند قبل، این روش ایده‌آل نیست. `Toggle` ابتدا حالت خود را به‌روزرسانی می‌کند و سپس React صفحه را به‌روزرسانی می‌کند. سپس React Effect را اجرا می‌کند که تابع `onChange` را از کامپوننت والد فراخوانی می‌کند. حالا کامپوننت والد حالت خود را به‌روزرسانی می‌کند و یک پاس رندر جدید را شروع می‌کند. بهتر است همه چیز را در یک پاس انجام دهید..

Delete the Effect and instead update the state of *both* components within the same event handler:
افکت را حذف کنید و به جای آن وضعیت *دو* مؤلفه را در یک رویداد کنترل کننده به روز کنید:

```js {5-7,11,16,18}
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  function updateToggle(nextIsOn) {
    // ✅ Good: Perform all updates during the event that caused them
    setIsOn(nextIsOn);
    onChange(nextIsOn);
  }

  function handleClick() {
    updateToggle(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      updateToggle(true);
    } else {
      updateToggle(false);
    }
  }

  // ...
}
```

With this approach, both the `Toggle` component and its parent component update their state during the event. React [batches updates](/learn/queueing-a-series-of-state-updates) from different components together, so there will only be one render pass.

You might also be able to remove the state altogether, and instead receive `isOn` from the parent component:
///////////////////
با این روش، هر دو کامپوننت `Toggle` و کامپوننت والد خود را در طول رویداد به‌روزرسانی می‌کنند. React [به‌روزرسانی‌ها را با هم ترکیب می‌کند](/learn/queueing-a-series-of-state-updates)، بنابراین تنها یک پاس رندر وجود خواهد داشت.

همچنین ممکن است بتوانید حالت را به طور کامل حذف کنید و به جای آن `isOn` را از کامپوننت والد دریافت کنید:

```js {1,2}
// ✅ Also good: the component is fully controlled by its parent
function Toggle({ isOn, onChange }) {
  function handleClick() {
    onChange(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      onChange(true);
    } else {
      onChange(false);
    }
  }

  // ...
}
```

["Lifting state up"](/learn/sharing-state-between-components) lets the parent component fully control the `Toggle` by toggling the parent's own state. This means the parent component will have to contain more logic, but there will be less state overall to worry about. Whenever you try to keep two different state variables synchronized, try lifting state up instead!
////////////////
با استفاده از روش "lifting state up"، کامپوننت والد می‌تواند با کنترل کامل بر روی حالت `Toggle`، حالت خود را تغییر دهد. این بدان معنی است که کامپوننت والد باید شامل منطق بیشتری باشد، اما در کل کمتری نگران حالت باشید. هر زمان که سعی می‌کنید دو متغیر حالت متفاوت را همگام کنید، به جای آن، از روش lifting state up استفاده کنید!

### Passing data to the parent {/*passing-data-to-the-parent*/}

This `Child` component fetches some data and then passes it to the `Parent` component in an Effect:
///////////////
این مؤلفه «فرزند» برخی از داده‌ها را واکشی می‌کند و سپس آن را به مؤلفه «والد» در یک افکت ارسال می‌کند:

```js {9-14}
function Parent() {
  const [data, setData] = useState(null);
  // ...
  return <Child onFetched={setData} />;
}

function Child({ onFetched }) {
  const data = useSomeAPI();
  // 🔴 Avoid: Passing data to the parent in an Effect
  useEffect(() => {
    if (data) {
      onFetched(data);
    }
  }, [onFetched, data]);
  // ...
}
```

In React, data flows from the parent components to their children. When you see something wrong on the screen, you can trace where the information comes from by going up the component chain until you find which component passes the wrong prop or has the wrong state. When child components update the state of their parent components in Effects, the data flow becomes very difficult to trace. Since both the child and the parent need the same data, let the parent component fetch that data, and *pass it down* to the child instead:
//////////////////
در React، داده‌ها از کامپوننت‌های والد به فرزندانشان جریان دارند. هنگامی که چیزی را در صفحه نمایش مشاهده می‌کنید که اشتباه است، می‌توانید با پیگیری زنجیره کامپوننت به بالا، پیدا کنید که کدام کامپوننت پراپ اشتباه را منتقل کرده است یا حالت اشتباهی دارد. هنگامی که کامپوننت‌های فرزند حالت کامپوننت والد خود را در Effects به‌روزرسانی می‌کنند، جریان داده بسیار سخت برای پیگیری می‌شود. از آنجایی که هر دو کامپوننت فرزند و والد نیاز به داده‌های یکسان دارند، بهتر است که کامپوننت والد داده را دریافت کند و به جای آن، آن را به کامپوننت فرزند *منتقل کند*.

```js {4-5}
function Parent() {
  const data = useSomeAPI();
  // ...
  // ✅ Good: Passing data down to the child
  return <Child data={data} />;
}

function Child({ data }) {
  // ...
}
```

This is simpler and keeps the data flow predictable: the data flows down from the parent to the child.
////////////////
این ساده‌تر است و جریان داده را قابل پیش‌بینی نگه می‌دارد: داده‌ها از والدین به فرزند منتقل می‌شوند.

### Subscribing to an external store {/*subscribing-to-an-external-store*/}

Sometimes, your components may need to subscribe to some data outside of the React state. This data could be from a third-party library or a built-in browser API. Since this data can change without React's knowledge, you need to manually subscribe your components to it. This is often done with an Effect, for example:
/////////////////////////////////////
گاهی اوقات، کامپوننت‌های شما ممکن است نیاز داشته باشند به برخی از داده‌های خارج از حالت React مشترک شوند. این داده‌ها ممکن است از یک کتابخانه شخص ثالث یا یک API داخلی مرورگر باشند. از آنجایی که این داده‌ها می‌توانند بدون دانش React تغییر کنند، شما باید به صورت دستی کامپوننت‌های خود را به آن مشترک کنید. این کار معمولاً با یک Effect انجام می‌شود، به عنوان مثال:.

```js {2-17}
function useOnlineStatus() {
  // Not ideal: Manual store subscription in an Effect
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function updateState() {
      setIsOnline(navigator.onLine);
    }

    updateState();

    window.addEventListener('online', updateState);
    window.addEventListener('offline', updateState);
    return () => {
      window.removeEventListener('online', updateState);
      window.removeEventListener('offline', updateState);
    };
  }, []);
  return isOnline;
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```

Here, the component subscribes to an external data store (in this case, the browser `navigator.onLine` API). Since this API does not exist on the server (so it can't be used for the initial HTML), initially the state is set to `true`. Whenever the value of that data store changes in the browser, the component updates its state.
///////////////////
در اینجا، کامپوننت به یک فروشگاه داده خارجی (در این مورد API `navigator.onLine` مرورگر) مشترک می‌شود. از آنجایی که این API در سرور وجود ندارد (بنابراین نمی‌توان آن را برای HTML اولیه استفاده کرد)، در ابتدا حالت به `true` تنظیم شده است. هر زمان که مقدار آن فروشگاه داده در مرورگر تغییر کند، کامپوننت حالت خود را به‌روزرسانی می‌کند.

Although it's common to use Effects for this, React has a purpose-built Hook for subscribing to an external store that is preferred instead. Delete the Effect and replace it with a call to [`useSyncExternalStore`](/reference/react/useSyncExternalStore):
////////////////////////
اگرچه استفاده از Effects برای این کار رایج است، React یک Hook ساخته شده برای مشترک شدن با فروشگاه خارجی دارد که به جای آن ترجیح داده می‌شود. Effect را حذف کرده و آن را با فراخوانی [`useSyncExternalStore`](/reference/react/useSyncExternalStore) جایگزین کنید:

```js {11-16}
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function useOnlineStatus() {
  // ✅ Good: Subscribing to an external store with a built-in Hook
  return useSyncExternalStore(
    subscribe, // React won't resubscribe for as long as you pass the same function
    () => navigator.onLine, // How to get the value on the client
    () => true // How to get the value on the server
  );
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```

This approach is less error-prone than manually syncing mutable data to React state with an Effect. Typically, you'll write a custom Hook like `useOnlineStatus()` above so that you don't need to repeat this code in the individual components. [Read more about subscribing to external stores from React components.](/reference/react/useSyncExternalStore)
////////////////////
این روش کمتر از دست دادن خطا نسبت به همگام سازی دستی داده‌های قابل تغییر با حالت React با یک Effect است. معمولاً یک Hook سفارشی مانند `useOnlineStatus()` را می‌نویسید تا نیازی به تکرار این کد در کامپوننت‌های جداگانه نداشته باشید. [بیشتر در مورد مشترک شدن با فروشگاه‌های خارجی از کامپوننت‌های React بخوانید.](/reference/react/useSyncExternalStore)

### Fetching data {/*fetching-data*/}

Many apps use Effects to kick off data fetching. It is quite common to write a data fetching Effect like this:
///////////////////
بسیاری از برنامه ها از جلوه ها برای شروع واکشی داده ها استفاده می کنند. بسیار معمول است که یک افکت واکشی داده مانند این بنویسید:

```js {5-10}
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);

  useEffect(() => {
    // 🔴 Avoid: Fetching without cleanup logic
    fetchResults(query, page).then(json => {
      setResults(json);
    });
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```

You *don't* need to move this fetch to an event handler.
شما *نیازی ندارید* این واکشی را به یک کنترل کننده رویداد منتقل کنید.

This might seem like a contradiction with the earlier examples where you needed to put the logic into the event handlers! However, consider that it's not *the typing event* that's the main reason to fetch. Search inputs are often prepopulated from the URL, and the user might navigate Back and Forward without touching the input.
///////////////////
این ممکن است با مثال‌های قبلی که باید منطق را در دستگیره‌های رویداد قرار دهید، تضاد داشته باشد! با این حال، در نظر داشته باشید که *رویداد تایپ* دلیل اصلی برای بازیابی نیست. ورودی‌های جستجو اغلب از URL پیش‌فرض شده پر شده‌اند و کاربر ممکن است بدون لمس ورودی، به عقب و به جلو بروید.

It doesn't matter where `page` and `query` come from. While this component is visible, you want to keep `results` [synchronized](/learn/synchronizing-with-effects) with data from the network for the current `page` and `query`. This is why it's an Effect.
////////////////////
به هر حال، مهم نیست که `page` و `query` از کجا بیایند. در حالی که این کامپوننت قابل مشاهده است، می‌خواهید `results` را با داده‌های شبکه برای `page` و `query` فعلی [همگام‌سازی](/learn/synchronizing-with-effects) کنید. به همین دلیل، این یک Effect است.

However, the code above has a bug. Imagine you type `"hello"` fast. Then the `query` will change from `"h"`, to `"he"`, `"hel"`, `"hell"`, and `"hello"`. This will kick off separate fetches, but there is no guarantee about which order the responses will arrive in. For example, the `"hell"` response may arrive *after* the `"hello"` response. Since it will call `setResults()` last, you will be displaying the wrong search results. This is called a ["race condition"](https://en.wikipedia.org/wiki/Race_condition): two different requests "raced" against each other and came in a different order than you expected.
//////////////
با این حال، کد بالا دارای یک باگ است. تصور کنید که به سرعت `"hello"` را تایپ می‌کنید. سپس `query` از `"h"` به `"he"`، `"hel"`، `"hell"` و `"hello"` تغییر می‌کند. این باعث شروع درخواست‌های جداگانه می‌شود، اما هیچ تضمینی در مورد ترتیب رسیدن پاسخ‌ها وجود ندارد. به عنوان مثال، پاسخ `"hell"` ممکن است *پس از* پاسخ `"hello"` دریافت شود. از آنجایی که `setResults()` آخرین بار فراخوانی می‌شود، نتایج جستجوی نادرستی را نمایش می‌دهید. این یک ["شرط رقابتی"](https://en.wikipedia.org/wiki/Race_condition) نامیده می‌شود: دو درخواست متفاوت با یکدیگر "رقابت" کرده و به ترتیبی متفاوت از آنچه انتظار داشتید وارد می‌شوند.

**To fix the race condition, you need to [add a cleanup function](/learn/synchronizing-with-effects#fetching-data) to ignore stale responses:**
//////////////
برای رفع شرایط مسابقه، باید یک تابع پاکسازی اضافه کنید تا پاسخ‌های قدیمی را نادیده بگیرید¹.

```js {5,7,9,11-13}
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);
  useEffect(() => {
    let ignore = false;
    fetchResults(query, page).then(json => {
      if (!ignore) {
        setResults(json);
      }
    });
    return () => {
      ignore = true;
    };
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```

This ensures that when your Effect fetches data, all responses except the last requested one will be ignored.
//////////////////
این تضمین می‌کند که وقتی Effect شما داده‌ها را واکشی می‌کند، همه پاسخ‌ها به جز آخرین پاسخ درخواستی نادیده گرفته می‌شوند.

Handling race conditions is not the only difficulty with implementing data fetching. You might also want to think about caching responses (so that the user can click Back and see the previous screen instantly), how to fetch data on the server (so that the initial server-rendered HTML contains the fetched content instead of a spinner), and how to avoid network waterfalls (so that a child can fetch data without waiting for every parent).
///////////////////
همگام‌سازی شرایط مسابقه تنها مشکلی نیست که با پیاده‌سازی بازیابی داده‌ها روبرو می‌شوید. شما ممکن است همچنین بخواهید در مورد ذخیره‌سازی پاسخ‌ها (تا کاربر بتواند برگشت کرده و صفحه قبلی را به صورت فوری مشاهده کند)، چگونگی بازیابی داده‌ها در سرور (تا HTML سرور-رندر شده اولیه حاوی محتوای بازیابی شده باشد به جای یک چرخشگر) و چگونگی جلوگیری از شلال‌های شبکه (تا یک فرزند بتواند داده را بدون انتظار برای هر والدی بازیابی کند) فکر کنید.

**These issues apply to any UI library, not just React. Solving them is not trivial, which is why modern [frameworks](/learn/start-a-new-react-project#production-grade-react-frameworks) provide more efficient built-in data fetching mechanisms than fetching data in Effects.**
//////////////////
این مسائل برای هر کتابخانه UI، نه تنها React، اعمال می‌شود. حل آن‌ها به راحتی انجام نمی‌شود، به همین دلیل [فریم‌ورک‌های](/learn/start-a-new-react-project#production-grade-react-frameworks) مدرن، مکانیزم‌های بازیابی داده داخلی کارآمدتری را نسبت به بازیابی داده در Effects فراهم می‌کنند.

If you don't use a framework (and don't want to build your own) but would like to make data fetching from Effects more ergonomic, consider extracting your fetching logic into a custom Hook like in this example:
///////////////////////
اگر از چارچوبی استفاده نمی‌کنید (و نمی‌خواهید چارچوب خود را بسازید) اما می‌خواهید واکشی داده‌ها از Effects را ارگونومیک‌تر کنید، منطق واکشی خود را در یک Hook سفارشی استخراج کنید مانند این مثال:
////////////////////////
اگر از چارچوبی استفاده نمی‌کنید (و نمی‌خواهید خودتان را بسازید) ولی می‌خواهید بازیابی داده‌ها از Effects راحت‌تر شود، در نظر داشته باشید که منطق بازیابی خود را در یک Hook سفارشی مانند مثال زیر استخراج کنید¹:
```jsx
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(async () => {
    const response = await fetch(url);
    const data = await response.json();
    setData(data);
    setLoading(false);
  }, [url]);

  return { loading, data };
}
```

```js {4}
function SearchResults({ query }) {
  const [page, setPage] = useState(1);
  const params = new URLSearchParams({ query, page });
  const results = useData(`/api/search?${params}`);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}

function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(url)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setData(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [url]);
  return data;
}
```

You'll likely also want to add some logic for error handling and to track whether the content is loading. You can build a Hook like this yourself or use one of the many solutions already available in the React ecosystem. **Although this alone won't be as efficient as using a framework's built-in data fetching mechanism, moving the data fetching logic into a custom Hook will make it easier to adopt an efficient data fetching strategy later.**
////////////////
شما احتمالاً می‌خواهید برای پیگیری خطا و ردیابی بارگیری محتوا، برخی از منطق را اضافه کنید. می‌توانید یک Hook مانند این را خودتان بسازید یا از یکی از راه‌حل‌های موجود در اکوسیستم React استفاده کنید. **اگرچه این تنها به تنهایی به عنوان مکانیزم بازیابی داده داخلی چارچوب کارآمد نخواهد بود، اما جابجایی منطق بازیابی داده به یک Hook سفارشی، به شما کمک می‌کند تا در آینده به راحتی یک استراتژی بازیابی داده کارآمد را اتخاذ کنید.**.

In general, whenever you have to resort to writing Effects, keep an eye out for when you can extract a piece of functionality into a custom Hook with a more declarative and purpose-built API like `useData` above. The fewer raw `useEffect` calls you have in your components, the easier you will find to maintain your application.
///////////////
بطور کلی، هر زمان که باید به نوشتن Effects روی آیتم‌های مختلفی از کامپوننت‌ها بازگردید، به دنبال آن باشید که آیا می‌توانید یک قطعه از عملکرد را به یک Hook سفارشی با API معرفی شده و مستقیم‌تر مانند `useData` در بالا منتقل کنید. هر چه تعداد فراخوانی‌های خام `useEffect` کمتر باشد، پیدا کردن راه‌حل‌های بهتر برای حفظ برنامه شما آسان‌تر خواهد بود.

<Recap>

- If you can calculate something during render, you don't need an Effect.
- To cache expensive calculations, add `useMemo` instead of `useEffect`.
- To reset the state of an entire component tree, pass a different `key` to it.
- To reset a particular bit of state in response to a prop change, set it during rendering.
- Code that runs because a component was *displayed* should be in Effects, the rest should be in events.
- If you need to update the state of several components, it's better to do it during a single event.
- Whenever you try to synchronize state variables in different components, consider lifting state up.
- You can fetch data with Effects, but you need to implement cleanup to avoid race conditions.

//////////////
ترجمه این متن به زبان فارسی به شرح زیر است:
- اگر بتوانید چیزی را در طول رندر محاسبه کنید، نیازی به Effect ندارید.
- برای ذخیره محاسبات گران قیمت، به جای `useEffect` از `useMemo` استفاده کنید.
- برای بازنشانی حالت یک درخت کامپوننت کامل، یک `key` متفاوت به آن منتقل کنید.
- برای بازنشانی یک بیت خاص از حالت در پاسخ به تغییر prop، آن را در طول رندرگیری تنظیم کنید.
- کدی که به دلیل نمایش یک کامپوننت اجرا می‌شود، باید در Effects باشد، بقیه باید در رویدادها باشد.
- اگر نیاز به به‌روزرسانی حالت چندین کامپوننت دارید، بهتر است آن را در یک رویداد واحد انجام دهید.
- هر زمان که سعی می‌کنید متغیرهای حالت را در کامپوننت‌های مختلف همگام کنید، در نظر داشته باشید که state را به بالا ببرید.
- می‌توانید با Effects داده را بازیابی کنید، اما باید پاکسازی را پیاده‌سازی کنید تا شرایط مسابقه را از بین ببرید.

</Recap>

<Challenges>

#### Transform data without Effects {/*transform-data-without-effects*/}

The `TodoList` below displays a list of todos. When the "Show only active todos" checkbox is ticked, completed todos are not displayed in the list. Regardless of which todos are visible, the footer displays the count of todos that are not yet completed.

Simplify this component by removing all the unnecessary state and Effects.

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { initialTodos, createTodo } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [activeTodos, setActiveTodos] = useState([]);
  const [visibleTodos, setVisibleTodos] = useState([]);
  const [footer, setFooter] = useState(null);

  useEffect(() => {
    setActiveTodos(todos.filter(todo => !todo.completed));
  }, [todos]);

  useEffect(() => {
    setVisibleTodos(showActive ? activeTodos : todos);
  }, [showActive, todos, activeTodos]);

  useEffect(() => {
    setFooter(
      <footer>
        {activeTodos.length} todos left
      </footer>
    );
  }, [activeTodos]);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
      {footer}
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

```js todos.js
let nextId = 0;

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

<Hint>

If you can calculate something during rendering, you don't need state or an Effect that updates it.

</Hint>

<Solution>

There are only two essential pieces of state in this example: the list of `todos` and the `showActive` state variable which represents whether the checkbox is ticked. All of the other state variables are [redundant](/learn/choosing-the-state-structure#avoid-redundant-state) and can be calculated during rendering instead. This includes the `footer` which you can move directly into the surrounding JSX.

Your result should end up looking like this:

<Sandpack>

```js
import { useState } from 'react';
import { initialTodos, createTodo } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
      <footer>
        {activeTodos.length} todos left
      </footer>
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

```js todos.js
let nextId = 0;

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

</Solution>

#### Cache a calculation without Effects {/*cache-a-calculation-without-effects*/}

In this example, filtering the todos was extracted into a separate function called `getVisibleTodos()`. This function contains a `console.log()` call inside of it which helps you notice when it's being called. Toggle "Show only active todos" and notice that it causes `getVisibleTodos()` to re-run. This is expected because visible todos change when you toggle which ones to display.

Your task is to remove the Effect that recomputes the `visibleTodos` list in the `TodoList` component. However, you need to make sure that `getVisibleTodos()` does *not* re-run (and so does not print any logs) when you type into the input.

<Hint>

One solution is to add a `useMemo` call to cache the visible todos. There is also another, less obvious solution.

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [text, setText] = useState('');
  const [visibleTodos, setVisibleTodos] = useState([]);

  useEffect(() => {
    setVisibleTodos(getVisibleTodos(todos, showActive));
  }, [todos, showActive]);

  function handleAddClick() {
    setText('');
    setTodos([...todos, createTodo(text)]);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

```js todos.js
let nextId = 0;
let calls = 0;

export function getVisibleTodos(todos, showActive) {
  console.log(`getVisibleTodos() was called ${++calls} times`);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;
  return visibleTodos;
}

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

<Solution>

Remove the state variable and the Effect, and instead add a `useMemo` call to cache the result of calling `getVisibleTodos()`:

<Sandpack>

```js
import { useState, useMemo } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [text, setText] = useState('');
  const visibleTodos = useMemo(
    () => getVisibleTodos(todos, showActive),
    [todos, showActive]
  );

  function handleAddClick() {
    setText('');
    setTodos([...todos, createTodo(text)]);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

```js todos.js
let nextId = 0;
let calls = 0;

export function getVisibleTodos(todos, showActive) {
  console.log(`getVisibleTodos() was called ${++calls} times`);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;
  return visibleTodos;
}

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

With this change, `getVisibleTodos()` will be called only if `todos` or `showActive` change. Typing into the input only changes the `text` state variable, so it does not trigger a call to `getVisibleTodos()`.

There is also another solution which does not need `useMemo`. Since the `text` state variable can't possibly affect the list of todos, you can extract the `NewTodo` form into a separate component, and move the `text` state variable inside of it:

<Sandpack>

```js
import { useState, useMemo } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const visibleTodos = getVisibleTodos(todos, showActive);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

```js todos.js
let nextId = 0;
let calls = 0;

export function getVisibleTodos(todos, showActive) {
  console.log(`getVisibleTodos() was called ${++calls} times`);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;
  return visibleTodos;
}

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

This approach satisfies the requirements too. When you type into the input, only the `text` state variable updates. Since the `text` state variable is in the child `NewTodo` component, the parent `TodoList` component won't get re-rendered. This is why `getVisibleTodos()` doesn't get called when you type. (It would still be called if the `TodoList` re-renders for another reason.)

</Solution>

#### Reset state without Effects {/*reset-state-without-effects*/}

This `EditContact` component receives a contact object shaped like `{ id, name, email }` as the `savedContact` prop. Try editing the name and email input fields. When you press Save, the contact's button above the form updates to the edited name. When you press Reset, any pending changes in the form are discarded. Play around with this UI to get a feel for it.

When you select a contact with the buttons at the top, the form resets to reflect that contact's details. This is done with an Effect inside `EditContact.js`. Remove this Effect. Find another way to reset the form when `savedContact.id` changes.

<Sandpack>

```js App.js hidden
import { useState } from 'react';
import ContactList from './ContactList.js';
import EditContact from './EditContact.js';

export default function ContactManager() {
  const [
    contacts,
    setContacts
  ] = useState(initialContacts);
  const [
    selectedId,
    setSelectedId
  ] = useState(0);
  const selectedContact = contacts.find(c =>
    c.id === selectedId
  );

  function handleSave(updatedData) {
    const nextContacts = contacts.map(c => {
      if (c.id === updatedData.id) {
        return updatedData;
      } else {
        return c;
      }
    });
    setContacts(nextContacts);
  }

  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={selectedId}
        onSelect={id => setSelectedId(id)}
      />
      <hr />
      <EditContact
        savedContact={selectedContact}
        onSave={handleSave}
      />
    </div>
  )
}

const initialContacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js ContactList.js hidden
export default function ContactList({
  contacts,
  selectedId,
  onSelect
}) {
  return (
    <section>
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact.id);
            }}>
              {contact.id === selectedId ?
                <b>{contact.name}</b> :
                contact.name
              }
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js EditContact.js active
import { useState, useEffect } from 'react';

export default function EditContact({ savedContact, onSave }) {
  const [name, setName] = useState(savedContact.name);
  const [email, setEmail] = useState(savedContact.email);

  useEffect(() => {
    setName(savedContact.name);
    setEmail(savedContact.email);
  }, [savedContact]);

  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: savedContact.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(savedContact.name);
        setEmail(savedContact.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li { display: inline-block; }
li button {
  padding: 10px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

<Hint>

It would be nice if there was a way to tell React that when `savedContact.id` is different, the `EditContact` form is conceptually a _different contact's form_ and should not preserve state. Do you recall any such way?

</Hint>

<Solution>

Split the `EditContact` component in two. Move all the form state into the inner `EditForm` component. Export the outer `EditContact` component, and make it pass `savedContact.id` as the `key` to the inner `EditForm` component. As a result, the inner `EditForm` component resets all of the form state and recreates the DOM whenever you select a different contact.

<Sandpack>

```js App.js hidden
import { useState } from 'react';
import ContactList from './ContactList.js';
import EditContact from './EditContact.js';

export default function ContactManager() {
  const [
    contacts,
    setContacts
  ] = useState(initialContacts);
  const [
    selectedId,
    setSelectedId
  ] = useState(0);
  const selectedContact = contacts.find(c =>
    c.id === selectedId
  );

  function handleSave(updatedData) {
    const nextContacts = contacts.map(c => {
      if (c.id === updatedData.id) {
        return updatedData;
      } else {
        return c;
      }
    });
    setContacts(nextContacts);
  }

  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={selectedId}
        onSelect={id => setSelectedId(id)}
      />
      <hr />
      <EditContact
        savedContact={selectedContact}
        onSave={handleSave}
      />
    </div>
  )
}

const initialContacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js ContactList.js hidden
export default function ContactList({
  contacts,
  selectedId,
  onSelect
}) {
  return (
    <section>
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact.id);
            }}>
              {contact.id === selectedId ?
                <b>{contact.name}</b> :
                contact.name
              }
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js EditContact.js active
import { useState } from 'react';

export default function EditContact(props) {
  return (
    <EditForm
      {...props}
      key={props.savedContact.id}
    />
  );
}

function EditForm({ savedContact, onSave }) {
  const [name, setName] = useState(savedContact.name);
  const [email, setEmail] = useState(savedContact.email);

  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: savedContact.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(savedContact.name);
        setEmail(savedContact.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li { display: inline-block; }
li button {
  padding: 10px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

</Solution>

#### Submit a form without Effects {/*submit-a-form-without-effects*/}

This `Form` component lets you send a message to a friend. When you submit the form, the `showForm` state variable is set to `false`. This triggers an Effect calling `sendMessage(message)`, which sends the message (you can see it in the console). After the message is sent, you see a "Thank you" dialog with an "Open chat" button that lets you get back to the form.

Your app's users are sending way too many messages. To make chatting a little bit more difficult, you've decided to show the "Thank you" dialog *first* rather than the form. Change the `showForm` state variable to initialize to `false` instead of `true`. As soon as you make that change, the console will show that an empty message was sent. Something in this logic is wrong!

What's the root cause of this problem? And how can you fix it?

<Hint>

Should the message be sent _because_ the user saw the "Thank you" dialog? Or is it the other way around?

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Form() {
  const [showForm, setShowForm] = useState(true);
  const [message, setMessage] = useState('');

  useEffect(() => {
    if (!showForm) {
      sendMessage(message);
    }
  }, [showForm, message]);

  function handleSubmit(e) {
    e.preventDefault();
    setShowForm(false);
  }

  if (!showForm) {
    return (
      <>
        <h1>Thanks for using our services!</h1>
        <button onClick={() => {
          setMessage('');
          setShowForm(true);
        }}>
          Open chat
        </button>
      </>
    );
  }

  return (
    <form onSubmit={handleSubmit}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit" disabled={message === ''}>
        Send
      </button>
    </form>
  );
}

function sendMessage(message) {
  console.log('Sending message: ' + message);
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

<Solution>

The `showForm` state variable determines whether to show the form or the "Thank you" dialog. However, you aren't sending the message because the "Thank you" dialog was _displayed_. You want to send the message because the user has _submitted the form._ Delete the misleading Effect and move the `sendMessage` call inside the `handleSubmit` event handler:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Form() {
  const [showForm, setShowForm] = useState(true);
  const [message, setMessage] = useState('');

  function handleSubmit(e) {
    e.preventDefault();
    setShowForm(false);
    sendMessage(message);
  }

  if (!showForm) {
    return (
      <>
        <h1>Thanks for using our services!</h1>
        <button onClick={() => {
          setMessage('');
          setShowForm(true);
        }}>
          Open chat
        </button>
      </>
    );
  }

  return (
    <form onSubmit={handleSubmit}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit" disabled={message === ''}>
        Send
      </button>
    </form>
  );
}

function sendMessage(message) {
  console.log('Sending message: ' + message);
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

Notice how in this version, only _submitting the form_ (which is an event) causes the message to be sent. It works equally well regardless of whether `showForm` is initially set to `true` or `false`. (Set it to `false` and notice no extra console messages.)

</Solution>

</Challenges>
