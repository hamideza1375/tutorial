---
title: 'Reusing Logic with Custom Hooks'
---

<Intro>

React comes with several built-in Hooks like `useState`, `useContext`, and `useEffect`. Sometimes, you'll wish that there was a Hook for some more specific purpose: for example, to fetch data, to keep track of whether the user is online, or to connect to a chat room. You might not find these Hooks in React, but you can create your own Hooks for your application's needs.
////////////////
React با چندین Hook داخلی مانند useState، useContext و useEffect همراه است. گاهی اوقات، شما ممکن است بخواهید یک Hook برای هدف خاصی مانند دریافت داده، پیگیری وضعیت آنلاین بودن کاربر یا اتصال به یک اتاق چت داشته باشید. شاید این Hook ها را در React پیدا نکنید، اما می توانید Hook های خود را برای نیازهای برنامه خود ایجاد کنید..

</Intro>

<YouWillLearn>

- What custom Hooks are, and how to write your own
- How to reuse logic between components
- How to name and structure your custom Hooks
- When and why to extract custom Hooks

///////////////
- Hook های سفارشی چیستند و چگونه می توانید خودتان آنها را بنویسید.
- چگونه می توانید منطق را بین کامپوننت ها باز استفاده کنید.
- چگونه می توانید Hook های سفارشی خود را نامگذاری و سازماندهی کنید.
- زمانی که و چرا باید Hook های سفارشی را استخراج کنید.

</YouWillLearn>

## Custom Hooks: Sharing logic between components {/*custom-hooks-sharing-logic-between-components*/}

Imagine you're developing an app that heavily relies on the network (as most apps do). You want to warn the user if their network connection has accidentally gone off while they were using your app. How would you go about it? It seems like you'll need two things in your component:

1. A piece of state that tracks whether the network is online.
2. An Effect that subscribes to the global [`online`](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event) and [`offline`](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event) events, and updates that state.

This will keep your component [synchronized](/learn/synchronizing-with-effects) with the network status. You might start with something like this:

///////////////////
تصور کنید که شما در حال توسعه یک برنامه هستید که به طور شدیدی به شبکه وابسته است (همانطور که بیشتر برنامه ها هستند). شما می خواهید کاربر را هشدار دهید اگر اتصال شبکه آنها به طور تصادفی قطع شده است در حالی که از برنامه شما استفاده می کردند. چگونه می توانید این کار را انجام دهید؟ به نظر می رسد که شما به دو چیز در کامپوننت خود نیاز دارید:

1. یک قطعه از state که وضعیت آنلاین بودن شبکه را پیگیری می کند.
2. یک Effect که به رویدادهای [`online`](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event) و [`offline`](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event) جهانی مشترک می شود و وضعیت را به روز می کند.

این کار باعث می شود که کامپوننت شما با وضعیت شبکه [همگام شود](/learn/synchronizing-with-effects). شما می توانید با چیزی شبیه به این شروع کنید:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

</Sandpack>

Try turning your network on and off, and notice how this `StatusBar` updates in response to your actions.

Now imagine you *also* want to use the same logic in a different component. You want to implement a Save button that will become disabled and show "Reconnecting..." instead of "Save" while the network is off.

To start, you can copy and paste the `isOnline` state and the Effect into `SaveButton`:

تلاش کنید شبکه خود را روشن و خاموش کنید و توجه کنید که این `StatusBar` به عملکرد شما واکنش نشان می دهد.

حال تصور کنید که شما *همچنین* می خواهید از همان منطق در یک کامپوننت دیگر استفاده کنید. شما می خواهید یک دکمه ذخیره سازی پیاده سازی کنید که در حالی که شبکه خاموش است، غیرفعال شده و به جای "ذخیره"، "در حال بازیابی..." را نشان می دهد.

برای شروع، می توانید حالت `isOnline` و افکت را در `SaveButton` کپی و جایگذاری کنید:
<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

</Sandpack>

Verify that, if you turn off the network, the button will change its appearance.

These two components work fine, but the duplication in logic between them is unfortunate. It seems like even though they have different *visual appearance,* you want to reuse the logic between them.
/////////////////
تلاش کنید شبکه خود را روشن و خاموش کنید و توجه کنید که این `StatusBar` به عملکرد شما واکنش نشان می دهد.

حال تصور کنید که شما *همچنین* می خواهید از همان منطق در یک کامپوننت دیگر استفاده کنید. شما می خواهید یک دکمه ذخیره سازی پیاده سازی کنید که در حالی که شبکه خاموش است، غیرفعال شده و به جای "ذخیره"، "در حال بازیابی..." را نشان می دهد.

### Extracting your own custom Hook from a component {/*extracting-your-own-custom-hook-from-a-component*/}

Imagine for a moment that, similar to [`useState`](/reference/react/useState) and [`useEffect`](/reference/react/useEffect), there was a built-in `useOnlineStatus` Hook. Then both of these components could be simplified and you could remove the duplication between them:
//////////////
تصور کنید که برای لحظه ای مانند [`useState`](/reference/react/useState) و [`useEffect`](/reference/react/useEffect)، یک Hook داخلی به نام `useOnlineStatus` وجود داشت. در این صورت، هر دوی این کامپوننت ها می توانستند ساده شوند و شما می توانید تکرار منطق بین آنها را حذف کنید:

```js {2,7}
function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

Although there is no such built-in Hook, you can write it yourself. Declare a function called `useOnlineStatus` and move all the duplicated code into it from the components you wrote earlier:
///////////////
ترجمه این متن به فارسی به شرح زیر است:
اگرچه چنین Hook داخلی وجود ندارد، می توانید آن را خودتان بنویسید. یک تابع به نام `useOnlineStatus` تعریف کنید و کد تکراری را از کامپوننت هایی که قبلاً نوشته اید، به آن منتقل کنید.

```js {2-16}
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

At the end of the function, return `isOnline`. This lets your components read that value:
در پایان تابع، «isOnline» را برگردانید. این به components شما اجازه می‌دهد آن مقدار را بخوانند:

<Sandpack>

```js
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```

```js useOnlineStatus.js
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

</Sandpack>

Verify that switching the network on and off updates both components.

Now your components don't have as much repetitive logic. **More importantly, the code inside them describes *what they want to do* (use the online status!) rather than *how to do it* (by subscribing to the browser events).**

When you extract logic into custom Hooks, you can hide the gnarly details of how you deal with some external system or a browser API. The code of your components expresses your intent, not the implementation.
///////////////////////////////
ترجمه این متن به فارسی به شرح زیر است:
تلاش کنید شبکه خود را روشن و خاموش کنید و توجه کنید که این دو کامپوننت به روزرسانی می شوند.

حالا کامپوننت های شما دیگر منطق تکراری زیادی ندارند. **به طور مهمتر، کد درون آنها *چه کاری را می خواهند انجام دهند* (استفاده از وضعیت آنلاین!) بیان می کند، نه *چگونه آن را انجام دهید* (با عضویت در رویدادهای مرورگر).**

زمانی که منطق را به Hook های سفارشی استخراج می کنید، می توانید جزئیات پیچیده از اینکه چگونه با یک سیستم خارجی یا API مرورگر برخورد می کنید را پنهان کنید. کد کامپوننت های شما قصد شما را بیان می کند، نه پیاده سازی آن.

### Hook names always start with `use` {/*hook-names-always-start-with-use*/}

React applications are built from components. Components are built from Hooks, whether built-in or custom. You'll likely often use custom Hooks created by others, but occasionally you might write one yourself!

You must follow these naming conventions:

1. **React component names must start with a capital letter,** like `StatusBar` and `SaveButton`. React components also need to return something that React knows how to display, like a piece of JSX.
2. **Hook names must start with `use` followed by a capital letter,** like [`useState`](/reference/react/useState) (built-in) or `useOnlineStatus` (custom, like earlier on the page). Hooks may return arbitrary values.

This convention guarantees that you can always look at a component and know where its state, Effects, and other React features might "hide". For example, if you see a `getColor()` function call inside your component, you can be sure that it can't possibly contain React state inside because its name doesn't start with `use`. However, a function call like `useOnlineStatus()` will most likely contain calls to other Hooks inside!
///////////////
برنامه های React از کامپوننت ها ساخته می شوند. کامپوننت ها از Hook ها ساخته می شوند، بدون توجه به اینکه آنها داخلی یا سفارشی باشند. شما احتمالاً از Hook های سفارشی ایجاد شده توسط دیگران بیشتر استفاده خواهید کرد، اما گاهی اوقات ممکن است خودتان یک Hook بنویسید!

شما باید این قوانین نامگذاری را رعایت کنید:

1. **نام کامپوننت های React باید با حرف بزرگ شروع شود**، مانند `StatusBar` و `SaveButton`. کامپوننت های React همچنین باید چیزی را که React می داند چگونه نمایش داده شود، مانند یک قطعه JSX، برگردانند.
2. **نام Hook ها باید با `use` و حرف بزرگ شروع شود**، مانند [`useState`](/reference/react/useState) (داخلی) یا `useOnlineStatus` (سفارشی، مانند صفحه قبل). Hook ها می توانند مقادیر خود را به صورت دلخواه برگردانند.

این قانون تضمین می کند که همیشه می توانید به یک کامپوننت نگاه کنید و بدانید کجای state آن، Effects و سایر ویژگی های React ممکن است "پنهان" شوند. به عنوان مثال، اگر یک تماس تابع `getColor()` در داخل کامپوننت خود مشاهده کردید، می توانید مطمئن باشید که نمی تواند شامل state React باشد زیرا نام آن با `use` شروع نمی شود. با این حال، یک تماس تابع مانند `useOnlineStatus()` احتمالاً شامل تماس هایی با Hook های دیگر در داخل خود خواهد بود!

<Note>

If your linter is [configured for React,](/learn/editor-setup#linting) it will enforce this naming convention. Scroll up to the sandbox above and rename `useOnlineStatus` to `getOnlineStatus`. Notice that the linter won't allow you to call `useState` or `useEffect` inside of it anymore. Only Hooks and components can call other Hooks!
//////////////
اگر لینتر شما برای React [پیکربندی شده باشد،](/learn/editor-setup#linting) این قانون نامگذاری را اعمال خواهد کرد. به بالا اسکرول کنید و `useOnlineStatus` را به `getOnlineStatus` تغییر نام دهید. توجه کنید که لینتر دیگر به شما اجازه استفاده از `useState` یا `useEffect` در داخل آن را نمی دهد. فقط Hook ها و کامپوننت ها می توانند تماس با Hook های دیگر بگیرند!

</Note>

<DeepDive>

#### Should all functions called during rendering start with the use prefix? {/*should-all-functions-called-during-rendering-start-with-the-use-prefix*/}

No. Functions that don't *call* Hooks don't need to *be* Hooks.

If your function doesn't call any Hooks, avoid the `use` prefix. Instead, write it as a regular function *without* the `use` prefix. For example, `useSorted` below doesn't call Hooks, so call it `getSorted` instead:
///////////////
نه. توابعی که هیچ *Hook* را *فراخوانی* نمی کنند، نیازی به *بودن* *Hook* ندارند.

اگر تابع شما هیچ *Hook* را فراخوانی نمی کند، از پیشوند `use` خودداری کنید. به جای آن، آن را به عنوان یک تابع معمولی *بدون* پیشوند `use` بنویسید. به عنوان مثال، `useSorted` در زیر هیچ *Hook* را فراخوانی نمی کند، بنابراین به جای آن آن را `getSorted` نامگذاری کنید:

```js
// 🔴 Avoid: A Hook that doesn't use Hooks
function useSorted(items) {
  return items.slice().sort();
}

// ✅ Good: A regular function that doesn't use Hooks
function getSorted(items) {
  return items.slice().sort();
}
```

This ensures that your code can call this regular function anywhere, including conditions:
این تضمین می‌کند که کد شما می‌تواند این تابع معمولی را در هر مکانی فراخوانی کند، از جمله شرایط:

```js
function List({ items, shouldSort }) {
  let displayedItems = items;
  if (shouldSort) {
    // ✅ It's ok to call getSorted() conditionally because it's not a Hook
    displayedItems = getSorted(items);
  }
  // ...
}
```

You should give `use` prefix to a function (and thus make it a Hook) if it uses at least one Hook inside of it:
//////////
اگر تابعی از حداقل یک هوک در داخل آن استفاده می کند، باید پیشوند «use» را به یک تابع بدهید (و بنابراین آن را Hook کنید):

```js
// ✅ Good: A Hook that uses other Hooks
function useAuth() {
  return useContext(Auth);
}
```

Technically, this isn't enforced by React. In principle, you could make a Hook that doesn't call other Hooks. This is often confusing and limiting so it's best to avoid that pattern. However, there may be rare cases where it is helpful. For example, maybe your function doesn't use any Hooks right now, but you plan to add some Hook calls to it in the future. Then it makes sense to name it with the `use` prefix:
/////////////
ترجمه این متن به فارسی به شرح زیر است:
فنی، React این قانون نامگذاری را اعمال نمی کند. در اصل، می توانید یک Hook بسازید که دیگر Hook ها را فراخوانی نمی کند. این اغلب گیج کننده و محدود کننده است، بنابراین بهتر است این الگو را اجتناب کنید. با این حال، ممکن است موار
```js {3-4}
// ✅ Good: A Hook that will likely use some other Hooks later
function useAuth() {
  // TODO: Replace with this line when authentication is implemented:
  // return useContext(Auth);
  return TEST_USER;
}
```

Then components won't be able to call it conditionally. This will become important when you actually add Hook calls inside. If you don't plan to use Hooks inside it (now or later), don't make it a Hook.
////////////
این به صورت رسمی توسط React اعمال نمی شود. در اصل، می توانید یک Hook بسازید که دیگر Hook ها را فراخوانی نمی کند. این اغلب گیج کننده و محدود کننده است، بنابراین بهتر است این الگو را اجتناب کنید. با این حال، ممکن است موارد نادری وجود داشته باشد که مفید باشد. به عنوان مثال، شاید تابع شما در حال حاضر از هیچ Hookی استفاده نمی کند، اما در آینده قصد دارید برخی از فراخوانی های Hook را به آن اضافه کنید. در این صورت، منطقی است که آن را با پیشوند `use` نامگذاری کنید:

“سپس کامپوننت‌ها نمی‌توانند به صورت شرطی آن را فراخوانی کنند. این موضوع زمانی مهم می‌شود که شما درون آن فراخوانی‌های Hook را اضافه کنید. اگر قصد ندارید درون آن Hook را استفاده کنید (حالا یا در آینده)، آن را Hook نکنید.”

</DeepDive>

### Custom Hooks let you share stateful logic, not state itself {/*custom-hooks-let-you-share-stateful-logic-not-state-itself*/}

In the earlier example, when you turned the network on and off, both components updated together. However, it's wrong to think that a single `isOnline` state variable is shared between them. Look at this code:
/////////
در مثال قبلی، هنگامی که شبکه را روشن و خاموش کردید، هر دو کامپوننت به‌روز شدند. با این حال، اشتباه است که فکر کنید یک متغیر وضعیت `isOnline` به صورت مشترک بین آن‌ها به اشتراک گذاشته شده است. به کد زیر نگاه کنید:
```js {2,7}
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}
```

It works the same way as before you extracted the duplication:
به همان روشی که قبل از استخراج تکراری عمل می کند:

```js {2-5,10-13}
function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}

function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}
```

These are two completely independent state variables and Effects! They happened to have the same value at the same time because you synchronized them with the same external value (whether the network is on).
/////////////
"این دو متغیر وضعیت و اثر کاملاً مستقل هستند! آن‌ها به صورت تصادفی در یک زمان مشابه به دلیل همگام‌سازی با یک مقدار خارجی (آیا شبکه روشن است یا خاموش) مقدار یکسانی داشتند."

To better illustrate this, we'll need a different example. Consider this `Form` component:
برای نشان دادن بهتر این موضوع، به مثال دیگری نیاز داریم. این component `Form` را در نظر بگیرید:
<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [firstName, setFirstName] = useState('Mary');
  const [lastName, setLastName] = useState('Poppins');

  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
  }

  function handleLastNameChange(e) {
    setLastName(e.target.value);
  }

  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={handleFirstNameChange} />
      </label>
      <label>
        Last name:
        <input value={lastName} onChange={handleLastNameChange} />
      </label>
      <p><b>Good morning, {firstName} {lastName}.</b></p>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 10px; }
```

</Sandpack>

There's some repetitive logic for each form field:

1. There's a piece of state (`firstName` and `lastName`).
1. There's a change handler (`handleFirstNameChange` and `handleLastNameChange`).
1. There's a piece of JSX that specifies the `value` and `onChange` attributes for that input.
/////////////
"برای هر فیلد فرم، منطق تکراری وجود دارد:

1. یک قطعه وضعیت (`firstName` و `lastName`) وجود دارد.
2. یک دستگیره تغییر (`handleFirstNameChange` و `handleLastNameChange`) وجود دارد.
3. یک قطعه JSX وجود دارد که ویژگی‌های `value` و `onChange` را برای آن ورودی مشخص می‌کند."

You can extract the repetitive logic into this `useFormInput` custom Hook:
می‌توانید منطق تکراری را در این هوک سفارشی `useFormInput` استخراج کنید:

<Sandpack>

```js
import { useFormInput } from './useFormInput.js';

export default function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');

  return (
    <>
      <label>
        First name:
        <input {...firstNameProps} />
      </label>
      <label>
        Last name:
        <input {...lastNameProps} />
      </label>
      <p><b>Good morning, {firstNameProps.value} {lastNameProps.value}.</b></p>
    </>
  );
}
```

```js useFormInput.js active
import { useState } from 'react';

export function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  const inputProps = {
    value: value,
    onChange: handleChange
  };

  return inputProps;
}
```

```css
label { display: block; }
input { margin-left: 10px; }
```

</Sandpack>

Notice that it only declares *one* state variable called `value`.
"توجه کنید که فقط یک متغیر state به نام `value` تعریف شده است."

However, the `Form` component calls `useFormInput` *two times:*
با این حال، کامپوننت `Form` دو بار `useFormInput` را فراخوانی می‌کند:*

```js
function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');
  // ...
```

This is why it works like declaring two separate state variables!

**Custom Hooks let you share *stateful logic* but not *state itself.* Each call to a Hook is completely independent from every other call to the same Hook.** This is why the two sandboxes above are completely equivalent. If you'd like, scroll back up and compare them. The behavior before and after extracting a custom Hook is identical.
///////////////
"این دلیلی است که آن به شکل تعریف دو متغیر state جداگانه کار می‌کند!

**Custom Hooks به شما اجازه می‌دهند تا *منطق stateful* را به اشتراک بگذارید، اما نه *خود state.* هر فراخوانی به یک Hook به طور کاملاً مستقل از هر فراخوانی دیگر به همان Hook است.** به همین دلیل دو محیط بالا به طور کامل معادل هم هستند. اگر مایل هستید، به بالا برگردید و آن‌ها را مقایسه کنید. رفتار قبل و بعد از استخراج یک Custom Hook یکسان است."

When you need to share the state itself between multiple components, [lift it up and pass it down](/learn/sharing-state-between-components) instead.
هنگامی که نیاز دارید خود state را بین چندین components به اشتراک بگذارید، به جای آن [آن را بالا ببرید و پایین بیاورید] (/learn/sharing-state-between-components).

## Passing reactive values between Hooks {/*passing-reactive-values-between-hooks*/}

The code inside your custom Hooks will re-run during every re-render of your component. This is why, like components, custom Hooks [need to be pure.](/learn/keeping-components-pure) Think of custom Hooks' code as part of your component's body!
////////////
کد داخل Hooks سفارشی شما در طول هر رندر مجدد component شما دوباره اجرا می شود. به همین دلیل است که مانند کامپوننت ها، هوک های سفارشی [باید خالص باشند.](/learn/keeping-components-pure) به کد Hooks سفارشی به عنوان بخشی از بدنه component خود فکر کنید!
/////////////
متن بالا به فارسی می‌گوید: **کد داخل هوک های سفارشی شما در هر بازنشانی مجدد کامپوننت شما اجرا می‌شود. به همین دلیل، همانند کامپوننت ها، هوک های سفارشی نیاز به بودن خالص دارند. کد هوک های سفارشی را به عنوان بخشی از بدنه کامپوننت خود تصور کنید!**¹¹: [اینجا](https://react.dev/learn/keeping-components-pure) می‌توانید بیشتر در مورد این موضوع بخوانید.

Because custom Hooks re-render together with your component, they always receive the latest props and state. To see what this means, consider this chat room example. Change the server URL or the chat room:
////////////////////
متن بالا به فارسی می‌گوید: **زیرا هوک های سفارشی همراه با کامپوننت شما مجدداً بازنشانی می‌شوند، همیشه آخرین props و state را دریافت می‌کنند. برای دیدن این موضوع، به عنوان مثال، به این چت روم فکر کنید. آدرس سرور یا اتاق چت را تغییر دهید:**

¹: [اینجا](https://react.dev/learn/reusing-logic-with-custom-hooks) می‌توانید بیشتر در مورد این موضوع بخوانید.

<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

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
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js ChatRoom.js active
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
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
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
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
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
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

export function showNotification(message, theme = 'dark') {
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

```json package.json hidden
{
  "dependencies": {
    "react": "latest",
    "react-dom": "latest",
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

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

When you change `serverUrl` or `roomId`, the Effect ["reacts" to your changes](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) and re-synchronizes. You can tell by the console messages that the chat re-connects every time that you change your Effect's dependencies.
////////////////////
متن بالا به فارسی می‌گوید: **زمانی که شما `serverUrl` یا `roomId` را تغییر می‌دهید، اثر به تغییرات شما "واکنش می‌دهد" و دوباره همگام می‌شود. می‌توانید با پیام‌های کنسول متوجه شوید که هر بار که شما وابستگی‌های اثر خود را تغییر می‌دهید، چت دوباره متصل می‌شود.**¹¹: ["reacts" to your changes](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) می‌توانید بیشتر در مورد این موضوع بخوانید.

Now move the Effect's code into a custom Hook:
اکنون کد افکت را به یک هوک سفارشی منتقل کنید:

```js {2-13}
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

This lets your `ChatRoom` component call your custom Hook without worrying about how it works inside:
////////////
این به کامپوننت ChatRoom شما اجازه می‌دهد تا Hook سفارشی شما را بدون نگرانی در مورد نحوه عملکرد آن در داخل صدا کند:

```js {4-7}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

This looks much simpler! (But it does the same thing.)
این خیلی ساده تر به نظر می رسد! (اما همین کار را می کند.)

Notice that the logic *still responds* to prop and state changes. Try editing the server URL or the selected room:
///////////////
توجه داشته باشید که منطق *هنوز* به تغییرات پروپ و state پاسخ می دهد. URL سرور یا اتاق انتخاب شده را ویرایش کنید:
//////////////
متن بالا به فارسی می‌گوید: **توجه کنید که منطق هنوز به تغییرات prop و state پاسخ می‌دهد. سعی کنید آدرس سرور یا اتاق انتخاب شده را ویرایش کنید.**
<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

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
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js ChatRoom.js active
import { useState } from 'react';
import { useChatRoom } from './useChatRoom.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

```js useChatRoom.js
import { useEffect } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
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
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
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
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
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

export function showNotification(message, theme = 'dark') {
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

```json package.json hidden
{
  "dependencies": {
    "react": "latest",
    "react-dom": "latest",
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

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Notice how you're taking the return value of one Hook:
توجه کنید که شما در حال گرفتن مقدار بازگشتی یک هوک هستید.

```js {2}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
  // ...
```

and pass it as an input to another Hook:
و آن را به عنوان ورودی به هوک دیگری ارسال کنید:

```js {6}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
  // ...
```

Every time your `ChatRoom` component re-renders, it passes the latest `roomId` and `serverUrl` to your Hook. This is why your Effect re-connects to the chat whenever their values are different after a re-render. (If you ever worked with audio or video processing software, chaining Hooks like this might remind you of chaining visual or audio effects. It's as if the output of `useState` "feeds into" the input of the `useChatRoom`.)
////////////////
هر بار که کامپوننت `ChatRoom` شما دوباره رندر می‌شود، آخرین `roomId` و `serverUrl` را به هوک شما منتقل می‌کند. به همین دلیل، هر زمان که مقادیر آن‌ها پس از رندر مجدد متفاوت باشند، Effect شما به چت متصل می‌شود. (اگر تا به حال با نرم‌افزارهای پردازش صوتی یا تصویری کار کرده‌اید، زنجیره کردن هوک‌هایی مانند این ممکن است به یاد زنجیره کردن افکت‌های صوتی یا تصویری بیفتاند. انگار خروجی `useState` ورودی `useChatRoom` را "تغذیه می‌کند".)

### Passing event handlers to custom Hooks {/*passing-event-handlers-to-custom-hooks*/}

<Wip>

This section describes an **experimental API that has not yet been released** in a stable version of React.
/////////////
این بخش یک **API آزمایشی است که هنوز در نسخه پایدار React منتشر نشده است**.

</Wip>

As you start using `useChatRoom` in more components, you might want to let components customize its behavior. For example, currently, the logic for what to do when a message arrives is hardcoded inside the Hook:
/////////////////
هر چه بیشتر از `useChatRoom` در کامپوننت‌های دیگر استفاده می‌کنید، ممکن است بخواهید به کامپوننت‌ها اجازه دهید رفتار آن‌ها را سفارشی کنند. به عنوان مثال، در حال حاضر، منطق برای انجام کاری که پیامی دریافت شده است، درون هوک به صورت سخت‌کد شده است.

```js {9-11}
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

Let's say you want to move this logic back to your component:
فرض کنید می خواهید این منطق را به component خود برگردانید:

```js {7-9}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });
  // ...
```

To make this work, change your custom Hook to take `onReceiveMessage` as one of its named options:
///////////
برای اینکه این کار کند، هوک سفارشی خود را به گونه‌ای تغییر دهید که `onReceiveMessage` را به عنوان یکی از گزینه‌های نام‌گذاری شده خود بپذیرد.

```js {1,10,13}
export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onReceiveMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl, onReceiveMessage]); // ✅ All dependencies declared
}
```

This will work, but there's one more improvement you can do when your custom Hook accepts event handlers.
//////////////
این کار خواهد کرد، اما هنگامی که هوک سفارشی شما دستگیری رویدادها را قبول می‌کند، یک بهبود دیگر وجود دارد که می‌توانید انجام دهید.

Adding a dependency on `onReceiveMessage` is not ideal because it will cause the chat to re-connect every time the component re-renders. [Wrap this event handler into an Effect Event to remove it from the dependencies:](/learn/removing-effect-dependencies#wrapping-an-event-handler-from-the-props)
///////////////
افزودن وابستگی به `onReceiveMessage` ایده‌آل نیست زیرا هر بار که کامپوننت دوباره رندر می‌شود، باعث می‌شود چت هر بار که مقدار آن‌ها پس از رندر مجدد متفاوت باشد، دوباره متصل شود. [این دستگیری رویداد را در یک رویداد اثر بسته بندی کنید تا آن را از وابستگی‌ها حذف کنید:](/learn/removing-effect-dependencies#wrapping-an-event-handler-from-the-props)

```js {1,4,5,15,18}
import { useEffect, useEffectEvent } from 'react';
// ...

export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  const onMessage = useEffectEvent(onReceiveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ All dependencies declared
}
```

Now the chat won't re-connect every time that the `ChatRoom` component re-renders. Here is a fully working demo of passing an event handler to a custom Hook that you can play with:
////////////
اکنون هر بار که کامپوننت «ChatRoom» دوباره کانکت می‌شود، گپ دوباره وصل نمی‌شود. در اینجا یک نسخه نمایشی کاملاً کارآمد از انتقال یک رویداد کنترل کننده به یک Hook سفارشی است که می توانید آن را اجرا کنید:

<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

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
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js ChatRoom.js active
import { useState } from 'react';
import { useChatRoom } from './useChatRoom.js';
import { showNotification } from './notifications.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

```js useChatRoom.js
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection } from './chat.js';

export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  const onMessage = useEffectEvent(onReceiveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
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
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
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
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
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

export function showNotification(message, theme = 'dark') {
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

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Notice how you no longer need to know *how* `useChatRoom` works in order to use it. You could add it to any other component, pass any other options, and it would work the same way. That's the power of custom Hooks.
//////////
 "توجه کنید که دیگر نیازی به دانستن چگونگی کاربرد `useChatRoom` برای استفاده از آن نیست. شما می‌توانید آن را به هر کامپوننت دیگری اضافه کنید، هر گزینه‌ای را منتقل کنید و همانطور کار خواهد کرد. این قدرت Hook های سفارشی است."

## When to use custom Hooks {/*when-to-use-custom-hooks*/}

You don't need to extract a custom Hook for every little duplicated bit of code. Some duplication is fine. For example, extracting a `useFormInput` Hook to wrap a single `useState` call like earlier is probably unnecessary.
//////////////
ترجمه: شما نیازی به استخراج یک Custom Hook برای هر بخش کوچک تکراری از کد ندارید. برخی تکرارها قابل قبول هستند. به عنوان مثال، استخراج یک Custom Hook به نام `useFormInput` برای پوشش یک `useState` تکی مانند قبلی، احتمالاً غیرضروری است.

However, whenever you write an Effect, consider whether it would be clearer to also wrap it in a custom Hook. [You shouldn't need Effects very often,](/learn/you-might-not-need-an-effect) so if you're writing one, it means that you need to "step outside React" to synchronize with some external system or to do something that React doesn't have a built-in API for. Wrapping it into a custom Hook lets you precisely communicate your intent and how the data flows through it.
//////////////
ترجمه: با این حال، هر زمان که یک افکت می‌نویسید، باید در نظر داشته باشید که آیا بهتر نیست آن را هم در یک Custom Hook قرار دهید. [شما باید از افکت‌ها بسیار کم استفاده کنید،](/learn/you-might-not-need-an-effect) بنابراین اگر یکی را می‌نویسید، به این معنی است که باید "از React خارج شوید" تا با یک سیستم خارجی همگام شوید یا کاری را انجام دهید که React برای آن API داخلی ندارد. قرار دادن آن در یک Custom Hook به شما اجازه می‌دهد تا قصد خود را و چگونگی جریان داده‌ها را به دقت ارتباط دهید.

For example, consider a `ShippingForm` component that displays two dropdowns: one shows the list of cities, and another shows the list of areas in the selected city. You might start with some code that looks like this:
////////////////
 به عنوان مثال، یک کامپوننت `ShippingForm` را در نظر بگیرید که دو منوی کشویی را نمایش می‌دهد: یکی نشانی لیست شهرها و دیگری لیست مناطق در شهر انتخابی. ممکن است با کدی شروع کنید که شبیه به این باشد.


```js {3-16,20-35}
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  // This Effect fetches cities for a country
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
  }, [country]);

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  // This Effect fetches areas for the selected city
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
  }, [city]);

  // ...
```

Although this code is quite repetitive, [it's correct to keep these Effects separate from each other.](/learn/removing-effect-dependencies#is-your-effect-doing-several-unrelated-things) They synchronize two different things, so you shouldn't merge them into one Effect. Instead, you can simplify the `ShippingForm` component above by extracting the common logic between them into your own `useData` Hook:
////////////////
 "اگرچه این کد بسیار تکراری است، [صحیح است که این افکت‌ها را جدا از یکدیگر نگه دارید.](/learn/removing-effect-dependencies#is-your-effect-doing-several-unrelated-things) آن‌ها دو چیز متفاوت را همگام می‌کنند، بنابراین نباید آن‌ها را در یک افکت ترکیب کنید. به جای این کار، می‌توانید کامپوننت `ShippingForm` را با استخراج منطق مشترک بین آن‌ها به یک Custom Hook به نام `useData` خودتان ساده‌تر کنید."


```js {2-18}
function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    if (url) {
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
    }
  }, [url]);
  return data;
}
```

Now you can replace both Effects in the `ShippingForm` components with calls to `useData`:
"اکنون می‌توانید هر دو افکت را در کامپوننت‌های ShippingForm با فراخوانی useData جایگزین کنید."

```js {2,4}
function ShippingForm({ country }) {
  const cities = useData(`/api/cities?country=${country}`);
  const [city, setCity] = useState(null);
  const areas = useData(city ? `/api/areas?city=${city}` : null);
  // ...
```

Extracting a custom Hook makes the data flow explicit. You feed the `url` in and you get the `data` out. By "hiding" your Effect inside `useData`, you also prevent someone working on the `ShippingForm` component from adding [unnecessary dependencies](/learn/removing-effect-dependencies) to it. With time, most of your app's Effects will be in custom Hooks.
///////////////
"استخراج یک Custom Hook جریان داده را صریح می‌کند. شما url را وارد می‌کنید و data را دریافت می‌کنید. با "مخفی" کردن افکت خود در useData، همچنین جلوگیری می‌کنید که فردی که در حال کار بر روی کامپوننت ShippingForm است، وابستگی‌های غیرضروری را به آن اضافه کند. با گذشت زمان، بیشتر افکت‌های برنامه‌ی شما در Custom Hook ها قرار خواهند گرفت."

<DeepDive>

#### Keep your custom Hooks focused on concrete high-level use cases {/*keep-your-custom-hooks-focused-on-concrete-high-level-use-cases*/}

Start by choosing your custom Hook's name. If you struggle to pick a clear name, it might mean that your Effect is too coupled to the rest of your component's logic, and is not yet ready to be extracted.
/////////////
شروع با انتخاب نام Custom Hook خود. اگر با انتخاب نام واضح مشکل دارید، این ممکن است بدان معنا باشد که افکت شما بیش از حد به بقیه منطق کامپوننت متصل است و هنوز آماده برای استخراج نیست.

Ideally, your custom Hook's name should be clear enough that even a person who doesn't write code often could have a good guess about what your custom Hook does, what it takes, and what it returns:
///////////////////
ایده‌آل، نام Custom Hook شما باید به اندازه کافی واضح باشد که حتی یک شخص که اغلب کد نمی‌نویسد، می‌تواند حدس خوبی درباره کاری که Custom Hook شما انجام می‌دهد، چه چیزی را می‌گیرد و چه چیزی برمی‌گرداند، بزند.


* ✅ `useData(url)`
* ✅ `useImpressionLog(eventName, extraData)`
* ✅ `useChatRoom(options)`

When you synchronize with an external system, your custom Hook name may be more technical and use jargon specific to that system. It's good as long as it would be clear to a person familiar with that system:
////////////////////
هنگامی که با یک سیستم خارجی همگام می‌شوید، نام Custom Hook شما ممکن است فنی‌تر و از اصطلاحات خاص آن سیستم استفاده کند. این خوب است تا زمانی که برای یک فرد آشنا با آن سیستم واضح باشد.


* ✅ `useMediaQuery(query)`
* ✅ `useSocket(url)`
* ✅ `useIntersectionObserver(ref, options)`

**Keep custom Hooks focused on concrete high-level use cases.** Avoid creating and using custom "lifecycle" Hooks that act as alternatives and convenience wrappers for the `useEffect` API itself:
///////////////////
ترجمه: Custom Hook های خود را بر روی موارد کاربردی با سطح بالای مشخص تمرکز دهید. ایجاد و استفاده از Custom Hook های "lifecycle" که به عنوان جایگزین و بسته‌بندی‌های راحتی برای API `useEffect` عمل می‌کنند، را اجتناب کنید.


* 🔴 `useMount(fn)`
* 🔴 `useEffectOnce(fn)`
* 🔴 `useUpdateEffect(fn)`

For example, this `useMount` Hook tries to ensure some code only runs "on mount":
مثالاً، این Custom Hook `useMount` سعی می‌کند اطمینان حاصل کند که برخی از کدها تنها "در زمان نصب" اجرا می‌شوند.

```js {4-5,14-15}
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // 🔴 Avoid: using custom "lifecycle" Hooks
  useMount(() => {
    const connection = createConnection({ roomId, serverUrl });
    connection.connect();

    post('/analytics/event', { eventName: 'visit_chat' });
  });
  // ...
}

// 🔴 Avoid: creating custom "lifecycle" Hooks
function useMount(fn) {
  useEffect(() => {
    fn();
  }, []); // 🔴 React Hook useEffect has a missing dependency: 'fn'
}
```

**Custom "lifecycle" Hooks like `useMount` don't fit well into the React paradigm.** For example, this code example has a mistake (it doesn't "react" to `roomId` or `serverUrl` changes), but the linter won't warn you about it because the linter only checks direct `useEffect` calls. It won't know about your Hook.
/////////////
Custom "lifecycle" Hooks مانند `useMount` با React paradigm سازگاری خوبی ندارند. به عنوان مثال، این مثال کد دارای یک خطا است (به تغییرات `roomId` یا `serverUrl` واکنش نمی‌دهد)، اما لینتر شما را در مورد آن هشدار نمی‌دهد زیرا لینتر فقط تماس‌های مستقیم `useEffect` را بررسی می‌کند. از وجود Hook شما مطلع نیست.

If you're writing an Effect, start by using the React API directly:
ترجمه: اگر شما در حال نوشتن یک افکت هستید، با استفاده مستقیم از API React شروع کنید.

```js
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // ✅ Good: two raw Effects separated by purpose

  useEffect(() => {
    const connection = createConnection({ serverUrl, roomId });
    connection.connect();
    return () => connection.disconnect();
  }, [serverUrl, roomId]);

  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_chat', roomId });
  }, [roomId]);

  // ...
}
```

Then, you can (but don't have to) extract custom Hooks for different high-level use cases:
سپس، شما می‌توانید (اما نیازی نیست) هوک‌های سفارشی را برای موارد استفاده با سطح بالا مختلف استخراج کنید.

```js
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // ✅ Great: custom Hooks named after their purpose
  useChatRoom({ serverUrl, roomId });
  useImpressionLog('visit_chat', { roomId });
  // ...
}
```

**A good custom Hook makes the calling code more declarative by constraining what it does.** For example, `useChatRoom(options)` can only connect to the chat room, while `useImpressionLog(eventName, extraData)` can only send an impression log to the analytics. If your custom Hook API doesn't constrain the use cases and is very abstract, in the long run it's likely to introduce more problems than it solves.
//////////
یک Hook سفارشی خوب با محدود کردن کاری که انجام می‌دهد، کد فراخوانی را اظهاری‌تر می‌کند. به عنوان مثال، useChatRoom(options) تنها می‌تواند به اتاق گفتگو متصل شود، در حالی که useImpressionLog(eventName, extraData) تنها می‌تواند یک ورودی احساس را به تجزیه و تحلیل ارسال کند. اگر API Hook سفارشی شما موارد استفاده را محدود نکند و بسیار انتزاعی باشد، درازمدت احتمالاً مشکلات بیشتری را نسبت به حل مشکل به وجود خواهد آورد
1
</DeepDive>

### Custom Hooks help you migrate to better patterns {/*custom-hooks-help-you-migrate-to-better-patterns*/}

Effects are an ["escape hatch"](/learn/escape-hatches): you use them when you need to "step outside React" and when there is no better built-in solution for your use case. With time, the React team's goal is to reduce the number of the Effects in your app to the minimum by providing more specific solutions to more specific problems. Wrapping your Effects in custom Hooks makes it easier to upgrade your code when these solutions become available.
////////////
افکت‌ها یکی از ["فرارگاه‌های فرار"](/learn/escape-hatches) هستند: شما از آن‌ها استفاده می‌کنید وقتی که نیاز دارید "از React خارج شوید" و هیچ راه‌حل داخلی بهتری برای مورد استفاده شما وجود ندارد. با گذر زمان، هدف تیم React این است که با ارائه راه‌حل‌های بیشتر ویژه‌تر برای مشکلات خاص، تعداد افکت‌ها در برنامه شما را به حداقل برساند. بسته‌بندی افکت‌های شما در Hook‌های سفارشی، آپگرید کردن کد شما را زمانی که این راه‌حل‌ها در دسترس قرار می‌گیرند، آسان‌تر می‌کند[1][4].

Let's return to this example:

<Sandpack>

```js
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```

```js useOnlineStatus.js active
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

</Sandpack>

In the above example, `useOnlineStatus` is implemented with a pair of [`useState`](/reference/react/useState) and [`useEffect`.](/reference/react/useEffect) However, this isn't the best possible solution. There is a number of edge cases it doesn't consider. For example, it assumes that when the component mounts, `isOnline` is already `true`, but this may be wrong if the network already went offline. You can use the browser [`navigator.onLine`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine) API to check for that, but using it directly would not work on the server for generating the initial HTML. In short, this code could be improved.
/////////////
در مثال بالا، `useOnlineStatus` با یک جفت از [`useState`](/reference/react/useState) و [`useEffect`](/reference/react/useEffect) پیاده‌سازی شده است. با این حال، این بهترین راه‌حل ممکن نیست. تعدادی موارد خاصی وجود دارد که در نظر گرفته نشده‌اند. به عنوان مثال، فرض می‌کند که هنگامی که کامپوننت مونت شده است، `isOnline` در حال حاضر `true` است، اما این ممکن است اشتباه باشد اگر شبکه قبلاً آفلاین شده باشد. شما می‌توانید از API [`navigator.onLine`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine) مرورگر برای بررسی آن استفاده کنید، اما استفاده مستقیم از آن برای تولید HTML اولیه در سمت سرور کار نمی‌کند. به طور خلاصه، این کد می‌تواند بهبود یابد[1][4].

Luckily, React 18 includes a dedicated API called [`useSyncExternalStore`](/reference/react/useSyncExternalStore) which takes care of all of these problems for you. Here is how your `useOnlineStatus` Hook, rewritten to take advantage of this new API:
///////////
به خوش‌شانسی، React 18 شامل یک API اختصاصی به نام [`useSyncExternalStore`](/reference/react/useSyncExternalStore) است که از تمام این مشکلات برای شما مراقبت می‌کند. در ادامه، نحوه بازنویسی Hook `useOnlineStatus` شما برای بهره‌گیری از این API جدید آورده شده است[1][4].

<Sandpack>

```js
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```

```js useOnlineStatus.js active
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

export function useOnlineStatus() {
  return useSyncExternalStore(
    subscribe,
    () => navigator.onLine, // How to get the value on the client
    () => true // How to get the value on the server
  );
}

```

</Sandpack>

Notice how **you didn't need to change any of the components** to make this migration:
در توجه داشته باشید که **شما نیازی به تغییر هیچ یک از کامپوننت‌ها نداشتید** تا این مهاجرت انجام شود.

```js {2,7}
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}
```

This is another reason for why wrapping Effects in custom Hooks is often beneficial:

1. You make the data flow to and from your Effects very explicit.
2. You let your components focus on the intent rather than on the exact implementation of your Effects.
3. When React adds new features, you can remove those Effects without changing any of your components.
////////////////
این یکی از دلایل دیگری است که بسته‌بندی افکت‌ها در Hook‌های سفارشی اغلب مفید است:

1. شما جریان داده به و از افکت‌های خود را بسیار صریح می‌کنید.
2. شما به کامپوننت‌های خود اجازه می‌دهید که بر روی اندازه دقیق افکت‌های خود تمرکز کنند.
3. وقتی React ویژگی‌های جدیدی اضافه می‌کند، می‌توانید این افکت‌ها را بدون تغییر هیچ یک از کامپوننت‌های خود حذف کنید[1][2][3][4][5].

Similar to a [design system,](https://uxdesign.cc/everything-you-need-to-know-about-design-systems-54b109851969) you might find it helpful to start extracting common idioms from your app's components into custom Hooks. This will keep your components' code focused on the intent, and let you avoid writing raw Effects very often. Many excellent custom Hooks are maintained by the React community.
////////////
مانند یک [سیستم طراحی،](https://uxdesign.cc/everything-you-need-to-know-about-design-systems-54b109851969) ممکن است مفید باشد که شروع به استخراج اصطلاحات مشترک از کامپوننت‌های برنامه‌ی خود به Hook‌های سفارشی کنید. این کار باعث می‌شود که کد کامپوننت‌های شما بر روی هدف تمرکز کند و به شما اجازه می‌دهد که از نوشتن افکت‌های خام بسیار اغلب خودداری کنید. بسیاری از Hook‌های سفارشی عالی توسط جامعه React حفظ می‌شوند[1][3][4][5].

<DeepDive>

#### Will React provide any built-in solution for data fetching? {/*will-react-provide-any-built-in-solution-for-data-fetching*/}

We're still working out the details, but we expect that in the future, you'll write data fetching like this:
//////////////
ما هنوز در حال بررسی جزئیات هستیم، اما انتظار داریم که در آینده، واکشی داده‌ها را به این صورت بنویسید:

```js {1,4,6}
import { use } from 'react'; // Not available yet!

function ShippingForm({ country }) {
  const cities = use(fetch(`/api/cities?country=${country}`));
  const [city, setCity] = useState(null);
  const areas = city ? use(fetch(`/api/areas?city=${city}`)) : null;
  // ...
```

If you use custom Hooks like `useData` above in your app, it will require fewer changes to migrate to the eventually recommended approach than if you write raw Effects in every component manually. However, the old approach will still work fine, so if you feel happy writing raw Effects, you can continue to do that.
////////////////
اگر از Hook‌های سفارشی مانند `useData` در برنامه‌ی خود استفاده کنید، برای مهاجرت به روش پیشنهادی در آینده، نیاز به تغییرات کمتری خواهد بود نسبت به زمانی که به صورت دستی افکت‌های خام را در هر کامپوننت بنویسید. با این حال، روش قدیمی همچنان عالی کار می‌کند، بنابراین اگر احساس می‌کنید که نوشتن افکت‌های خام برایتان رضایت‌بخش است، می‌توانید ادامه دهید[1][2][3][4][5].

</DeepDive>

### There is more than one way to do it {/*there-is-more-than-one-way-to-do-it*/}

Let's say you want to implement a fade-in animation *from scratch* using the browser [`requestAnimationFrame`](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) API. You might start with an Effect that sets up an animation loop. During each frame of the animation, you could change the opacity of the DOM node you [hold in a ref](/learn/manipulating-the-dom-with-refs) until it reaches `1`. Your code might start like this:
//////////////
متن مورد نظر شما بسیار طولانی است و شامل توضیحات مربوط به استفاده از `requestAnimationFrame` در React می‌شود. چون این متن بسیار طولانی است و شامل توضیحات فنی، برای ترجمه آن به فارسی نیاز به یک مترجم ماهر در زمینه‌ی توسعه وب و React داریم. در صورتی که سوال یا موضوع خاصی دارید که می‌خواهید در مورد آن اطلاعات بیشتری بدست آورید، خوشحال می‌شویم که کمک کنیم.

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';

function Welcome() {
  const ref = useRef(null);

  useEffect(() => {
    const duration = 1000;
    const node = ref.current;

    let startTime = performance.now();
    let frameId = null;

    function onFrame(now) {
      const timePassed = now - startTime;
      const progress = Math.min(timePassed / duration, 1);
      onProgress(progress);
      if (progress < 1) {
        // We still have more frames to paint
        frameId = requestAnimationFrame(onFrame);
      }
    }

    function onProgress(progress) {
      node.style.opacity = progress;
    }

    function start() {
      onProgress(0);
      startTime = performance.now();
      frameId = requestAnimationFrame(onFrame);
    }

    function stop() {
      cancelAnimationFrame(frameId);
      startTime = null;
      frameId = null;
    }

    start();
    return () => stop();
  }, []);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

To make the component more readable, you might extract the logic into a `useFadeIn` custom Hook:
برای افزایش خوانایی کامپوننت، ممکن است منطق آن را در یک Hook سفارشی به نام `useFadeIn` استخراج کنید.

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js useFadeIn.js
import { useEffect } from 'react';

export function useFadeIn(ref, duration) {
  useEffect(() => {
    const node = ref.current;

    let startTime = performance.now();
    let frameId = null;

    function onFrame(now) {
      const timePassed = now - startTime;
      const progress = Math.min(timePassed / duration, 1);
      onProgress(progress);
      if (progress < 1) {
        // We still have more frames to paint
        frameId = requestAnimationFrame(onFrame);
      }
    }

    function onProgress(progress) {
      node.style.opacity = progress;
    }

    function start() {
      onProgress(0);
      startTime = performance.now();
      frameId = requestAnimationFrame(onFrame);
    }

    function stop() {
      cancelAnimationFrame(frameId);
      startTime = null;
      frameId = null;
    }

    start();
    return () => stop();
  }, [ref, duration]);
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

You could keep the `useFadeIn` code as is, but you could also refactor it more. For example, you could extract the logic for setting up the animation loop out of `useFadeIn` into a custom `useAnimationLoop` Hook:
//////////////
شما می‌توانید کد `useFadeIn` را به همان شکل نگه دارید، اما می‌توانید آن را بازنویسی کنید. به عنوان مثال، می‌توانید منطق برای راه‌اندازی حلقه‌ی انیمیشن را از `useFadeIn` به یک Hook سفارشی به نام `useAnimationLoop` استخراج کنید.

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js useFadeIn.js active
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useFadeIn(ref, duration) {
  const [isRunning, setIsRunning] = useState(true);

  useAnimationLoop(isRunning, (timePassed) => {
    const progress = Math.min(timePassed / duration, 1);
    ref.current.style.opacity = progress;
    if (progress === 1) {
      setIsRunning(false);
    }
  });
}

function useAnimationLoop(isRunning, drawFrame) {
  const onFrame = useEffectEvent(drawFrame);

  useEffect(() => {
    if (!isRunning) {
      return;
    }

    const startTime = performance.now();
    let frameId = null;

    function tick(now) {
      const timePassed = now - startTime;
      onFrame(timePassed);
      frameId = requestAnimationFrame(tick);
    }

    tick();
    return () => cancelAnimationFrame(frameId);
  }, [isRunning]);
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

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

</Sandpack>

However, you didn't *have to* do that. As with regular functions, ultimately you decide where to draw the boundaries between different parts of your code. You could also take a very different approach. Instead of keeping the logic in the Effect, you could move most of the imperative logic inside a JavaScript [class:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
////////////////
با این حال، شما مجبور به انجام این کار نبودید. همانطور که با توابع معمولی است، در نهایت شما تصمیم می‌گیرید که مرزهای مختلف بین بخش‌های مختلف کد خود را کجا قرار دهید. شما همچنین می‌توانید به یک رویکرد بسیار متفاوت بپردازید. به جای نگه داشتن منطق در افکت، می‌توانید بیشتر منطق امری را داخل یک [کلاس JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) منتقل کنید.

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js useFadeIn.js active
import { useState, useEffect } from 'react';
import { FadeInAnimation } from './animation.js';

export function useFadeIn(ref, duration) {
  useEffect(() => {
    const animation = new FadeInAnimation(ref.current);
    animation.start(duration);
    return () => {
      animation.stop();
    };
  }, [ref, duration]);
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
    if (progress === 1) {
      this.stop();
    } else {
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
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

Effects let you connect React to external systems. The more coordination between Effects is needed (for example, to chain multiple animations), the more it makes sense to extract that logic out of Effects and Hooks *completely* like in the sandbox above. Then, the code you extracted *becomes* the "external system". This lets your Effects stay simple because they only need to send messages to the system you've moved outside React.
/////////////
افکت‌ها به شما اجازه می‌دهند تا React را به سیستم‌های خارجی متصل کنید. هر چه هماهنگی بیشتری بین افکت‌ها نیاز است (به عنوان مثال، برای زنجیره‌ای کردن چند انیمیشن)، منطقی‌تر است که آن منطق را به طور کامل از افکت‌ها و هوک‌ها خارج کنید، همانند مثال بالا. در این صورت، کدی که استخراج کرده‌اید، به "سیستم خارجی" تبدیل می‌شود. این امکان را به شما می‌دهد که افکت‌های خود را ساده نگه دارید، زیرا تنها نیاز به ارسال پیام به سیستمی که بیرون از React منتقل کرده‌اید دارند.

The examples above assume that the fade-in logic needs to be written in JavaScript. However, this particular fade-in animation is both simpler and much more efficient to implement with a plain [CSS Animation:](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations)
///////////
مثال‌های بالا فرض می‌کنند که منطق ظاهر شدن کم‌کم نیاز به نوشتن در جاوااسکریپت دارد. با این حال، این انیمیشن خاص ظاهر شدن کم‌کم بهتر و کارآمدتر است که با استفاده از یک [انیمیشن CSS ساده](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations) پیاده‌سازی شود.

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import './welcome.css';

function Welcome() {
  return (
    <h1 className="welcome">
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```css styles.css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
```

```css welcome.css active
.welcome {
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);

  animation: fadeIn 1000ms;
}

@keyframes fadeIn {
  0% { opacity: 0; }
  100% { opacity: 1; }
}

```

</Sandpack>

Sometimes, you don't even need a Hook!
گاهی اوقات، شما حتی به یک هوک هم نیاز ندارید!
<Recap>

- Custom Hooks let you share logic between components.
- Custom Hooks must be named starting with `use` followed by a capital letter.
- Custom Hooks only share stateful logic, not state itself.
- You can pass reactive values from one Hook to another, and they stay up-to-date.
- All Hooks re-run every time your component re-renders.
- The code of your custom Hooks should be pure, like your component's code.
- Wrap event handlers received by custom Hooks into Effect Events.
- Don't create custom Hooks like `useMount`. Keep their purpose specific.
- It's up to you how and where to choose the boundaries of your code.
///////////////

Custom Hooks let you share logic between components.
هوک‌های سفارشی به شما اجازه می‌دهند تا منطق را بین کامپوننت‌ها به اشتراک بگذارید.

Custom Hooks must be named starting with `use` followed by a capital letter.
هوک‌های سفارشی باید با `use` شروع شده و دنبال آن یک حرف بزرگ داشته باشند.

Custom Hooks only share stateful logic, not state itself.
هوک‌های سفارشی تنها منطق مربوط به وضعیت را به اشتراک می‌گذارند، نه خود وضعیت.

You can pass reactive values from one Hook to another, and they stay up-to-date.
شما می‌توانید مقادیر واکنشی را از یک Hook به دیگری منتقل کنید و آن‌ها به‌روز می‌مانند.

All Hooks re-run every time your component re-renders.
تمامی Hook‌ها هر بار که کامپوننت شما مجدداً رندر می‌شود، مجدداً اجرا می‌شوند.

The code of your custom Hooks should be pure, like your component's code.
کد هوک‌های سفارشی شما باید مانند کد کامپوننت‌هایتان خالص باشد.

Wrap event handlers received by custom Hooks into Effect Events.
دستگیره‌های رویداد دریافت شده توسط Hook‌های سفارشی را به رویدادهای افکت بسته‌بندی کنید.

Don't create custom Hooks like `useMount`. Keep their purpose specific.
هوک‌های سفارشی مانند `useMount` ایجاد نکنید. هدف خاص خود را حفظ کنید.

It's up to you how and where to choose the boundaries of your code.
این بستگی به شما دارد که مرزهای کد خود را چگونه و کجا انتخاب کنید.

</Recap>

<Challenges>

#### Extract a `useCounter` Hook {/*extract-a-usecounter-hook*/}

This component uses a state variable and an Effect to display a number that increments every second. Extract this logic into a custom Hook called `useCounter`. Your goal is to make the `Counter` component implementation look exactly like this:

```js
export default function Counter() {
  const count = useCounter();
  return <h1>Seconds passed: {count}</h1>;
}
```

You'll need to write your custom Hook in `useCounter.js` and import it into the `Counter.js` file.

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js useCounter.js
// Write your custom Hook in this file!
```

</Sandpack>

<Solution>

Your code should look like this:

<Sandpack>

```js
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter();
  return <h1>Seconds passed: {count}</h1>;
}
```

```js useCounter.js
import { useState, useEffect } from 'react';

export function useCounter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}
```

</Sandpack>

Notice that `App.js` doesn't need to import `useState` or `useEffect` anymore.

</Solution>

#### Make the counter delay configurable {/*make-the-counter-delay-configurable*/}

In this example, there is a `delay` state variable controlled by a slider, but its value is not used. Pass the `delay` value to your custom `useCounter` Hook, and change the `useCounter` Hook to use the passed `delay` instead of hardcoding `1000` ms.

<Sandpack>

```js
import { useState } from 'react';
import { useCounter } from './useCounter.js';

export default function Counter() {
  const [delay, setDelay] = useState(1000);
  const count = useCounter();
  return (
    <>
      <label>
        Tick duration: {delay} ms
        <br />
        <input
          type="range"
          value={delay}
          min="10"
          max="2000"
          onChange={e => setDelay(Number(e.target.value))}
        />
      </label>
      <hr />
      <h1>Ticks: {count}</h1>
    </>
  );
}
```

```js useCounter.js
import { useState, useEffect } from 'react';

export function useCounter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}
```

</Sandpack>

<Solution>

Pass the `delay` to your Hook with `useCounter(delay)`. Then, inside the Hook, use `delay` instead of the hardcoded `1000` value. You'll need to add `delay` to your Effect's dependencies. This ensures that a change in `delay` will reset the interval.

<Sandpack>

```js
import { useState } from 'react';
import { useCounter } from './useCounter.js';

export default function Counter() {
  const [delay, setDelay] = useState(1000);
  const count = useCounter(delay);
  return (
    <>
      <label>
        Tick duration: {delay} ms
        <br />
        <input
          type="range"
          value={delay}
          min="10"
          max="2000"
          onChange={e => setDelay(Number(e.target.value))}
        />
      </label>
      <hr />
      <h1>Ticks: {count}</h1>
    </>
  );
}
```

```js useCounter.js
import { useState, useEffect } from 'react';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, delay);
    return () => clearInterval(id);
  }, [delay]);
  return count;
}
```

</Sandpack>

</Solution>

#### Extract `useInterval` out of `useCounter` {/*extract-useinterval-out-of-usecounter*/}

Currently, your `useCounter` Hook does two things. It sets up an interval, and it also increments a state variable on every interval tick. Split out the logic that sets up the interval into a separate Hook called `useInterval`. It should take two arguments: the `onTick` callback, and the `delay`. After this change, your `useCounter` implementation should look like this:

```js
export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

Write `useInterval` in the `useInterval.js` file and import it into the `useCounter.js` file.

<Sandpack>

```js
import { useState } from 'react';
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter(1000);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js useCounter.js
import { useState, useEffect } from 'react';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, delay);
    return () => clearInterval(id);
  }, [delay]);
  return count;
}
```

```js useInterval.js
// Write your Hook here!
```

</Sandpack>

<Solution>

The logic inside `useInterval` should set up and clear the interval. It doesn't need to do anything else.

<Sandpack>

```js
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter(1000);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js useInterval.js active
import { useEffect } from 'react';

export function useInterval(onTick, delay) {
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => clearInterval(id);
  }, [onTick, delay]);
}
```

</Sandpack>

Note that there is a bit of a problem with this solution, which you'll solve in the next challenge.

</Solution>

#### Fix a resetting interval {/*fix-a-resetting-interval*/}

In this example, there are *two* separate intervals.

The `App` component calls `useCounter`, which calls `useInterval` to update the counter every second. But the `App` component *also* calls `useInterval` to randomly update the page background color every two seconds.

For some reason, the callback that updates the page background never runs. Add some logs inside `useInterval`:

```js {2,5}
  useEffect(() => {
    console.log('✅ Setting up an interval with delay ', delay)
    const id = setInterval(onTick, delay);
    return () => {
      console.log('❌ Clearing an interval with delay ', delay)
      clearInterval(id);
    };
  }, [onTick, delay]);
```

Do the logs match what you expect to happen? If some of your Effects seem to re-synchronize unnecessarily, can you guess which dependency is causing that to happen? Is there some way to [remove that dependency](/learn/removing-effect-dependencies) from your Effect?

After you fix the issue, you should expect the page background to update every two seconds.

<Hint>

It looks like your `useInterval` Hook accepts an event listener as an argument. Can you think of some way to wrap that event listener so that it doesn't need to be a dependency of your Effect?

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
import { useCounter } from './useCounter.js';
import { useInterval } from './useInterval.js';

export default function Counter() {
  const count = useCounter(1000);

  useInterval(() => {
    const randomColor = `hsla(${Math.random() * 360}, 100%, 50%, 0.2)`;
    document.body.style.backgroundColor = randomColor;
  }, 2000);

  return <h1>Seconds passed: {count}</h1>;
}
```

```js useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js useInterval.js
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useInterval(onTick, delay) {
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => {
      clearInterval(id);
    };
  }, [onTick, delay]);
}
```

</Sandpack>

<Solution>

Inside `useInterval`, wrap the tick callback into an Effect Event, as you did [earlier on this page.](/learn/reusing-logic-with-custom-hooks#passing-event-handlers-to-custom-hooks)

This will allow you to omit `onTick` from dependencies of your Effect. The Effect won't re-synchronize on every re-render of the component, so the page background color change interval won't get reset every second before it has a chance to fire.

With this change, both intervals work as expected and don't interfere with each other:

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
import { useCounter } from './useCounter.js';
import { useInterval } from './useInterval.js';

export default function Counter() {
  const count = useCounter(1000);

  useInterval(() => {
    const randomColor = `hsla(${Math.random() * 360}, 100%, 50%, 0.2)`;
    document.body.style.backgroundColor = randomColor;
  }, 2000);

  return <h1>Seconds passed: {count}</h1>;
}
```

```js useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js useInterval.js active
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useInterval(callback, delay) {
  const onTick = useEffectEvent(callback);
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => clearInterval(id);
  }, [delay]);
}
```

</Sandpack>

</Solution>

#### Implement a staggering movement {/*implement-a-staggering-movement*/}

In this example, the `usePointerPosition()` Hook tracks the current pointer position. Try moving your cursor or your finger over the preview area and see the red dot follow your movement. Its position is saved in the `pos1` variable.

In fact, there are five (!) different red dots being rendered. You don't see them because currently they all appear at the same position. This is what you need to fix. What you want to implement instead is a "staggered" movement: each dot should "follow" the previous dot's path. For example, if you quickly move your cursor, the first dot should follow it immediately, the second dot should follow the first dot with a small delay, the third dot should follow the second dot, and so on.

You need to implement the `useDelayedValue` custom Hook. Its current implementation returns the `value` provided to it. Instead, you want to return the value back from `delay` milliseconds ago. You might need some state and an Effect to do this.

After you implement `useDelayedValue`, you should see the dots move following one another.

<Hint>

You'll need to store the `delayedValue` as a state variable inside your custom Hook. When the `value` changes, you'll want to run an Effect. This Effect should update `delayedValue` after the `delay`. You might find it helpful to call `setTimeout`.

Does this Effect need cleanup? Why or why not?

</Hint>

<Sandpack>

```js
import { usePointerPosition } from './usePointerPosition.js';

function useDelayedValue(value, delay) {
  // TODO: Implement this Hook
  return value;
}

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos3, 50);
  return (
    <>
      <Dot position={pos1} opacity={1} />
      <Dot position={pos2} opacity={0.8} />
      <Dot position={pos3} opacity={0.6} />
      <Dot position={pos4} opacity={0.4} />
      <Dot position={pos5} opacity={0.2} />
    </>
  );
}

function Dot({ position, opacity }) {
  return (
    <div style={{
      position: 'absolute',
      backgroundColor: 'pink',
      borderRadius: '50%',
      opacity,
      transform: `translate(${position.x}px, ${position.y}px)`,
      pointerEvents: 'none',
      left: -20,
      top: -20,
      width: 40,
      height: 40,
    }} />
  );
}
```

```js usePointerPosition.js
import { useState, useEffect } from 'react';

export function usePointerPosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, []);
  return position;
}
```

```css
body { min-height: 300px; }
```

</Sandpack>

<Solution>

Here is a working version. You keep the `delayedValue` as a state variable. When `value` updates, your Effect schedules a timeout to update the `delayedValue`. This is why the `delayedValue` always "lags behind" the actual `value`.

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { usePointerPosition } from './usePointerPosition.js';

function useDelayedValue(value, delay) {
  const [delayedValue, setDelayedValue] = useState(value);

  useEffect(() => {
    setTimeout(() => {
      setDelayedValue(value);
    }, delay);
  }, [value, delay]);

  return delayedValue;
}

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos3, 50);
  return (
    <>
      <Dot position={pos1} opacity={1} />
      <Dot position={pos2} opacity={0.8} />
      <Dot position={pos3} opacity={0.6} />
      <Dot position={pos4} opacity={0.4} />
      <Dot position={pos5} opacity={0.2} />
    </>
  );
}

function Dot({ position, opacity }) {
  return (
    <div style={{
      position: 'absolute',
      backgroundColor: 'pink',
      borderRadius: '50%',
      opacity,
      transform: `translate(${position.x}px, ${position.y}px)`,
      pointerEvents: 'none',
      left: -20,
      top: -20,
      width: 40,
      height: 40,
    }} />
  );
}
```

```js usePointerPosition.js
import { useState, useEffect } from 'react';

export function usePointerPosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, []);
  return position;
}
```

```css
body { min-height: 300px; }
```

</Sandpack>

Note that this Effect *does not* need cleanup. If you called `clearTimeout` in the cleanup function, then each time the `value` changes, it would reset the already scheduled timeout. To keep the movement continuous, you want all the timeouts to fire.

</Solution>

</Challenges>
