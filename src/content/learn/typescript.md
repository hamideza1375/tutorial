---
title: Using TypeScript
re: https://github.com/reactjs/react.dev/issues/5960
---

<Intro>

TypeScript is a popular way to add type definitions to JavaScript codebases. Out of the box, TypeScript [supports JSX](/learn/writing-markup-with-jsx) and you can get full React Web support by adding [`@types/react`](https://www.npmjs.com/package/@types/react) and [`@types/react-dom`](https://www.npmjs.com/package/@types/react-dom) to your project.

//////////////////////

TypeScript یک روش محبوب برای افزودن تعاریف نوع به پایگاه های کد جاوا اسکریپت است. خارج از جعبه، TypeScript [پشتیبانی از JSX](/learn/writing-markup-with-jsx) و می توانید با افزودن [`@types/react`] پشتیبانی کامل React Web را دریافت کنید (https://www.npmjs.com /package/@types/react) و [`@types/react-dom`](https://www.npmjs.com/package/@types/react-dom) به پروژه شما.

</Intro>

<YouWillLearn>

* [TypeScript with React Components](/learn/typescript#typescript-with-react-components)
* [Examples of typing with hooks](/learn/typescript#example-hooks)
* [Common types from `@types/react`](/learn/typescript/#useful-types)
* [Further learning locations](/learn/typescript/#further-learning)

////////////////

* [TypeScript با React Components](/learn/typescript#typescript-with-react-components)
* [نمونه هایی از تایپ با قلاب](/learn/typescript#example-hooks)
* [انواع رایج از «@types/react»](/learn/typescript/#useful-types)
* [محل یادگیری بیشتر] (/learn/typescript/#further-learning)

</YouWillLearn>

## نصب و راه اندازی {/*installation*/}

All [production-grade React frameworks](/learn/start-a-new-react-project#production-grade-react-frameworks) offer support for using TypeScript. Follow the framework specific guide for installation:

- [Next.js](https://nextjs.org/docs/pages/building-your-application/configuring/typescript)
- [Remix](https://remix.run/docs/en/1.19.2/guides/typescript)
- [Gatsby](https://www.gatsbyjs.com/docs/how-to/custom-configuration/typescript/)
- [Expo](https://docs.expo.dev/guides/typescript/)

///////////////////////////

همه [چارچوب‌های React درجه تولید](/learn/start-a-new-react-project#production-grade-react-frameworks) برای استفاده از TypeScript پشتیبانی می‌کنند. راهنمای خاص چارچوب را برای نصب دنبال کنید:

- [Next.js](https://nextjs.org/docs/pages/building-your-application/configuring/typescript)
- [ریمیکس](https://remix.run/docs/en/1.19.2/guides/typescript)
- [گتسبی] (https://www.gatsbyjs.com/docs/how-to/custom-configuration/typescript/)
- [Expo](https://docs.expo.dev/guides/typescript/)

### افزودن TypeScript به یک پروژه React موجود {/*adding-typescript-to-an-existing-react-project*/}

To install the latest version of React's type definitions:

/////////////////

برای نصب آخرین نسخه از تعاریف نوع React:

<TerminalBlock>
npm install @types/react @types/react-dom
</TerminalBlock>

The following compiler options need to be set in your `tsconfig.json`:

///////////////////

گزینه های کامپایلر زیر باید در «tsconfig.json» شما تنظیم شوند

1. `dom` must be included in [`lib`](https://www.typescriptlang.org/tsconfig/#lib) (Note: If no `lib` option is specified, `dom` is included by default).
1. [`jsx`](https://www.typescriptlang.org/tsconfig/#jsx) must be set to one of the valid options. `preserve` should suffice for most applications.
  If you're publishing a library, consult the [`jsx` documentation](https://www.typescriptlang.org/tsconfig/#jsx) on what value to choose.

  ///////////////////

  1. «dom» باید در [«lib»] (https://www.typescriptlang.org/tsconfig/#lib) گنجانده شود (توجه: اگر گزینه «lib» مشخص نشده باشد، «dom» به طور پیش‌فرض گنجانده شده است) .
1. [`jsx`](https://www.typescriptlang.org/tsconfig/#jsx) باید روی یکی از گزینه های معتبر تنظیم شود. "حفظ" باید برای اکثر برنامه ها کافی باشد.
  اگر در حال انتشار یک کتابخانه هستید، به [`jsx` مستندات] (https://www.typescriptlang.org/tsconfig/#jsx) در مورد مقداری که باید انتخاب کنید، مراجعه کنید.

## TypeScript with React Components {/*typescript-with-react-components*/}

<Note>

هر فایل حاوی JSX باید از پسوند فایل «.tsx» استفاده کند. این یک پسوند خاص TypeScript است که به TypeScript می گوید که این فایل حاوی JSX است.

</Note>

با توجه به متنی که ارسال کردید، به نظر می‌رسد که شما در مورد نحوه‌ی نوشتن کد با استفاده از TypeScript در React صحبت می‌کنید. نوشتن کد با TypeScript در React بسیار شبیه به نوشتن کد با JavaScript در React است. اما تفاوت اصلی در کار با کامپوننت‌ها این است که شما می‌توانید انواع داده‌ای برای props کامپوننت خود تعریف کنید. این انواع داده‌ای می‌توانند برای بررسی صحت و ارائه‌ی مستندات درون خطی در ویرایشگرها استفاده شوند¹²³.

برای استفاده از TypeScript در React، شما باید ابتدا پلاگین TypeScript را در VSCode نصب کنید. برای این کار، به بخش Extensions در VSCode بروید و TypeScript را جستجو کنید. سپس بر روی گزینه‌ی Install کلیک کنید¹²³.


 گرفتن [`MyButton` component](/learn#components) از [Quick Start](/learn) راهنمای، می‌توانیم یک نوع توصیف کننده «عنوان» برای دکمه اضافه کنیم:

<Sandpack>

```tsx App.tsx active
function MyButton({ title }: { title: string }) {
  return (
    <button>{title}</button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton title="I'm a button" />
    </div>
  );
}
```

```js App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```
</Sandpack>

 <Note>

These sandboxes can handle TypeScript code, but they do not run the type-checker. This means you can amend the TypeScript sandboxes to learn, but you won't get any type errors or warnings. To get type-checking, you can use the [TypeScript Playground](https://www.typescriptlang.org/play) or use a more fully-featured online sandbox.

//////////////////////

. در این متن به sandbox های TypeScript اشاره شده است. این sandbox ها قادر به اجرای کد TypeScript هستند، اما type-checker را اجرا نمی کنند. این بدان معنی است که شما می توانید sandbox های TypeScript را برای یادگیری ویرایش کنید، اما هیچ خطا یا هشداری دریافت نخواهید کرد. برای دریافت type-checking، می توانید از TypeScript Playground استفاده کنید یا از sandbox آنلاین با قابلیت های کامل تر استفاده کنید.

</Note>

This inline syntax is the simplest way to provide types for a component, though once you start to have a few fields to describe it can become unwieldy. Instead, you can use an `interface` or `type` to describe the component's props:

////////////////

این نحوی از تعریف نوع برای یک کامپوننت، ساده‌ترین روش است، اما هنگامی که تعداد فیلدهای آن زیاد می‌شود، نامناسب می‌شود. به جای آن، می‌توانید از یک interface یا type برای توصیف props کامپوننت استفاده کنید1
<Sandpack>

```tsx App.tsx active
interface MyButtonProps {
  /** The text to display inside the button */
  title: string;
  /** Whether the button can be interacted with */
  disabled: boolean;
}

function MyButton({ title, disabled }: MyButtonProps) {
  return (
    <button disabled={disabled}>{title}</button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton title="I'm a disabled button" disabled={true}/>
    </div>
  );
}
```

```js App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>


<Sandpack>

```js
interface Props {
  name: string;
  age: number;
}

function MyComponent(props: Props) {
  return (
    <div>
      <p>{props.name}</p>
      <p>{props.age}</p>
    </div>
  );
}
```

</Sandpack>

The type describing your component's props can be as simple or as complex as you need, though they should be an object type described with either a `type` or `interface`. You can learn about how TypeScript describes objects in [Object Types](https://www.typescriptlang.org/docs/handbook/2/objects.html) but you may also be interested in using [Union Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types) to describe a prop that can be one of a few different types and the [Creating Types from Types](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html) guide for more advanced use cases.

////////////////

نوعی که props کامپوننت شما را توصیف می‌کند، می‌تواند به هر سادگی یا پیچیدگی که نیاز دارید، باشد، اما باید یک object type با یک type یا interface توصیف شود. شما می‌توانید در مورد نحوه توصیف TypeScript برای اشیاء در Object Types بیاموزید، اما ممکن است به استفاده از Union Types برای توصیف یک prop که می‌تواند یکی از چندین نوع مختلف باشد و راهنمای Creating Types from Types برای موارد استفاده پیشرفته‌تر علاقه‌مند باشید123

## Example Hooks {/*example-hooks*/}

The type definitions from `@types/react` include types for the built-in hooks, so you can use them in your components without any additional setup. They are built to take into account the code you write in your component, so you will get [inferred types](https://www.typescriptlang.org/docs/handbook/type-inference.html) a lot of the time and ideally do not need to handle the minutiae of providing the types. 

However, we can look at a few examples of how to provide types for hooks.

//////////////////////////////

تعریف‌های نوع از @types/react شامل تعریف‌های نوع برای hooks داخلی هستند، بنابراین می‌توانید از آن‌ها در کامپوننت‌های خود بدون نیاز به هیچ تنظیمات اضافی استفاده کنید. آن‌ها برای در نظر گرفتن کدی که در کامپوننت خود می‌نویسید ساخته شده‌اند، بنابراین بسیاری از زمان‌ها نوع‌های استنباطی دریافت خواهید کرد و بهتر است که جزئیات ارائه نوع را بررسی نکنید123

### `useState` {/*typing-usestate*/}

The [`useState` hook](/reference/react/useState) will re-use the value passed in as the initial state to determine what the type of the value should be. For example:

//////////////////////////

قلاب ['useState'](/reference/react/useState) از مقدار ارسال شده به عنوان حالت اولیه برای تعیین نوع مقدار استفاده می کند. مثلا:

```ts
// Infer the type as "boolean"
const [enabled, setEnabled] = useState(false);
```

این کد به متغیر `enabled` نوع داده‌ی `boolean` را اختصاص می‌دهد و تابع `setEnabled` یک تابع است که یک آرگومان `boolean` یا یک تابعی که یک `boolean` را برمی‌گرداند را می‌پذیرد. اگر می‌خواهید به صورت صریح نوع state را مشخص کنید، می‌توانید با ارائه یک آرگومان نوع به فراخوانی `useState`، این کار را انجام دهید:

```ts 
// نوع را به صورت صریح "boolean" تنظیم کنید
const [enabled, setEnabled] = useState<boolean>(false);
```

این متن در این مورد خیلی مفید نیست، اما یک مورد رایج که ممکن است بخواهید نوعی را ارائه دهید زمانی است که شما یک نوع اتحادی دارید. به عنوان مثال، `status` در اینجا می‌تواند یکی از چند رشته مختلف باشد:

```ts
type Status = "idle" | "loading" | "success" | "error";

const [status, setStatus] = useState<Status>("idle");
```


شما می‌توانید، همانطور که در [اصول ساختاردهی state](/learn/choosing-the-state-structure#principles-for-structuring-state) توصیه شده است، وضعیت‌های مرتبط را به صورت یک شیء گروه‌بندی کنید و با استفاده از انواع شیء، انواع مختلف را شرح دهید:

```ts
type RequestState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success', data: any }
  | { status: 'error', error: Error };

const [requestState, setRequestState] = useState<RequestState>({ status: 'idle' });
```

### `useReducer` {/*typing-usereducer*/}

The [`useReducer` hook](/reference/react/useReducer) is a more complex hook that takes a reducer function and an initial state. The types for the reducer function are inferred from the initial state. You can optionally provide a type argument to the `useReducer` call to provide a type for the state, but it is often better to set the type on the initial state instead:

<Sandpack>

```tsx App.tsx active
import {useReducer} from 'react';

interface State {
   count: number 
};

type CounterAction =
  | { type: "reset" }
  | { type: "setCount"; value: State["count"] }

const initialState: State = { count: 0 };

function stateReducer(state: State, action: CounterAction): State {
  switch (action.type) {
    case "reset":
      return initialState;
    case "setCount":
      return { ...state, count: action.value };
    default:
      throw new Error("Unknown action");
  }
}

export default function App() {
  const [state, dispatch] = useReducer(stateReducer, initialState);

  const addFive = () => dispatch({ type: "setCount", value: state.count + 5 });
  const reset = () => dispatch({ type: "reset" });

  return (
    <div>
      <h1>Welcome to my counter</h1>

      <p>Count: {state.count}</p>
      <button onClick={addFive}>Add 5</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

```

```js App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>


We are using TypeScript in a few key places:

 - `interface State` describes the shape of the reducer's state.
 - `type CounterAction` describes the different actions which can be dispatched to the reducer.
 - `const initialState: State` provides a type for the initial state, and also the type which is used by `useReducer` by default.
 - `stateReducer(state: State, action: CounterAction): State` sets the types for the reducer function's arguments and return value.

A more explicit alternative to setting the type on `initialState` is to provide a type argument to `useReducer`:

```ts
import { stateReducer, State } from './your-reducer-implementation';

const initialState = { count: 0 };

export default function App() {
  const [state, dispatch] = useReducer<State>(stateReducer, initialState);
}
```

### `useContext` {/*typing-usecontext*/}

The [`useContext` hook](/reference/react/useContext) is a technique for passing data down the component tree without having to pass props through components. It is used by creating a provider component and often by creating a hook to consume the value in a child component.

The type of the value provided by the context is inferred from the value passed to the `createContext` call:

<Sandpack>

```tsx App.tsx active
import { createContext, useContext, useState } from 'react';

type Theme = "light" | "dark" | "system";
const ThemeContext = createContext<Theme>("system");

const useGetTheme = () => useContext(ThemeContext);

export default function MyApp() {
  const [theme, setTheme] = useState<Theme>('light');

  return (
    <ThemeContext.Provider value={theme}>
      <MyComponent />
    </ThemeContext.Provider>
  )
}

function MyComponent() {
  const theme = useGetTheme();

  return (
    <div>
      <p>Current theme: {theme}</p>
    </div>
  )
}
```

```js App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>

This technique works when you have a default value which makes sense - but there are occasionally cases when you do not, and in those cases `null` can feel reasonable as a default value. However, to allow the type-system to understand your code, you need to explicitly set `ContextShape | null` on the `createContext`. 

This causes the issue that you need to eliminate the `| null` in the type for context consumers. Our recommendation is to have the hook do a runtime check for it's existence and throw an error when not present:

```js {5, 16-20}
import { createContext, useContext, useState, useMemo } from 'react';

// This is a simpler example, but you can imagine a more complex object here
type ComplexObject = {
  kind: string
};

// The context is created with `| null` in the type, to accurately reflect the default value.
const Context = createContext<ComplexObject | null>(null);

// The `| null` will be removed via the check in the hook.
const useGetComplexObject = () => {
  const object = useContext(Context);
  if (!object) { throw new Error("useGetComplexObject must be used within a Provider") }
  return object;
}

export default function MyApp() {
  const object = useMemo(() => ({ kind: "complex" }), []);

  return (
    <Context.Provider value={object}>
      <MyComponent />
    </Context.Provider>
  )
}

function MyComponent() {
  const object = useGetComplexObject();

  return (
    <div>
      <p>Current object: {object.kind}</p>
    </div>
  )
}
```

### `useMemo` {/*typing-usememo*/}

The [`useMemo`](/reference/react/useMemo) hooks will create/re-access a memorized value from a function call, re-running the function only when dependencies passed as the 2nd parameter are changed. The result of calling the hook is inferred from the return value from the function in the first parameter. You can be more explicit by providing a type argument to the hook.

```ts
// The type of visibleTodos is inferred from the return value of filterTodos
const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
```


### `useCallback` {/*typing-usecallback*/}

The [`useCallback`](/reference/react/useCallback) provide a stable reference to a function as long as the dependencies passed into the second parameter are the same. Like `useMemo`, the function's type is inferred from the return value of the function in the first parameter, and you can be more explicit by providing a type argument to the hook.


```ts
const handleClick = useCallback(() => {
  // ...
}, [todos]);
```

When working in TypeScript strict mode `useCallback` requires adding types for the parameters in your callback. This is because the type of the callback is inferred from the return value of the function, and without parameters the type cannot be fully understood.

Depending on your code-style preferences, you could use the `*EventHandler` functions from the React types to provide the type for the event handler at the same time as defining the callback: 

```ts
import { useState, useCallback } from 'react';

export default function Form() {
  const [value, setValue] = useState("Change me");

  const handleChange = useCallback<React.ChangeEventHandler<HTMLInputElement>>((event) => {
    setValue(event.currentTarget.value);
  }, [setValue])
  
  return (
    <>
      <input value={value} onChange={handleChange} />
      <p>Value: {value}</p>
    </>
  );
}
```

## Useful Types( انواع مفید) {/*useful-types*/}

There is quite an expansive set of types which come from the `@types/react` package, it is worth a read when you feel comfortable with how React and TypeScript interact. You can find them [in React's folder in DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/react/index.d.ts). We will cover a few of the more common types here.

//////////////////

بسته @types/react شامل مجموعه‌ای بسیار گسترده از انواع داده‌ای است که ارزش خواندن آن را دارد، زمانی که با نحوه تعامل React و TypeScript آشنا شده‌اید. شما می‌توانید آن‌ها را در پوشه React در DefinitelyTyped پیدا کنید. در اینجا، چند نوع متداول را پوشش می‌دهیم.

### DOM Events {/*typing-dom-events*/}

When working with DOM events in React, the type of the event can often be inferred from the event handler. However, when you want to extract a function to be passed to an event handler, you will need to explicitly set the type of the event.

///////////////

زمانی که با رویدادهای DOM در React کار می‌کنید، نوع رویداد اغلب از مدیرت کننده رویداد استخراج می‌شود. با این حال، زمانی که می‌خواهید یک تابع را برای انتقال به یک مدیرت کننده رویداد استخراج کنید، باید به صورت صریح نوع رویداد را تنظیم کنید.

<Sandpack>

```tsx App.tsx active
import { useState } from 'react';

export default function Form() {
  const [value, setValue] = useState("Change me");

  function handleChange(event: React.ChangeEvent<HTMLInputElement>) {
    setValue(event.currentTarget.value);
  }

  return (
    <>
      <input value={value} onChange={handleChange} />
      <p>Value: {value}</p>
    </>
  );
}
```

```js App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>

There are many types of events provided in the React types - the full list can be found [here](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/b580df54c0819ec9df62b0835a315dd48b8594a9/types/react/index.d.ts#L1247C1-L1373) which is based on the [most popular events from the DOM](https://developer.mozilla.org/en-US/docs/Web/Events).

When determining the type you are looking for you can first look at the hover information for the event handler you are using, which will show the type of the event.

If you need to use an event that is not included in this list, you can use the `React.SyntheticEvent` type, which is the base type for all events.

/////////////////

تعداد زیادی از انواع رویدادها در انواع React ارائه شده‌اند - لیست کامل آن‌ها را می‌توانید در اینجا پیدا کنید که بر اساس محبوب‌ترین رویدادهای DOM است.

زمانی که نوعی را که به دنبال آن هستید تعیین می‌کنید، می‌توانید ابتدا به اطلاعات هوور برای دستگیرنده رویدادی که استفاده می‌کنید نگاه کنید، که نوع رویداد را نشان می‌دهد.

اگر نیاز به استفاده از یک رویدادی دارید که در این لیست وجود ندارد، می‌توانید از نوع React.SyntheticEvent استفاده کنید که نوع پایه برای همه رویدادها است.

### Children {/*typing-children*/}

There are two common paths to describing the children of a component. The first is to use the `React.ReactNode` type, which is a union of all the possible types that can be passed as children in JSX:

//////////

دو مسیر مشترک برای توصیف فرزندان یک جزء وجود دارد. اولین مورد استفاده از نوع «React.ReactNode» است که ترکیبی از همه انواع ممکن است که می‌توان به عنوان کودک در JSX منتقل کرد:

```ts
interface ModalRendererProps {
  title: string;
  children: React.ReactNode;
}
```

This is a very broad definition of children. The second is to use the `React.ReactElement` type, which is only JSX elements and not JavaScript primitives like strings or numbers:

//////////////

این جمله به معنی “این یک تعریف بسیار گسترده از کودکان است. دومین روش استفاده از نوع React.ReactElement است که تنها عناصر JSX و نه اعداد یا رشته‌های جاوااسکریپتی مانند آن‌ها را شامل می‌شود” می‌باشد. کد بالا یک interface به نام ModalRendererProps تعریف می‌کند که دارای دو property به نام‌های title و children است. title یک رشته است و children یک عنصر React.ReactElement است.

```ts
interface ModalRendererProps {
  title: string;
  children: React.ReactElement;
}
```

Note, that you cannot use TypeScript to describe that the children are a certain type of JSX elements, so you cannot use the type-system to describe a component which only accepts `<li>` children. 

You can see all an example of both `React.ReactNode` and `React.ReactElement` with the type-checker in [this TypeScript playground](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgIilQ3wChSB6CxYmAOmXRgDkIATJOdNJMGAZzgwAFpxAR+8YADswAVwGkZMJFEzpOjDKw4AFHGEEBvUnDhphwADZsi0gFw0mDWjqQBuUgF9yaCNMlENzgAXjgACjADfkctFnYkfQhDAEpQgD44AB42YAA3dKMo5P46C2tbJGkvLIpcgt9-QLi3AEEwMFCItJDMrPTTbIQ3dKywdIB5aU4kKyQQKpha8drhhIGzLLWODbNs3b3s8YAxKBQAcwXpAThMaGWDvbH0gFloGbmrgQfBzYpd1YjQZbEYARkB6zMwO2SHSAAlZlYIBCdtCRkZpHIrFYahQYQD8UYYFA5EhcfjyGYqHAXnJAsIUHlOOUbHYhMIIHJzsI0Qk4P9SLUBuRqXEXEwAKKfRZcNA8PiCfxWACecAAUgBlAAacFm80W-CU11U6h4TgwUv11yShjgJjMLMqDnN9Dilq+nh8pD8AXgCHdMrCkWisVoAet0R6fXqhWKhjKllZVVxMcavpd4Zg7U6Qaj+2hmdG4zeRF10uu-Aeq0LBfLMEe-V+T2L7zLVu+FBWLdLeq+lc7DYFf39deFVOotMCACNOCh1dq219a+30uC8YWoZsRyuEdjkevR8uvoVMdjyTWt4WiSSydXD4NqZP4AymeZE072ZzuUeZQKheQgA).

///////////////

این جمله به معنی "توجه کنید که شما نمی‌توانید از TypeScript برای توصیف کردن این استفاده کنید که فرزندان یک نوع خاصی از عناصر JSX هستند، بنابراین نمی‌توانید از سیستم نوع برای توصیف یک کامپوننت استفاده کنید که فقط فرزندان `<li>` را قبول می‌کند.

شما می‌توانید نمونه‌ای از React.ReactNode و React.ReactElement را با type-checker در این TypeScript playground مشاهده کنید.

کد بالا یک interface به نام ModalRendererProps تعریف می‌کند که دارای دو property به نام‌های title و children است. title یک رشته است و children یک عنصر React.ReactElement است.

### Style Props {/*typing-style-props*/}

When using inline styles in React, you can use `React.CSSProperties` to describe the object passed to the `style` prop. This type is a union of all the possible CSS properties, and is a good way to ensure you are passing valid CSS properties to the `style` prop, and to get auto-complete in your editor.

////////////////

هنگام استفاده از استایل های درون خطی در React، می توانید از React.CSSProperties برای توصیف شی ارسال شده به style prop استفاده کنید. این نوع ترکیبی از تمام ویژگی‌های CSS ممکن است، و راه خوبی برای اطمینان از اینکه ویژگی‌های CSS معتبر را به استایل prop ارسال می‌کنید و تکمیل خودکار در ویرایشگرتان است.
```ts
interface MyComponentProps {
  style: React.CSSProperties;
}
```

## Further learning(یادگیری بیشتر) {/*further-learning*/}

This guide has covered the basics of using TypeScript with React, but there is a lot more to learn.
Individual API pages on the docs may contain more in-depth documentation on how to use them with TypeScript.

////////////

این راهنما اصول استفاده از TypeScript با React را پوشش داده است، اما چیزهای بیشتری برای یادگیری وجود دارد.
صفحات API منفرد در اسناد ممکن است حاوی مستندات عمیق تری در مورد نحوه استفاده از آنها با TypeScript باشند.

We recommend the following resources:
ما منابع زیر را توصیه می کنیم:

 - [The TypeScript handbook](https://www.typescriptlang.org/docs/handbook/) is the official documentation for TypeScript, and covers most key language features.

 - [The TypeScript release notes](https://devblogs.microsoft.com/typescript/) covers a each new features in-depth.

 - [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/) is a community-maintained cheatsheet for using TypeScript with React, covering a lot of useful edge cases and providing more breadth than this document.

 - [TypeScript Community Discord](https://discord.com/invite/typescript) is a great place to ask questions and get help with TypeScript and React issues.
