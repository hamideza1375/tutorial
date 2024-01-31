---
title: 'Manipulating the DOM with Refs (دستکاری DOM با Refs)'
---

<Intro>

React automatically updates the [DOM](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) to match your render output, so your components won't often need to manipulate it. However, sometimes you might need access to the DOM elements managed by React--for example, to focus a node, scroll to it, or measure its size and position. There is no built-in way to do those things in React, so you will need a *ref* to the DOM node.
//////////////////
React به طور خودکار DOM را برای تطبیق با خروجی رندر شما به‌روز می‌کند، بنابراین کامپوننت‌های شما به طور معمول نیازی به مدیریت آن ندارند. با این حال، گاهی اوقات ممکن است به المان‌های DOM مدیریت شده توسط React دسترسی داشته باشید - به عنوان مثال، برای تمرکز روی یک نود، به آن اسکرول کنید، یا اندازه و موقعیت آن را اندازه‌گیری کنید. در React راهی داخلی برای انجام این کارها وجود ندارد، بنابراین شما به یک *ref* به المان DOM نیاز خواهید داشت.
</Intro>

<YouWillLearn>

- How to access a DOM node managed by React with the `ref` attribute
- How the `ref` JSX attribute relates to the `useRef` Hook
- How to access another component's DOM node
- In which cases it's safe to modify the DOM managed by React

</YouWillLearn>

## Getting a ref to the node {/*getting-a-ref-to-the-node*/}

To access a DOM node managed by React, first, import the `useRef` Hook:
برای دسترسی به یک گره DOM که توسط React مدیریت می شود، ابتدا هوک «useRef» را وارد کنید:

```js
import { useRef } from 'react';
```

Then, use it to declare a ref inside your component:
سپس، از آن برای اعلام یک ref در کامپوننت خود استفاده کنید:

```js
const myRef = useRef(null);
```

Finally, pass your ref as the `ref` attribute to the JSX tag for which you want to get the DOM node:
در نهایت، ref خود را به عنوان ویژگی «ref» به تگ JSX که می‌خواهید گره DOM را برای آن دریافت کنید، ارسال کنید:

```js
<div ref={myRef}>
```

The `useRef` Hook returns an object with a single property called `current`. Initially, `myRef.current` will be `null`. When React creates a DOM node for this `<div>`, React will put a reference to this node into `myRef.current`. You can then access this DOM node from your [event handlers](/learn/responding-to-events) and use the built-in [browser APIs](https://developer.mozilla.org/docs/Web/API/Element) defined on it.
//////////////
`useRef` Hook یک شیء با یک خاصیت به نام `current` را برمی‌گرداند. در ابتدا، `myRef.current` `null` خواهد بود. هنگامی که React یک المان DOM برای این `<div>` ایجاد می‌کند، React یک مرجع به این المان در `myRef.current` قرار می‌دهد. سپس می‌توانید از این المان DOM در event handlers خود استفاده کنید و از API های مرورگر داخلی روی آن استفاده کنید.

```js
// You can use any browser APIs, for example:
myRef.current.scrollIntoView();
```

### Example: Focusing a text input {/*example-focusing-a-text-input*/}

In this example, clicking the button will focus the input:
در این مثال، کلیک کردن روی دکمه ورودی را متمرکز می کند:

<Sandpack>

```js
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

To implement this:
برای پیاده سازی این:

1. Declare `inputRef` with the `useRef` Hook.
2. Pass it as `<input ref={inputRef}>`. This tells React to **put this `<input>`'s DOM node into `inputRef.current`.**
3. In the `handleClick` function, read the input DOM node from `inputRef.current` and call [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) on it with `inputRef.current.focus()`.
4. Pass the `handleClick` event handler to `<button>` with `onClick`.
/////////////
1. با استفاده از Hook `useRef`، `inputRef` را اعلام کنید.
2. آن را به عنوان `<input ref={inputRef}>` منتقل کنید. این به React می‌گوید که **المان DOM این `<input>` را در `inputRef.current` قرار دهد.**
3. در تابع `handleClick`، المان DOM ورودی را از `inputRef.current` بخوانید و با `inputRef.current.focus()` روی آن [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) را فراخوانی کنید.
4. `handleClick` را به `<button>` با `onClick` منتقل کنید.

While DOM manipulation is the most common use case for refs, the `useRef` Hook can be used for storing other things outside React, like timer IDs. Similarly to state, refs remain between renders. Refs are like state variables that don't trigger re-renders when you set them. Read about refs in [Referencing Values with Refs.](/learn/referencing-values-with-refs)
////////////////
اگرچه مدیریت DOM معمول‌ترین کاربرد refs است، Hook `useRef` می‌تواند برای ذخیره سازی چیزهای دیگر خارج از React مانند شناسه‌های تایمر استفاده شود. مانند state، refs بین رندرینگ‌ها باقی می‌مانند. Refs شبیه به متغیرهای state هستند که هنگام تنظیم آنها رندر مجدد را فراخوانی نمی‌کنند. در [Referencing Values with Refs](/learn/referencing-values-with-refs) درباره refs بیشتر بخوانید..

### مثال: اسکرول به یک المان {/*example-scrolling-to-an-element*/}

You can have more than a single ref in a component. In this example, there is a carousel of three images. Each button centers an image by calling the browser [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) method on the corresponding DOM node:
///////////////

شما می‌توانید بیش از یک ref در یک کامپوننت داشته باشید. در این مثال، یک کاروسل سه تصویری وجود دارد. هر دکمه با فراخوانی متد مرورگر [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) روی المان DOM مربوطه، یک تصویر را در مرکز قرار می‌دهد:

<Sandpack>

```js
import { useRef } from 'react';

export default function CatFriends() {
  const firstCatRef = useRef(null);
  const secondCatRef = useRef(null);
  const thirdCatRef = useRef(null);

  function handleScrollToFirstCat() {
    firstCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToSecondCat() {
    secondCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToThirdCat() {
    thirdCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  return (
    <>
      <nav>
        <button onClick={handleScrollToFirstCat}>
          Tom
        </button>
        <button onClick={handleScrollToSecondCat}>
          Maru
        </button>
        <button onClick={handleScrollToThirdCat}>
          Jellylorum
        </button>
      </nav>
      <div>
        <ul>
          <li>
            <img
              src="https://placekitten.com/g/200/200"
              alt="Tom"
              ref={firstCatRef}
            />
          </li>
          <li>
            <img
              src="https://placekitten.com/g/300/200"
              alt="Maru"
              ref={secondCatRef}
            />
          </li>
          <li>
            <img
              src="https://placekitten.com/g/250/200"
              alt="Jellylorum"
              ref={thirdCatRef}
            />
          </li>
        </ul>
      </div>
    </>
  );
}
```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

<DeepDive>

#### How to manage a list of refs using a ref callback(چگونه با استفاده از یک بازخورد ref یک لیست از refs را مدیریت کنیم؟ ) {/*how-to-manage-a-list-of-refs-using-a-ref-callback*/}


In the above examples, there is a predefined number of refs. However, sometimes you might need a ref to each item in the list, and you don't know how many you will have. Something like this **wouldn't work**:
////////////
در مثال‌های بالا، تعدادی از refs پیش‌تعریف شده است. با این حال، گاهی اوقات ممکن است به یک ref برای هر مورد در لیست نیاز داشته باشید و نمی‌دانید چند تا خواهید داشت. چیزی شبیه به این **کار نمی‌کند**:

```js
<ul>
  {items.map((item) => {
    // Doesn't work!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```

This is because **Hooks must only be called at the top-level of your component.** You can't call `useRef` in a loop, in a condition, or inside a `map()` call.
/////////////
این به این دلیل است که **Hooks باید فقط در سطح بالای کامپوننت شما فراخوانی شوند.** شما نمی‌توانید `useRef` را در یک حلقه، در یک شرط، یا درون یک فراخوانی `map()` فراخوانی کنید.

One possible way around this is to get a single ref to their parent element, and then use DOM manipulation methods like [`querySelectorAll`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) to "find" the individual child nodes from it. However, this is brittle and can break if your DOM structure changes.
/////////////////
یکی از راه‌های ممکن برای این کار، دریافت یک ref واحد به المان والد آنهاست، و سپس از روش‌های مدیریت DOM مانند [`querySelectorAll`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) برای "یافتن" گره‌های فرزند فردی از آن استفاده کنید. با این حال، این روش شکننده است و ممکن است در صورت تغییر ساختار DOM شما شکست بخورد.

Another solution is to **pass a function to the `ref` attribute.** This is called a [`ref` callback.](/reference/react-dom/components/common#ref-callback) React will call your ref callback with the DOM node when it's time to set the ref, and with `null` when it's time to clear it. This lets you maintain your own array or a [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), and access any ref by its index or some kind of ID.
//////////////
یکی از راه‌های ممکن برای این کار، **انتقال یک تابع به ویژگی `ref`.** این به عنوان یک [`ref` callback](/reference/react-dom/components/common#ref-callback) شناخته می‌شود. React در زمان تنظیم ref با المان DOM بازخورد ref شما را فراخوانی می‌کند، و با `null` در زمان پاک کردن آن. این به شما اجازه می‌دهد تا آرایه خود یا یک [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) را حفظ کنید و با هر شاخص یا نوع ID مربوطه دسترسی داشته باشید..

This example shows how you can use this approach to scroll to an arbitrary node in a long list:
این مثال نشان می‌دهد که چگونه می‌توانید از این روش برای اسکرول به یک گره خاص در یک لیست بلند استفاده کنید:

<Sandpack>

```js
import { useRef } from 'react';

export default function CatFriends() {
  const itemsRef = useRef(null);

  function scrollToId(itemId) {
    const map = getMap();
    const node = map.get(itemId);
    node.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      // Initialize the Map on first usage.
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToId(0)}>
          Tom
        </button>
        <button onClick={() => scrollToId(5)}>
          Maru
        </button>
        <button onClick={() => scrollToId(9)}>
          Jellylorum
        </button>
      </nav>
      <div>
        <ul>
          {catList.map(cat => (
            <li
              key={cat.id}
              ref={(node) => {
                const map = getMap();
                if (node) {
                  map.set(cat.id, node);
                } else {
                  map.delete(cat.id);
                }
              }}
            >
              <img
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

In this example, `itemsRef` doesn't hold a single DOM node. Instead, it holds a [Map](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) from item ID to a DOM node. ([Refs can hold any values!](/learn/referencing-values-with-refs)) The [`ref` callback](/reference/react-dom/components/common#ref-callback) on every list item takes care to update the Map:
////////////////////
در این مثال، `itemsRef` یک المان DOM واحد را نگه نمی‌دارد. به جای آن، یک [Map](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) از شناسه مورد به یک المان DOM نگه می‌دارد. ([Refs می‌توانند هر مقداری را نگه دارند!](/learn/referencing-values-with-refs)) هر [`ref` callback](/reference/react-dom/components/common#ref-callback) برای هر مورد در لیست، به روزرسانی Map را انجام می‌دهد:

```js
<li
  key={cat.id}
  ref={node => {
    const map = getMap();
    if (node) {
      // Add to the Map
      map.set(cat.id, node);
    } else {
      // Remove from the Map
      map.delete(cat.id);
    }
  }}
>
```

This lets you read individual DOM nodes from the Map later.
این به شما امکان می‌دهد گره‌های DOM جداگانه را بعداً از نقشه بخوانید.

</DeepDive>

## دسترسی به گره‌های DOM کامپوننت دیگر {/*accessing-another-components-dom-nodes*/}

When you put a ref on a built-in component that outputs a browser element like `<input />`, React will set that ref's `current` property to the corresponding DOM node (such as the actual `<input />` in the browser).

However, if you try to put a ref on **your own** component, like `<MyInput />`, by default you will get `null`. Here is an example demonstrating it. Notice how clicking the button **does not** focus the input:
//////////////////////
وقتی شما یک ref را بر روی یک کامپوننت پیش‌تعریف شده که یک المان مرورگر مانند `<input />` را خروجی می‌دهد قرار می‌دهید، React این خاصیت `current` ref را به المان DOM مربوطه (مانند `<input />` واقعی در مرورگر) تنظیم می‌کند.

با این حال، اگر شما سعی کنید یک ref را بر روی **کامپوننت خودتان** مانند `<MyInput />` قرار دهید، به طور پیش‌فرض `null` دریافت خواهید کرد. در اینجا یک مثال برای نشان دادن آن وجود دارد. توجه کنید که با کلیک بر روی دکمه، **تمرکز روی ورودی صورت نمی‌گیرد**:


<Sandpack>

```js
import { useRef } from 'react';

function MyInput(props) {
  return <input {...props} />;
}

export default function MyForm() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

To help you notice the issue, React also prints an error to the console:
///////////////
همچنین React یک خطایی را به کنسول چاپ می‌کند تا به شما کمک کند مشکل را تشخیص دهید:

<ConsoleBlock level="error">

Warning: Function components cannot be given refs. Attempts to access this ref will fail. Did you mean to use React.forwardRef()?
//////////////////
هشدار: اجزای تابع نمی‌توانند refs داده شوند. تلاش برای دسترسی به این ref با شکست مواجه خواهد شد. آیا منظور شما استفاده از React.forwardRef() است؟

</ConsoleBlock>

This happens because by default React does not let a component access the DOM nodes of other components. Not even for its own children! This is intentional. Refs are an escape hatch that should be used sparingly. Manually manipulating _another_ component's DOM nodes makes your code even more fragile.
//////////////
این اتفاق به دلیل این است که به طور پیش‌فرض React به اجزای یک کامپوننت اجازه دسترسی به عناصر DOM سایر کامپوننت‌ها را نمی‌دهد. حتی برای فرزندان خودش هم! این قصدی است. Refs یک دروازه فرار هستند که باید با احتیاط استفاده شوند. دستیابی به عناصر DOM کامپوننت‌های دیگر باعث شکنندگی بیشتر کد شما می‌شود.

Instead, components that _want_ to expose their DOM nodes have to **opt in** to that behavior. A component can specify that it "forwards" its ref to one of its children. Here's how `MyInput` can use the `forwardRef` API:
/////////////////
Instead, components that _want_ to expose their DOM nodes have to **opt in** to that behavior. A component can specify that it "forwards" its ref to one of its children. Here's how `MyInput` can use the `forwardRef` API:
/////////////////
به جای این، اجزایی که می‌خواهند عناصر DOM خود را نشان دهند، باید به این رفتار **پیوست** شوند. یک اجزا می‌تواند مشخص کند که ref خود را به یکی از فرزندان خود "انتقال" دهد. اینجا نحوه استفاده از API `forwardRef` برای `MyInput` آمده است:

```js
const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});
```

This is how it works:

1. `<MyInput ref={inputRef} />` tells React to put the corresponding DOM node into `inputRef.current`. However, it's up to the `MyInput` component to opt into that--by default, it doesn't.
2. The `MyInput` component is declared using `forwardRef`. **This opts it into receiving the `inputRef` from above as the second `ref` argument** which is declared after `props`.
3. `MyInput` itself passes the `ref` it received to the `<input>` inside of it.
//////////////////
این چگونه کار می‌کند:

1. `<MyInput ref={inputRef} />` به React می‌گوید که عنصر DOM مربوطه را در `inputRef.current` قرار دهد. با این حال، به عهدهٔ کامپوننت `MyInput` است که به این رفتار پیوست کند - به طور پیش‌فرض، این اتفاق نمی‌افتد.
2. کامپوننت `MyInput` با استفاده از `forwardRef` تعریف شده است. **این باعث می‌شود که به عنوان دومین آرگومان `ref`، `inputRef` از بالا دریافت شود** که پس از `props` تعریف شده است.
3. `MyInput` خود `ref` دریافتی را به `<input>` درون خود منتقل می‌کند..

Now clicking the button to focus the input works:
اکنون با کلیک بر روی دکمه تمرکز ورودی کار می کند:

<Sandpack>

```js
import { forwardRef, useRef } from 'react';

const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

In design systems, it is a common pattern for low-level components like buttons, inputs, and so on, to forward their refs to their DOM nodes. On the other hand, high-level components like forms, lists, or page sections usually won't expose their DOM nodes to avoid accidental dependencies on the DOM structure.
/////////////////////
در سیستم‌های طراحی، الگوی معمول برای اجزای پایین‌سطح مانند دکمه‌ها، ورودی‌ها و موارد مشابه، این است که ref خود را به عناصر DOM خود منتقل کنند. از طرف دیگر، اجزای بالا سطح مانند فرم‌ها، لیست‌ها یا بخش‌های صفحه معمولاً عناصر DOM خود را نشان نمی‌دهند تا از وابستگی‌های تصادفی به ساختار DOM جلوگیری شود.

<DeepDive>

#### ارائه زیرمجموعه‌ای از API با یک handle امریّ {/*exposing-a-subset-of-the-api-with-an-imperative-handle*/}

In the above example, `MyInput` exposes the original DOM input element. This lets the parent component call `focus()` on it. However, this also lets the parent component do something else--for example, change its CSS styles. In uncommon cases, you may want to restrict the exposed functionality. You can do that with `useImperativeHandle`:
///////////////////
در مثال بالا، `MyInput` عنصر DOM اصلی ورودی را نشان می‌دهد. این به والدین کامپوننت اجازه می‌دهد تا `focus()` را روی آن فراخوانی کنند. با این حال، این همچنین به والدین کامپوننت اجازه می‌دهد که کار دیگری انجام دهند - به عنوان مثال، تغییر سبک CSS آن. در موارد نادر، شاید بخواهید قابلیت‌های ارائه شده را محدود کنید. می‌توانید این کار را با `useImperativeHandle` انجام دهید:


<Sandpack>

```js
import {
  forwardRef, 
  useRef, 
  useImperativeHandle
} from 'react';

const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    // Only expose focus and nothing else
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input {...props} ref={realInputRef} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

Here, `realInputRef` inside `MyInput` holds the actual input DOM node. However, `useImperativeHandle` instructs React to provide your own special object as the value of a ref to the parent component. So `inputRef.current` inside the `Form` component will only have the `focus` method. In this case, the ref "handle" is not the DOM node, but the custom object you create inside `useImperativeHandle` call.
///////////////////////////
اینجا، `realInputRef` درون `MyInput`، عنصر DOM واقعی ورودی را نگه می‌دارد. با این حال، `useImperativeHandle` به React دستور می‌دهد که شیء خاص خود را به عنوان مقدار ref به کامپوننت والد ارائه دهد. بنابراین، `inputRef.current` درون کامپوننت `Form` فقط دارای متد `focus` خواهد بود. در این مورد، "handle" ref، عنصر DOM نیست، بلکه شیء سفارشی‌ای است که درون تماس `useImperativeHandle` ایجاد می‌کنید.

</DeepDive>

## هنگامی که React refs را اتصال می‌دهد {/*when-react-attaches-the-refs*/}

In React, every update is split in [two phases](/learn/render-and-commit#step-3-react-commits-changes-to-the-dom):

* During **render,** React calls your components to figure out what should be on the screen.
* During **commit,** React applies changes to the DOM.

In general, you [don't want](/learn/referencing-values-with-refs#best-practices-for-refs) to access refs during rendering. That goes for refs holding DOM nodes as well. During the first render, the DOM nodes have not yet been created, so `ref.current` will be `null`. And during the rendering of updates, the DOM nodes haven't been updated yet. So it's too early to read them.

React sets `ref.current` during the commit. Before updating the DOM, React sets the affected `ref.current` values to `null`. After updating the DOM, React immediately sets them to the corresponding DOM nodes.

**Usually, you will access refs from event handlers.** If you want to do something with a ref, but there is no particular event to do it in, you might need an Effect. We will discuss effects on the next pages.
/////////////////////////////

در React، هر به‌روزرسانی به [دو مرحله](/learn/render-and-commit#step-3-react-commits-changes-to-the-dom) تقسیم می‌شود:

* در **رندر**، React اجزای شما را فراخوانی می‌کند تا بفهمد چه چیزی باید روی صفحه نمایش باشد.
* در **commit**، React تغییرات را در DOM اعمال می‌کند.

به طور کلی، [نمی‌خواهید](/learn/referencing-values-with-refs#best-practices-for-refs) در طول رندر به refs دسترسی داشته باشید. این برای refs نگه‌داری عناصر DOM نیز صادق است. در طول رندر اول، عناصر DOM هنوز ایجاد نشده‌اند، بنابراین `ref.current` برابر `null` خواهد بود. و در طول رندر به‌روزرسانی‌ها، عناصر DOM هنوز به‌روزرسانی نشده‌اند. بنابراین، زمانی که می‌خواهید آن‌ها را بخوانید، خیلی زود است.

React در commit، `ref.current` را تنظیم می‌کند. قبل از به‌روزرسانی DOM، React مقادیر `ref.current` را تحت تأثیر قرار داده را به `null` تنظیم می‌کند. پس از به‌روزرسانی DOM، React آن‌ها را به عناصر DOM مربوطه تنظیم می‌کند.

**معمولاً، شما از event handlers به refs دسترسی خواهید داشت.** اگر می‌خواهید کاری با یک ref انجام دهید، اما هیچ رویداد خاصی برای انجام آن وجود ندارد، شاید نیاز به یک Effect داشته باشید. ما در صفحات بعدی درباره Effects صحبت خواهیم کرد.


<DeepDive>
#### همگام‌سازی به‌روزرسانی‌های state با flushSync {/*flushing-state-updates-synchronously-with-flush-sync*/}

Consider code like this, which adds a new todo and scrolls the screen down to the last child of the list. Notice how, for some reason, it always scrolls to the todo that was *just before* the last added one:
////////////////////////

کدی را مانند زیر در نظر بگیرید که یک todo جدید اضافه می‌کند و صفحه را به پایین می‌کشاند تا آخرین فرزند لیست را نشان دهد. توجه کنید که به دلیلی، همیشه به todo قبل از آخرین todo اضافه شده می‌رود:

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    setText('');
    setTodos([ ...todos, newTodo]);
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

</Sandpack>

The issue is with these two lines:
مشکل با این دو خط کد است:

```js
setTodos([ ...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```

In React, [state updates are queued.](/learn/queueing-a-series-of-state-updates) Usually, this is what you want. However, here it causes a problem because `setTodos` does not immediately update the DOM. So the time you scroll the list to its last element, the todo has not yet been added. This is why scrolling always "lags behind" by one item.
//////////////
در React، [به‌روزرسانی‌های state در صف قرار می‌گیرند.](/learn/queueing-a-series-of-state-updates) به طور معمول، این همان چیزی است که می‌خواهید. با این حال، در اینجا مشکلی ایجاد می‌شود زیرا `setTodos` DOM را به‌صورت فوری به‌روز نمی‌کند. بنابراین، زمانی که لیست را به آخرین عنصر آن پیمایش می‌دهید، todo هنوز اضافه نشده است. به همین دلیل، پیمایش همیشه "یک مورد عقب" است.

To fix this issue, you can force React to update ("flush") the DOM synchronously. To do this, import `flushSync` from `react-dom` and **wrap the state update** into a `flushSync` call:
///////////////
برای رفع این مشکل، می‌توانید React را مجبور به به‌روزرسانی ("flush") DOM به‌صورت همزمان کنید. برای این کار، `flushSync` را از `react-dom` وارد کرده و **به‌روزرسانی state را** درون یک تماس `flushSync` بگذارید:

```js
flushSync(() => {
  setTodos([ ...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```

This will instruct React to update the DOM synchronously right after the code wrapped in `flushSync` executes. As a result, the last todo will already be in the DOM by the time you try to scroll to it:
//////////////////////////////
این دستور به React می‌گوید که بلافاصله پس از اجرای کد درون `flushSync` DOM را به‌صورت همزمان به‌روزرسانی کند. به‌عبارت دیگر، آخرین todo تا زمانی که سعی در پیمایش به آن دارید، در DOM قرار دارد:


<Sandpack>

```js
import { useState, useRef } from 'react';
import { flushSync } from 'react-dom';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    flushSync(() => {
      setText('');
      setTodos([ ...todos, newTodo]);      
    });
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

</Sandpack>

</DeepDive>

## بهترین روش‌های کنترل DOM با refs {/*best-practices-for-dom-manipulation-with-refs*/}

Refs are an escape hatch. You should only use them when you have to "step outside React". Common examples of this include managing focus, scroll position, or calling browser APIs that React does not expose.

If you stick to non-destructive actions like focusing and scrolling, you shouldn't encounter any problems. However, if you try to **modify** the DOM manually, you can risk conflicting with the changes React is making.

To illustrate this problem, this example includes a welcome message and two buttons. The first button toggles its presence using [conditional rendering](/learn/conditional-rendering) and [state](/learn/state-a-components-memory), as you would usually do in React. The second button uses the [`remove()` DOM API](https://developer.mozilla.org/en-US/docs/Web/API/Element/remove) to forcefully remove it from the DOM outside of React's control.

Try pressing "Toggle with setState" a few times. The message should disappear and appear again. Then press "Remove from the DOM". This will forcefully remove it. Finally, press "Toggle with setState":
/////////////////
Refs یک دروازه فرار هستند. شما باید آن‌ها را فقط زمانی استفاده کنید که باید "از React خارج شوید". مثال‌های معمول از این قبیل مدیریت فوکوس، موقعیت اسکرول یا فراخوانی API‌های مرورگر هستند که React آن‌ها را ارائه نمی‌دهد.

اگر به اعمال غیرمخرب مانند فوکوس و اسکرول عمل کنید، نباید با هیچ مشکلی مواجه شوید. با این حال، اگر سعی کنید به‌صورت دستی DOM را **تغییر** دهید، ممکن است با تغییراتی که React ایجاد می‌کند، درگیر شوید.

برای توضیح این مشکل، این مثال شامل یک پیام خوش‌آمدگویی و دو دکمه است. دکمه اول با استفاده از [رندرینگ شرطی](/learn/conditional-rendering) و [state](/learn/state-a-components-memory)، به همان شکلی که معمولاً در React انجام می‌دهید، حضور خود را تغییر می‌دهد. دکمه دوم از API [`remove()` DOM](https://developer.mozilla.org/en-US/docs/Web/API/Element/remove) برای حذف آن به‌صورت اجباری از DOM خارج از کنترل React استفاده می‌کند.

چند بار روی "Toggle with setState" کلیک کنید. پیام باید ناپدید شود و دوباره ظاهر شود. سپس روی "Remove from the DOM" کلیک کنید. این کار آن را به‌صورت اجباری حذف می‌کند. در نهایت، روی "Toggle with setState" کلیک کنید:

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Counter() {
  const [show, setShow] = useState(true);
  const ref = useRef(null);

  return (
    <div>
      <button
        onClick={() => {
          setShow(!show);
        }}>
        Toggle with setState
      </button>
      <button
        onClick={() => {
          ref.current.remove();
        }}>
        Remove from the DOM
      </button>
      {show && <p ref={ref}>Hello world</p>}
    </div>
  );
}
```

```css
p,
button {
  display: block;
  margin: 10px;
}
```

</Sandpack>

After you've manually removed the DOM element, trying to use `setState` to show it again will lead to a crash. This is because you've changed the DOM, and React doesn't know how to continue managing it correctly.

**Avoid changing DOM nodes managed by React.** Modifying, adding children to, or removing children from elements that are managed by React can lead to inconsistent visual results or crashes like above.

However, this doesn't mean that you can't do it at all. It requires caution. **You can safely modify parts of the DOM that React has _no reason_ to update.** For example, if some `<div>` is always empty in the JSX, React won't have a reason to touch its children list. Therefore, it is safe to manually add or remove elements there.
///////////////////////////////
پس از حذف دستی عنصر DOM، سعی در استفاده از `setState` برای نمایش آن مجدداً، منجر به خطا خواهد شد. این به دلیل تغییر DOM است و React نمی‌داند چگونه ادامهٔ مدیریت آن را به درستی انجام دهد.

**از تغییر عناصر DOM که توسط React مدیریت می‌شوند، خودداری کنید.** اصلاح، اضافه کردن فرزندان به یا حذف فرزندان از عناصری که توسط React مدیریت می‌شوند، می‌تواند منجر به نتایج بصری نامنظم یا خطاهایی مانند خطاهای فوق شود.

با این حال، این بدان معنا نیست که به هیچ وجه نمی‌توانید این کار را انجام دهید. این نیاز به احتیاط دارد. **شما می‌توانید بخش‌هایی از DOM را که React هیچ دلیلی برای به‌روزرسانی آن‌ها ندارد، به‌صورت ایمن تغییر دهید.** به عنوان مثال، اگر `<div>`ی خالی همیشه در JSX وجود دارد، React دلیلی برای لمس لیست فرزندان آن ندارد. بنابراین، اضافه کردن یا حذف عناصر در آنجا به‌صورت دستی، ایمن است.

<Recap>

- Refs are a generic concept, but most often you'll use them to hold DOM elements.
- You instruct React to put a DOM node into `myRef.current` by passing `<div ref={myRef}>`.
- Usually, you will use refs for non-destructive actions like focusing, scrolling, or measuring DOM elements.
- A component doesn't expose its DOM nodes by default. You can opt into exposing a DOM node by using `forwardRef` and passing the second `ref` argument down to a specific node.
- Avoid changing DOM nodes managed by React.
- If you do modify DOM nodes managed by React, modify parts that React has no reason to update.
////////////////////
- Ref ها یک مفهوم کلی هستند، اما بیشتر اوقات از آن‌ها برای نگه‌داری عناصر DOM استفاده می‌شود.
- شما با گذراندن `<div ref={myRef}>` به React دستور می‌دهید تا یک عنصر DOM را در `myRef.current` قرار دهد.
- معمولاً از Ref ها برای اعمال غیرمخرب مانند فوکوس، اسکرول یا اندازه‌گیری عناصر DOM استفاده می‌شود.
- یک کامپوننت به طور پیش‌فرض عناصر DOM خود را نشان نمی‌دهد. شما می‌توانید با استفاده از `forwardRef` و انتقال دادن دومین آرگومان `ref` به یک عنصر خاص، به نمایش گذاشتن یک عنصر DOM پیوست کنید.
- از تغییر عناصر DOM که توسط React مدیریت می‌شوند، خودداری کنید.
- اگر شما عناصر DOM که توسط React مدیریت می‌شوند را تغییر دهید، بخش‌هایی را که React هیچ دلیلی برای به‌روزرسانی آن‌ها ندارد، تغییر دهید.
</Recap>



<Challenges>

#### Play and pause the video {/*play-and-pause-the-video*/}

In this example, the button toggles a state variable to switch between a playing and a paused state. However, in order to actually play or pause the video, toggling state is not enough. You also need to call [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) and [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) on the DOM element for the `<video>`. Add a ref to it, and make the button work.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video width="250">
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

For an extra challenge, keep the "Play" button in sync with whether the video is playing even if the user right-clicks the video and plays it using the built-in browser media controls. You might want to listen to `onPlay` and `onPause` on the video to do that.

<Solution>

Declare a ref and put it on the `<video>` element. Then call `ref.current.play()` and `ref.current.pause()` in the event handler depending on the next state.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);
  const ref = useRef(null);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);

    if (nextIsPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video
        width="250"
        ref={ref}
        onPlay={() => setIsPlaying(true)}
        onPause={() => setIsPlaying(false)}
      >
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

In order to handle the built-in browser controls, you can add `onPlay` and `onPause` handlers to the `<video>` element and call `setIsPlaying` from them. This way, if the user plays the video using the browser controls, the state will adjust accordingly.

</Solution>

#### Focus the search field {/*focus-the-search-field*/}

Make it so that clicking the "Search" button puts focus into the field.

<Sandpack>

```js
export default function Page() {
  return (
    <>
      <nav>
        <button>Search</button>
      </nav>
      <input
        placeholder="Looking for something?"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

Add a ref to the input, and call `focus()` on the DOM node to focus it:

<Sandpack>

```js
import { useRef } from 'react';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <button onClick={() => {
          inputRef.current.focus();
        }}>
          Search
        </button>
      </nav>
      <input
        ref={inputRef}
        placeholder="Looking for something?"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

</Solution>

#### Scrolling an image carousel {/*scrolling-an-image-carousel*/}

This image carousel has a "Next" button that switches the active image. Make the gallery scroll horizontally to the active image on click. You will want to call [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) on the DOM node of the active image:

```js
node.scrollIntoView({
  behavior: 'smooth',
  block: 'nearest',
  inline: 'center'
});
```

<Hint>

You don't need to have a ref to every image for this exercise. It should be enough to have a ref to the currently active image, or to the list itself. Use `flushSync` to ensure the DOM is updated *before* you scroll.

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function CatFriends() {
  const [index, setIndex] = useState(0);
  return (
    <>
      <nav>
        <button onClick={() => {
          if (index < catList.length - 1) {
            setIndex(index + 1);
          } else {
            setIndex(0);
          }
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li key={cat.id}>
              <img
                className={
                  index === i ?
                    'active' :
                    ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}

img {
  padding: 10px;
  margin: -10px;
  transition: background 0.2s linear;
}

.active {
  background: rgba(0, 100, 150, 0.4);
}
```

</Sandpack>

<Solution>

You can declare a `selectedRef`, and then pass it conditionally only to the current image:

```js
<li ref={index === i ? selectedRef : null}>
```

When `index === i`, meaning that the image is the selected one, the `<li>` will receive the `selectedRef`. React will make sure that `selectedRef.current` always points at the correct DOM node.

Note that the `flushSync` call is necessary to force React to update the DOM before the scroll. Otherwise, `selectedRef.current` would always point at the previously selected item.

<Sandpack>

```js
import { useRef, useState } from 'react';
import { flushSync } from 'react-dom';

export default function CatFriends() {
  const selectedRef = useRef(null);
  const [index, setIndex] = useState(0);

  return (
    <>
      <nav>
        <button onClick={() => {
          flushSync(() => {
            if (index < catList.length - 1) {
              setIndex(index + 1);
            } else {
              setIndex(0);
            }
          });
          selectedRef.current.scrollIntoView({
            behavior: 'smooth',
            block: 'nearest',
            inline: 'center'
          });            
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li
              key={cat.id}
              ref={index === i ?
                selectedRef :
                null
              }
            >
              <img
                className={
                  index === i ?
                    'active'
                    : ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}

img {
  padding: 10px;
  margin: -10px;
  transition: background 0.2s linear;
}

.active {
  background: rgba(0, 100, 150, 0.4);
}
```

</Sandpack>

</Solution>

#### Focus the search field with separate components {/*focus-the-search-field-with-separate-components*/}

Make it so that clicking the "Search" button puts focus into the field. Note that each component is defined in a separate file and shouldn't be moved out of it. How do you connect them together?

<Hint>

You'll need `forwardRef` to opt into exposing a DOM node from your own component like `SearchInput`.

</Hint>

<Sandpack>

```js App.js
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  return (
    <>
      <nav>
        <SearchButton />
      </nav>
      <SearchInput />
    </>
  );
}
```

```js SearchButton.js
export default function SearchButton() {
  return (
    <button>
      Search
    </button>
  );
}
```

```js SearchInput.js
export default function SearchInput() {
  return (
    <input
      placeholder="Looking for something?"
    />
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

You'll need to add an `onClick` prop to the `SearchButton`, and make the `SearchButton` pass it down to the browser `<button>`. You'll also pass a ref down to `<SearchInput>`, which will forward it to the real `<input>` and populate it. Finally, in the click handler, you'll call `focus` on the DOM node stored inside that ref.

<Sandpack>

```js App.js
import { useRef } from 'react';
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <SearchButton onClick={() => {
          inputRef.current.focus();
        }} />
      </nav>
      <SearchInput ref={inputRef} />
    </>
  );
}
```

```js SearchButton.js
export default function SearchButton({ onClick }) {
  return (
    <button onClick={onClick}>
      Search
    </button>
  );
}
```

```js SearchInput.js
import { forwardRef } from 'react';

export default forwardRef(
  function SearchInput(props, ref) {
    return (
      <input
        ref={ref}
        placeholder="Looking for something?"
      />
    );
  }
);
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

</Solution>

</Challenges>
