---
title: Passing Data Deeply with Context(انتقال داده ها به صورت عمیق با Context)
---

<Intro>

Usually, you will pass information from a parent component to a child component via props. But passing props can become verbose and inconvenient if you have to pass them through many components in the middle, or if many components in your app need the same information. *Context* lets the parent component make some information available to any component in the tree below it—no matter how deep—without passing it explicitly through props.
معمولاً، شما اطلاعات را از کامپوننت والد به کامپوننت فرزند از طریق props منتقل می‌کنید. اما اگر بخواهید این props را از طریق بسیاری از کامپوننت‌ها در میان منتقل کنید یا اگر بسیاری از کامپوننت‌های برنامه شما به همان اطلاعات نیاز دارند، منتقل کردن props ممکن است طولانی و نامطلوب باشد. *Context* به کامپوننت والد اجازه می‌دهد تا برخی از اطلاعات را برای هر کامپوننت در درخت زیر آن در دسترس قرار دهد - بدون اینکه به صراحت از طریق props منتقل شود.

</Intro>

<YouWillLearn>

- What "prop drilling" is
- How to replace repetitive prop passing with context
- Common use cases for context
- Common alternatives to context

////////////////////
متن زیر، ترجمه‌ی متن انگلیسی شماست:

- چیستی "prop drilling"
- چگونگی جایگزینی انتقال تکراری props با context
- مورد استفاده‌های رایج برای context
- جایگزین‌های رایج برای context

</YouWillLearn>

## The problem with passing props(مشکل با پاس دادن prop ها) {/*the-problem-with-passing-props*/}

[Passing props](/learn/passing-props-to-a-component) is a great way to explicitly pipe data through your UI tree to the components that use it.
///////////////
ترجمه‌ی متن انگلیسی شما به فارسی:

[انتقال props](/learn/passing-props-to-a-component) یک روش عالی برای انتقال داده‌ها به صورت صریح از طریق درخت UI به کامپوننت‌هایی است که از آن استفاده می‌کنند.

But passing props can become verbose and inconvenient when you need to pass some prop deeply through the tree, or if many components need the same prop. The nearest common ancestor could be far removed from the components that need data, and [lifting state up](/learn/sharing-state-between-components) that high can lead to a situation called "prop drilling".
///////////////////////
اما اگر نیاز داشته باشید که یک prop را به صورت عمیق در درخت منتقل کنید یا اگر بسیاری از کامپوننت‌ها به همان prop نیاز داشته باشند، انتقال props ممکن است طولانی و نامطلوب باشد. نزدیک‌ترین پدر مشترک ممکن است از کامپوننت‌هایی که به داده نیاز دارند، دور باشد و [بالابردن حالت](/learn/sharing-state-between-components) به این اندازه می‌تواند منجر به وضعیتی به نام "prop drilling" شود.

<DiagramGroup>

<Diagram name="passing_data_lifting_state" height={160} width={608} captionPosition="top" alt="Diagram with a tree of three components. The parent contains a bubble representing a value highlighted in purple. The value flows down to each of the two children, both highlighted in purple." >

Lifting state up

</Diagram>
<Diagram name="passing_data_prop_drilling" height={430} width={608} captionPosition="top" alt="Diagram with a tree of ten nodes, each node with two children or less. The root node contains a bubble representing a value highlighted in purple. The value flows down through the two children, each of which pass the value but do not contain it. The left child passes the value down to two children which are both highlighted purple. The right child of the root passes the value through to one of its two children - the right one, which is highlighted purple. That child passed the value through its single child, which passes it down to both of its two children, which are highlighted purple.">

Prop drilling

</Diagram>

</DiagramGroup>

Wouldn't it be great if there were a way to "teleport" data to the components in the tree that need it without passing props? With React's context feature, there is!
/////////
آیا اگر راهی برای "تله‌پورت" داده‌ها به کامپوننت‌های در درخت که به آن‌ها نیاز دارند بدون انتقال props وجود داشت، عالی نبود؟ با ویژگی context React، این امکان وجود دا

## Context: an alternative to passing props(Context: جایگزینی برای پاس دادن پراپ ها) {/*context-an-alternative-to-passing-props*/}

Context lets a parent component provide data to the entire tree below it. There are many uses for context. Here is one example. Consider this `Heading` component that accepts a `level` for its size:
//////////////////
با استفاده از ویژگی context React، به کامپوننت والد اجازه می‌دهد تا اطلاعات را برای کل درخت زیر آن در دسترس قرار دهد. برای context کاربردهای متعددی وجود دارد. یک مثال را در نظر بگیرید. این کامپوننت `Heading` که یک `level` را برای اندازه خود قبول می‌کند را در نظر بگیرید:

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Heading level={2}>Heading</Heading>
      <Heading level={3}>Sub-heading</Heading>
      <Heading level={4}>Sub-sub-heading</Heading>
      <Heading level={5}>Sub-sub-sub-heading</Heading>
      <Heading level={6}>Sub-sub-sub-sub-heading</Heading>
    </Section>
  );
}
```

```js Section.js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

```js Heading.js
export default function Heading({ level, children }) {
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

Let's say you want multiple headings within the same `Section` to always have the same size:
بیایید فرض کنیم که می‌خواهید چندین عنوان در داخل همان `Section` همیشه اندازه یکسانی داشته باشند:

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Section>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Section>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Section>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```js Section.js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

```js Heading.js
export default function Heading({ level, children }) {
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

Currently, you pass the `level` prop to each `<Heading>` separately:
در حال حاضر، شما level را به هر `<Heading>` به صورت جداگانه منتقل می‌کنید:

```js
<Section>
  <Heading level={3}>About</Heading>
  <Heading level={3}>Photos</Heading>
  <Heading level={3}>Videos</Heading>
</Section>
```

It would be nice if you could pass the `level` prop to the `<Section>` component instead and remove it from the `<Heading>`. This way you could enforce that all headings in the same section have the same size:
/////////////////////
بله، اگر `level` را به کامپوننت `<Section>` منتقل کنید و آن را از `<Heading>` حذف کنید، خوب خواهد بود. به این ترتیب می‌توانید اجبار کنید که همه عناوین در یک بخش اندازه یکسانی داشته باشند.

```js
<Section level={3}>
  <Heading>About</Heading>
  <Heading>Photos</Heading>
  <Heading>Videos</Heading>
</Section>
```

But how can the `<Heading>` component know the level of its closest `<Section>`? **That would require some way for a child to "ask" for data from somewhere above in the tree.**
متن زیر، ترجمه‌ی متن انگلیسی شماست:
/////////////////////////
اما چگونه کامپوننت `<Heading>` می‌تواند سطح `<Section>` نزدیک خود را بداند؟ **این نیاز به یک روشی برای اینکه فرزند از جایی در درخت اطلاعات بپرسد وجود دارد.**

You can't do it with props alone. This is where context comes into play. You will do it in three steps:
/////////////////////
شما نمی‌توانید فقط با props این کار را انجام دهید. در اینجا ویژگی context به کار می‌آید. شما این کار را در سه مرحله انجام خواهید داد:

<ol>
<li>ساخت یک context جدید</li>
<li>تنظیم مقدار context</li>
<li>دسترسی به مقدار context</li>
</ol>


1. **Create** a context. (You can call it `LevelContext`, since it's for the heading level.)
2. **Use** that context from the component that needs the data. (`Heading` will use `LevelContext`.)
3. **Provide** that context from the component that specifies the data. (`Section` will provide `LevelContext`.)
////////////////////////////
متن زیر، ترجمه‌ی متن انگلیسی شماست:

1. یک context بسازید. (می‌توانید آن را `LevelContext` بنامید، زیرا برای سطح عنوان است.)
2. از آن context در کامپوننتی که به داده نیاز دارد، استفاده کنید. (`Heading` از `LevelContext` استفاده خواهد کرد.)
3. این context را از کامپوننتی که داده‌ها را مشخص می‌کند، فراهم کنید. (`Section` `LevelContext` را فراهم خواهد کرد.)

Context lets a parent--even a distant one!--provide some data to the entire tree inside of it.
////////////
با ویژگی context React، حتی یک والد حتی اگر دور باشد، می‌تواند برخی از داده‌ها را به کل درخت درون آن فراهم کند.

<DiagramGroup>

<Diagram name="passing_data_context_close" height={160} width={608} captionPosition="top" alt="Diagram with a tree of three components. The parent contains a bubble representing a value highlighted in orange which projects down to the two children, each highlighted in orange." >

Using context in close children
استفاده از context در فرزندان نزدیک

</Diagram>

<Diagram name="passing_data_context_far" height={430} width={608} captionPosition="top" alt="Diagram with a tree of ten nodes, each node with two children or less. The root parent node contains a bubble representing a value highlighted in orange. The value projects down directly to four leaves and one intermediate component in the tree, which are all highlighted in orange. None of the other intermediate components are highlighted.">

Using context in distant children
استفاده از context در فرزندان دور

</Diagram>

</DiagramGroup>

### Step 1: Create the context(کانتکست را بسازید) {/*step-1-create-the-context*/}

First, you need to create the context. You'll need to **export it from a file** so that your components can use it:
////////////////////////
ابتدا باید یک context بسازید. برای استفاده از آن در کامپوننت‌های خود، باید آن را از یک فایل export کنید.

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Section>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Section>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Section>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```js Section.js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

```js Heading.js
export default function Heading({ level, children }) {
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

```js LevelContext.js active
import { createContext } from 'react';

export const LevelContext = createContext(1);
```

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

The only argument to `createContext` is the _default_ value. Here, `1` refers to the biggest heading level, but you could pass any kind of value (even an object). You will see the significance of the default value in the next step.
///////////////////////
متن زیر، ترجمه‌ی متن انگلیسی شماست:

تنها آرگومان `createContext` مقدار پیش‌فرض است. در اینجا، `1` به بزرگترین سطح عنوان اشاره دارد، اما می‌توانید هر نوع مقداری (حتی یک شیء) را منتقل کنید. اهمیت مقدار پیش‌فرض را در مرحله بعدی خواهید دید.

### Step 2: Use the context(از context استفاده کنید) {/*step-2-use-the-context*/}

Import the `useContext` Hook from React and your context:
////////////
هوک `useContext` را از React و زمینه خود وارد کنید:

```js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';
```

Currently, the `Heading` component reads `level` from props:
//////////////////
در حال حاضر، کامپوننت `Heading` `level` را از props می‌خواند:

```js
export default function Heading({ level, children }) {
  // ...
}
```

Instead, remove the `level` prop and read the value from the context you just imported, `LevelContext`:
///////////////////////
بله، به جای `level` prop را حذف کنید و مقدار را از contextی که به تازگی import کرده‌اید، `LevelContext` بخوانید.

```js {2}
export default function Heading({ children }) {
  const level = useContext(LevelContext);
  // ...
}
```

`useContext` is a Hook. Just like `useState` and `useReducer`, you can only call a Hook immediately inside a React component (not inside loops or conditions). **`useContext` tells React that the `Heading` component wants to read the `LevelContext`.**
///////////////
`useContext` یک Hook است. مانند `useState` و `useReducer`، شما فقط می‌توانید یک Hook را در داخل یک کامپوننت React (نه درون حلقه‌ها یا شرایط) فراخوانی کنید. **`useContext` به React می‌گوید که کامپوننت `Heading` می‌خواهد `LevelContext` را بخواند.**.

Now that the `Heading` component doesn't have a `level` prop, you don't need to pass the level prop to `Heading` in your JSX like this anymore:
/////////////////
حالا که کامپوننت `Heading` دارای `level` prop نیست، شما نیازی به انتقال `level` prop به `Heading` در JSX خود ندارید.

```js
<Section>
  <Heading level={4}>Sub-sub-heading</Heading>
  <Heading level={4}>Sub-sub-heading</Heading>
  <Heading level={4}>Sub-sub-heading</Heading>
</Section>
```

Update the JSX so that it's the `Section` that receives it instead:
JSX را به‌روزرسانی کنید همچنین آن هست یک`Section` که آن را دریافت کند:

```jsx
<Section level={4}>
  <Heading>Sub-sub-heading</Heading>
  <Heading>Sub-sub-heading</Heading>
  <Heading>Sub-sub-heading</Heading>
</Section>
```

As a reminder, this is the markup that you were trying to get working:
به عنوان یادآوری، این نشانه گذاری است که شما سعی داشتید کار کنید:

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section level={1}>
      <Heading>Title</Heading>
      <Section level={2}>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Section level={3}>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Section level={4}>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```js Section.js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

```js Heading.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext);
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

```js LevelContext.js
import { createContext } from 'react';

export const LevelContext = createContext(1);
```

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

Notice this example doesn't quite work, yet! All the headings have the same size because **even though you're *using* the context, you have not *provided* it yet.** React doesn't know where to get it!
///////////////////
 "توجه کنید که این مثال هنوز کاملا کار نمی‌کند! تمام عناوین به دلیل اینکه حتی با وجود استفاده از context، شما هنوز آن را فراهم نکرده‌اید، اندازه یکسانی دارند. React نمی‌داند کجا باید آن را بگیرد!"¹.

If you don't provide the context, React will use the default value you've specified in the previous step. In this example, you specified `1` as the argument to `createContext`, so `useContext(LevelContext)` returns `1`, setting all those headings to `<h1>`. Let's fix this problem by having each `Section` provide its own context.
/////////////////
اگر شما محتوای context را فراهم نکنید، React از مقدار پیش‌فرضی که در مرحله قبل مشخص کرده‌اید استفاده خواهد کرد. در این مثال، شما `1` را به عنوان آرگومان `createContext` مشخص کرده‌اید، بنابراین `useContext(LevelContext)` مقدار `1` را برمی‌گرداند و تمام عناوین را به `<h1>` تبدیل می‌کند. برای رفع این مشکل، باید هر بخش (Section) محتوای context خود را فراهم کند¹.


### Step 3: Provide the context {/*step-3-provide-the-context*/}

The `Section` component currently renders its children:
"جزء بخش در حال حاضر فرزندان خود را ارائه می دهد" ⁴.

```js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

**Wrap them with a context provider** to provide the `LevelContext` to them:
“آنها را با یک ارائه دهنده context بپوشانید” تا LevelContext را به آنها ارائه دهید1.

```js {1,6,8}
import { LevelContext } from './LevelContext.js';

export default function Section({ level, children }) {
  return (
    <section className="section">
      <LevelContext.Provider value={level}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}
```

This tells React: "if any component inside this `<Section>` asks for `LevelContext`, give them this `level`." The component will use the value of the nearest `<LevelContext.Provider>` in the UI tree above it.
/////////////////////
این متن به فارسی می‌گوید که "اگر هر کامپوننت داخل `<Section>` برای `LevelContext` درخواست کند، به آنها `level` را ارائه دهید." کامپوننت از مقدار نزدیکترین `<LevelContext.Provider>` در درخت UI بالای خود استفاده خواهد کرد¹.

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section level={1}>
      <Heading>Title</Heading>
      <Section level={2}>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Section level={3}>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Section level={4}>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```js Section.js
import { LevelContext } from './LevelContext.js';

export default function Section({ level, children }) {
  return (
    <section className="section">
      <LevelContext.Provider value={level}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}
```

```js Heading.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext);
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

```js LevelContext.js
import { createContext } from 'react';

export const LevelContext = createContext(1);
```

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

It's the same result as the original code, but you did not need to pass the `level` prop to each `Heading` component! Instead, it "figures out" its heading level by asking the closest `Section` above:
////////////////////
این متن به فارسی می‌گوید که "نتیجه نهایی همانند کد اصلی است، اما شما نیازی به انتقال level prop به هر کامپوننت Heading نداشتید! به جای آن، با پرسیدن نزدیکترین Section بالای خود، سطح عنوان خود را “شناسایی می‌کند” 1.

1. You pass a `level` prop to the `<Section>`.
2. `Section` wraps its children into `<LevelContext.Provider value={level}>`.
3. `Heading` asks the closest value of `LevelContext` above with `useContext(LevelContext)`.

///////////////
این متن به فارسی می‌گوید که:
1. شما یک `prop` با نام `level` را به `<Section>` ارسال می‌کنید.
2. `Section` فرزندان خود را در `<LevelContext.Provider value={level}>` قرار می‌دهد.
3. `Heading` با استفاده از `useContext(LevelContext)`، نزدیکترین مقدار `LevelContext` را بالای خود در درخت UI پیدا می‌کند¹.


## Using and providing context from the same component {/*using-and-providing-context-from-the-same-component*/}

Currently, you still have to specify each section's `level` manually:
////////////
“در حال حاضر شما هنوز هم باید سطح هر بخش را به صورت دستی مشخص کنید”

```js
export default function Page() {
  return (
    <Section level={1}>
      ...
      <Section level={2}>
        ...
        <Section level={3}>
          ...
```

Since context lets you read information from a component above, each `Section` could read the `level` from the `Section` above, and pass `level + 1` down automatically. Here is how you could do it:
////////////////////
"از آنجایی که context به شما اجازه می دهد اطلاعات را از یک کامپوننت بالاتر بخوانید، هر بخش (Section) می تواند سطح خود را از بخش بالاتر بخواند و سطح level + 1 را به صورت خودکار به پایین منتقل کند. اینجا نحوه انجام آن را مشاهده می کنید:"1.

```js Section.js {5,8}
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext.Provider value={level + 1}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}
```

With this change, you don't need to pass the `level` prop *either* to the `<Section>` or to the `<Heading>`:
/////////////////
با این تغییر، شما نیازی به انتقال `level` prop به `<Section>` یا `<Heading>` ندارید¹.

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading>Title</Heading>
      <Section>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Section>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Section>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```js Section.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext.Provider value={level + 1}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}
```

```js Heading.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext);
  switch (level) {
    case 0:
      throw Error('Heading must be inside a Section!');
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

```js LevelContext.js
import { createContext } from 'react';

export const LevelContext = createContext(0);
```

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

Now both `Heading` and `Section` read the `LevelContext` to figure out how "deep" they are. And the `Section` wraps its children into the `LevelContext` to specify that anything inside of it is at a "deeper" level.
///////////////////
"اکنون هر دو Heading و Section مقدار LevelContext را می‌خوانند تا بفهمند چقدر “عمیق” هستند. و Section فرزندان خود را در LevelContext قرار می‌دهد تا مشخص کند هر چیزی در داخل آن در سطح “عمیق‌تر” قرار دارد."1.

<Note>

This example uses heading levels because they show visually how nested components can override context. But context is useful for many other use cases too. You can pass down any information needed by the entire subtree: the current color theme, the currently logged in user, and so on.
//////////////////
این متن به فارسی می‌گوید که "این مثال از سطح عنوان استفاده می‌کند زیرا به صورت بصری نشان می‌دهد که چگونه کامپوننت‌های تو در تو می‌توانند context را override کنند. اما context برای بسیاری از موارد دیگر هم مفید است. شما می‌توانید هر اطلاعاتی را که توسط کل زیر درخت نیاز است، به پایین منتقل کنید: تم فعلی رنگ، کاربری که در حال حاضر وارد شده است، و غیره."1.

</Note>

## Context passes through intermediate components {/*context-passes-through-intermediate-components*/}

You can insert as many components as you like between the component that provides context and the one that uses it. This includes both built-in components like `<div>` and components you might build yourself.
/////////////////
شما می‌توانید تعداد دلخواهی از کامپوننت‌ها را بین کامپوننتی که context را فراهم می‌کند و کامپوننتی که از آن استفاده می‌کند، قرار دهید. این شامل هر دو کامپوننت داخلی مانند `<div>` و کامپوننت‌هایی است که خودتان می‌سازید¹.

In this example, the same `Post` component (with a dashed border) is rendered at two different nesting levels. Notice that the `<Heading>` inside of it gets its level automatically from the closest `<Section>`:
///////////////////
در این مثال، کامپوننت `Post` با حاشیه خط چینی در دو سطح تو در تو رندر می‌شود. توجه کنید که `<Heading>` در داخل آن سطح خود را به صورت خودکار از نزدیکترین `<Section>` دریافت می‌کند¹.

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function ProfilePage() {
  return (
    <Section>
      <Heading>My Profile</Heading>
      <Post
        title="Hello traveller!"
        body="Read about my adventures."
      />
      <AllPosts />
    </Section>
  );
}

function AllPosts() {
  return (
    <Section>
      <Heading>Posts</Heading>
      <RecentPosts />
    </Section>
  );
}

function RecentPosts() {
  return (
    <Section>
      <Heading>Recent Posts</Heading>
      <Post
        title="Flavors of Lisbon"
        body="...those pastéis de nata!"
      />
      <Post
        title="Buenos Aires in the rhythm of tango"
        body="I loved it!"
      />
    </Section>
  );
}

function Post({ title, body }) {
  return (
    <Section isFancy={true}>
      <Heading>
        {title}
      </Heading>
      <p><i>{body}</i></p>
    </Section>
  );
}
```

```js Section.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ children, isFancy }) {
  const level = useContext(LevelContext);
  return (
    <section className={
      'section ' +
      (isFancy ? 'fancy' : '')
    }>
      <LevelContext.Provider value={level + 1}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}
```

```js Heading.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext);
  switch (level) {
    case 0:
      throw Error('Heading must be inside a Section!');
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

```js LevelContext.js
import { createContext } from 'react';

export const LevelContext = createContext(0);
```

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}

.fancy {
  border: 4px dashed pink;
}
```

</Sandpack>

You didn't do anything special for this to work. A `Section` specifies the context for the tree inside it, so you can insert a `<Heading>` anywhere, and it will have the correct size. Try it in the sandbox above!
////////////////
"شما هیچ کار خاصی برای این کار انجام ندادید. یک `Section` محتوای context را برای درخت داخل آن مشخص می کند، بنابراین می توانید هر جایی `<Heading>` را قرار دهید و اندازه صحیح آن را خواهید داشت. در بالای صفحه، این کار را امتحان کنید!"¹.

**Context lets you write components that "adapt to their surroundings" and display themselves differently depending on _where_ (or, in other words, _in which context_) they are being rendered.**

//////////////
"Context به شما اجازه می دهد که کامپوننت هایی را بنویسید که "به محیط خودشان سازگار می شوند" و به تفاوت های خودشان در نمایش، بستگی به _کجا_ (یا به عبارت دیگر، _در چه context_) در حال رندر شدن دارند."¹.

How context works might remind you of [CSS property inheritance.](https://developer.mozilla.org/en-US/docs/Web/CSS/inheritance) In CSS, you can specify `color: blue` for a `<div>`, and any DOM node inside of it, no matter how deep, will inherit that color unless some other DOM node in the middle overrides it with `color: green`. Similarly, in React, the only way to override some context coming from above is to wrap children into a context provider with a different value.
////////////////////
"نحوه کار context ممکن است به شما یادآوری وراثت خصوصیت CSS شود. در CSS، می توانید `color: blue` را برای `<div>` مشخص کنید و هر گره DOM داخل آن، بدون توجه به عمق، این رنگ را به ارث می برد مگر اینکه گره DOM دیگری در میان آن با `color: green` آن را override کند. به طور مشابه، در React، تنها راه override کردن برخی context هایی که از بالا می آیند، پوشاندن فرزندان در یک ارائه دهنده context با مقدار متفاوت است." ¹..

In CSS, different properties like `color` and `background-color` don't override each other. You can set all  `<div>`'s `color` to red without impacting `background-color`. Similarly, **different React contexts don't override each other.** Each context that you make with `createContext()` is completely separate from other ones, and ties together components using and providing *that particular* context. One component may use or provide many different contexts without a problem.
//////////////
"در CSS، خصوصیت های مختلف مانند `color` و `background-color` یکدیگر را override نمی کنند. شما می توانید تمام `color` های `<div>` را به رنگ قرمز تنظیم کنید بدون اینکه `background-color` را تحت تاثیر قرار دهید. به طور مشابه، **context های مختلف React یکدیگر را override نمی کنند**. هر context که با `createContext()` ایجاد می کنید، کاملاً جدا از دیگر context ها است و کامپوننت ها را با استفاده و ارائه *آن context خاص* به هم متصل می کند. یک کامپوننت می تواند بدون مشکل از چندین context استفاده کند یا آنها را فراهم کند."¹.

## Before you use context {/*before-you-use-context*/}

Context is very tempting to use! However, this also means it's too easy to overuse it. **Just because you need to pass some props several levels deep doesn't mean you should put that information into context.**
///////////////
"استفاده از context بسیار جذاب است! با این حال، این به این معنی است که استفاده از آن بسیار آسان است. **فقط به این دلیل که شما نیاز دارید تا چندین سطح از props را منتقل کنید، به معنی آن نیست که باید این اطلاعات را در context قرار دهید.**" ¹.

Here's a few alternatives you should consider before using context:
///////
"قبل از استفاده از context، چندین جایگزین را باید در نظر بگیرید:"¹.

1. **Start by [passing props.](/learn/passing-props-to-a-component)** If your components are not trivial, it's not unusual to pass a dozen props down through a dozen components. It may feel like a slog, but it makes it very clear which components use which data! The person maintaining your code will be glad you've made the data flow explicit with props.
/////////////////
"1. **ابتدا با [انتقال props](/learn/passing-props-to-a-component) شروع کنید.** اگر کامپوننت های شما ساده نیستند، عبور از دوازده props از طریق دوازده کامپوننت عادی نیست. ممکن است احساس کنید که این کار خسته کننده است، اما این کار باعث می شود که بسیار واضح شود کدام کامپوننت ها از کدام داده ها استفاده می کنند! شخصی که کد شما را نگهداری می کند، خوشحال خواهد شد که شما با props جریان داده را صریح کرده اید."¹.


2. **Extract components and [pass JSX as `children`](/learn/passing-props-to-a-component#passing-jsx-as-children) to them.** If you pass some data through many layers of intermediate components that don't use that data (and only pass it further down), this often means that you forgot to extract some components along the way. For example, maybe you pass data props like `posts` to visual components that don't use them directly, like `<Layout posts={posts} />`. Instead, make `Layout` take `children` as a prop, and render `<Layout><Posts posts={posts} /></Layout>`. This reduces the number of layers between the component specifying the data and the one that needs it.
///////////////////
۲. **استخراج کامپوننت ها و [انتقال JSX به عنوان `children`](/learn/passing-props-to-a-component#passing-jsx-as-children) به آنها.** اگر شما برخی از داده ها را از طریق چندین لایه از کامپوننت های واسطی که از آن داده ها استفاده نمی کنند (و فقط آنها را به پایین تر منتقل می کنند) منتقل کنید، این معمولا به این معنی است که در طول راه فراموش کرده اید که برخی از کامپوننت ها را استخراج کنید. به عنوان مثال، شاید شما داده هایی مانند `posts` را به کامپوننت های بصری مانند `<Layout posts={posts} />` منتقل کنید که به طور مستقیم از آنها استفاده نمی کنند. به جای آن، `Layout` را به عنوان یک `prop` با نام `children` بگیرید و `<Layout><Posts posts={posts} /></Layout>` را رندر کنید. این تعداد لایه های بین کامپوننت مشخص کننده داده و کامپوننتی که آن را نیاز دارد را کاهش می دهد.¹.

If neither of these approaches works well for you, consider context.
///////////
اگر هیچ یک از این روش ها برای شما کار نمی کند، به context فکر کنید¹.

## Use cases for context {/*use-cases-for-context*/}

* **Theming:** If your app lets the user change its appearance (e.g. dark mode), you can put a context provider at the top of your app, and use that context in components that need to adjust their visual look.
/////////////////
" * **تم‌سازی:** اگر برنامه شما به کاربر اجازه می دهد تا ظاهر خود را تغییر دهد (مانند حالت تاریک)، می توانید یک ارائه دهنده context را در بالای برنامه خود قرار دهید و از آن context در کامپوننت هایی که نیاز به تنظیم ظاهر خود دارند، استفاده کنید."¹.

* **Current account:** Many components might need to know the currently logged in user. Putting it in context makes it convenient to read it anywhere in the tree. Some apps also let you operate multiple accounts at the same time (e.g. to leave a comment as a different user). In those cases, it can be convenient to wrap a part of the UI into a nested provider with a different current account value.
//////////////////
" * **حساب جاری:** بسیاری از کامپوننت ها ممکن است نیاز داشته باشند تا کاربری که در حال حاضر وارد شده است را بدانند. قرار دادن آن در context، خواندن آن در هر جای درخت راحت می کند. برخی برنامه ها هم به شما اجازه می دهند که در همان زمان با چندین حساب کاربری فعال باشید (مانند ترک کردن نظر به عنوان یک کاربر متفاوت). در این موارد، مناسب است یک بخش از UI را در یک ارائه دهنده تو در تو با مقدار حساب جاری متفاوت پوشانید."¹.

* **Routing:** Most routing solutions use context internally to hold the current route. This is how every link "knows" whether it's active or not. If you build your own router, you might want to do it too.
//////////////////
* **مسیریابی:** بیشترین راه حل های مسیریابی از context به صورت داخلی برای نگهداری مسیر فعلی استفاده می کنند. این چگونه هر لینک "می داند" که آیا فعال است یا خیر. اگر شما مسیریاب خود را بسازید، ممکن است بخواهید همین کار را انجام دهید.².

* **Managing state:** As your app grows, you might end up with a lot of state closer to the top of your app. Many distant components below may want to change it. It is common to [use a reducer together with context](/learn/scaling-up-with-reducer-and-context) to manage complex state and pass it down to distant components without too much hassle.
  /////////////////
" * **مدیریت وضعیت:** با گسترش برنامه شما، ممکن است با بسیاری از وضعیت های نزدیک به بالای برنامه خود روبرو شوید. بسیاری از کامپوننت های دورتر ممکن است بخواهند آن را تغییر دهند. استفاده از یک reducer همراه با context برای مدیریت وضعیت پیچیده و انتقال آن به کامپوننت های دورتر بدون زحمت بیش از حد معمول است." ¹.

Context is not limited to static values. If you pass a different value on the next render, React will update all the components reading it below! This is why context is often used in combination with state.
///////////////////
- کانتکست به مقادیر استاتیک محدود نیست. اگر در رندر بعدی مقدار متفاوتی را منتقل کنید، React همه‌ی کامپوننت‌هایی که آن را می‌خوانند را به‌روزرسانی خواهد کرد! به همین دلیل کانتکست اغلب با استیت به کار می‌رود.

In general, if some information is needed by distant components in different parts of the tree, it's a good indication that context will help you.
///////////////
بطور کلی، اگر برای کامپوننت‌های دور از هم در قسمت‌های مختلف درخت نیاز به اطلاعاتی وجود دارد، این نشانه‌ای خوب است که کانتکست به شما کمک خواهد کرد¹².

<Recap>

* Context lets a component provide some information to the entire tree below it.
* To pass context:
  1. Create and export it with `export const MyContext = createContext(defaultValue)`.
  2. Pass it to the `useContext(MyContext)` Hook to read it in any child component, no matter how deep.
  3. Wrap children into `<MyContext.Provider value={...}>` to provide it from a parent.
* Context passes through any components in the middle.
* Context lets you write components that "adapt to their surroundings".
* Before you use context, try passing props or passing JSX as `children`.

////////////////////////

به عبارت دیگر، کانتکست به یک کامپوننت اجازه می‌دهد تا برای تمام درخت زیرین خود، اطلاعاتی را فراهم کند¹². برای انتقال کانتکست، می‌توانید به شرح زیر عمل کنید:

1. با استفاده از `createContext(defaultValue)`، آن را ایجاد و صادر کنید.
2. با استفاده از `useContext(MyContext)`، آن را در هر کامپوننت فرزندی که مایلید بخوانید، خوانده و استفاده کنید.
3. با استفاده از `<MyContext.Provider value={...}>`، آن را از یک کامپوننت والد به فرزندان خود ارائه دهید.

کانتکست از طریق هر کامپوننتی که در میانه قرار دارد، عبور می‌کند. با استفاده از کانتکست، می‌توانید کامپوننت‌هایی بنویسید که "به محیط خودشان سازگاری پیدا کنند" ¹. قبل از استفاده از کانتکست، تلاش کنید با انتقال پراپ‌ها یا JSX به عنوان `children`، نیاز خود را برطرف کنید.

</Recap>

<Challenges>

#### Replace prop drilling with context {/*replace-prop-drilling-with-context*/}

In this example, toggling the checkbox changes the `imageSize` prop passed to each `<PlaceImage>`. The checkbox state is held in the top-level `App` component, but each `<PlaceImage>` needs to be aware of it.

Currently, `App` passes `imageSize` to `List`, which passes it to each `Place`, which passes it to the `PlaceImage`. Remove the `imageSize` prop, and instead pass it from the `App` component directly to `PlaceImage`.

You can declare context in `Context.js`.

<Sandpack>

```js App.js
import { useState } from 'react';
import { places } from './data.js';
import { getImageUrl } from './utils.js';

export default function App() {
  const [isLarge, setIsLarge] = useState(false);
  const imageSize = isLarge ? 150 : 100;
  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isLarge}
          onChange={e => {
            setIsLarge(e.target.checked);
          }}
        />
        Use large images
      </label>
      <hr />
      <List imageSize={imageSize} />
    </>
  )
}

function List({ imageSize }) {
  const listItems = places.map(place =>
    <li key={place.id}>
      <Place
        place={place}
        imageSize={imageSize}
      />
    </li>
  );
  return <ul>{listItems}</ul>;
}

function Place({ place, imageSize }) {
  return (
    <>
      <PlaceImage
        place={place}
        imageSize={imageSize}
      />
      <p>
        <b>{place.name}</b>
        {': ' + place.description}
      </p>
    </>
  );
}

function PlaceImage({ place, imageSize }) {
  return (
    <img
      src={getImageUrl(place)}
      alt={place.name}
      width={imageSize}
      height={imageSize}
    />
  );
}
```

```js Context.js

```

```js data.js
export const places = [{
  id: 0,
  name: 'Bo-Kaap in Cape Town, South Africa',
  description: 'The tradition of choosing bright colors for houses began in the late 20th century.',
  imageId: 'K9HVAGH'
}, {
  id: 1, 
  name: 'Rainbow Village in Taichung, Taiwan',
  description: 'To save the houses from demolition, Huang Yung-Fu, a local resident, painted all 1,200 of them in 1924.',
  imageId: '9EAYZrt'
}, {
  id: 2, 
  name: 'Macromural de Pachuca, Mexico',
  description: 'One of the largest murals in the world covering homes in a hillside neighborhood.',
  imageId: 'DgXHVwu'
}, {
  id: 3, 
  name: 'Selarón Staircase in Rio de Janeiro, Brazil',
  description: 'This landmark was created by Jorge Selarón, a Chilean-born artist, as a "tribute to the Brazilian people."',
  imageId: 'aeO3rpI'
}, {
  id: 4, 
  name: 'Burano, Italy',
  description: 'The houses are painted following a specific color system dating back to 16th century.',
  imageId: 'kxsph5C'
}, {
  id: 5, 
  name: 'Chefchaouen, Marocco',
  description: 'There are a few theories on why the houses are painted blue, including that the color repels mosquitos or that it symbolizes sky and heaven.',
  imageId: 'rTqKo46'
}, {
  id: 6,
  name: 'Gamcheon Culture Village in Busan, South Korea',
  description: 'In 2009, the village was converted into a cultural hub by painting the houses and featuring exhibitions and art installations.',
  imageId: 'ZfQOOzf'
}];
```

```js utils.js
export function getImageUrl(place) {
  return (
    'https://i.imgur.com/' +
    place.imageId +
    'l.jpg'
  );
}
```

```css
ul { list-style-type: none; padding: 0px 10px; }
li { 
  margin-bottom: 10px; 
  display: grid; 
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
```

</Sandpack>

<Solution>

Remove `imageSize` prop from all the components.

Create and export `ImageSizeContext` from `Context.js`. Then wrap the List into `<ImageSizeContext.Provider value={imageSize}>` to pass the value down, and `useContext(ImageSizeContext)` to read it in the `PlaceImage`:

<Sandpack>

```js App.js
import { useState, useContext } from 'react';
import { places } from './data.js';
import { getImageUrl } from './utils.js';
import { ImageSizeContext } from './Context.js';

export default function App() {
  const [isLarge, setIsLarge] = useState(false);
  const imageSize = isLarge ? 150 : 100;
  return (
    <ImageSizeContext.Provider
      value={imageSize}
    >
      <label>
        <input
          type="checkbox"
          checked={isLarge}
          onChange={e => {
            setIsLarge(e.target.checked);
          }}
        />
        Use large images
      </label>
      <hr />
      <List />
    </ImageSizeContext.Provider>
  )
}

function List() {
  const listItems = places.map(place =>
    <li key={place.id}>
      <Place place={place} />
    </li>
  );
  return <ul>{listItems}</ul>;
}

function Place({ place }) {
  return (
    <>
      <PlaceImage place={place} />
      <p>
        <b>{place.name}</b>
        {': ' + place.description}
      </p>
    </>
  );
}

function PlaceImage({ place }) {
  const imageSize = useContext(ImageSizeContext);
  return (
    <img
      src={getImageUrl(place)}
      alt={place.name}
      width={imageSize}
      height={imageSize}
    />
  );
}
```

```js Context.js
import { createContext } from 'react';

export const ImageSizeContext = createContext(500);
```

```js data.js
export const places = [{
  id: 0,
  name: 'Bo-Kaap in Cape Town, South Africa',
  description: 'The tradition of choosing bright colors for houses began in the late 20th century.',
  imageId: 'K9HVAGH'
}, {
  id: 1, 
  name: 'Rainbow Village in Taichung, Taiwan',
  description: 'To save the houses from demolition, Huang Yung-Fu, a local resident, painted all 1,200 of them in 1924.',
  imageId: '9EAYZrt'
}, {
  id: 2, 
  name: 'Macromural de Pachuca, Mexico',
  description: 'One of the largest murals in the world covering homes in a hillside neighborhood.',
  imageId: 'DgXHVwu'
}, {
  id: 3, 
  name: 'Selarón Staircase in Rio de Janeiro, Brazil',
  description: 'This landmark was created by Jorge Selarón, a Chilean-born artist, as a "tribute to the Brazilian people".',
  imageId: 'aeO3rpI'
}, {
  id: 4, 
  name: 'Burano, Italy',
  description: 'The houses are painted following a specific color system dating back to 16th century.',
  imageId: 'kxsph5C'
}, {
  id: 5, 
  name: 'Chefchaouen, Marocco',
  description: 'There are a few theories on why the houses are painted blue, including that the color repels mosquitos or that it symbolizes sky and heaven.',
  imageId: 'rTqKo46'
}, {
  id: 6,
  name: 'Gamcheon Culture Village in Busan, South Korea',
  description: 'In 2009, the village was converted into a cultural hub by painting the houses and featuring exhibitions and art installations.',
  imageId: 'ZfQOOzf'
}];
```

```js utils.js
export function getImageUrl(place) {
  return (
    'https://i.imgur.com/' +
    place.imageId +
    'l.jpg'
  );
}
```

```css
ul { list-style-type: none; padding: 0px 10px; }
li { 
  margin-bottom: 10px; 
  display: grid; 
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
```

</Sandpack>

Note how components in the middle don't need to pass `imageSize` anymore.

</Solution>

</Challenges>
