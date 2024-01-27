---
title: Understanding Your UI as a Tree(درک رابط کاربری خود به عنوان یک درخت)
---

<Intro>

Your React app is taking shape with many components being nested within each other. How does React keep track of your app's component structure?

React, and many other UI libraries, model UI as a tree. Thinking of your app as a tree is useful for understanding the relationship between components. This understanding will help you debug future concepts like performance and state management.
/////
فهمیدن رابط کاربری شما به عنوان یک درخت برنامه React شما با بسیاری از کامپوننت‌های تو در تو شکل می‌گیرد. چگونه React از ساختار کامپوننت‌های برنامه شما پیگیری می‌کند؟

React و بسیاری از کتابخانه‌های رابط کاربری دیگر، رابط کاربری را به عنوان یک درخت مدل می‌کنند. فکر کردن به برنامه شما به عنوان یک درخت برای درک رابطه بین کامپوننت‌ها مفید است. این درک به شما در اشکال آینده مانند عملکرد و مدیریت وضعیت کمک می‌کند.

////
درختان یک مدل رابطه بین آیتم ها هستند و UI اغلب با استفاده از ساختارهای درختی نشان داده می شود. برای مثال، مرورگرها از ساختارهای درختی برای مدل‌سازی HTML (DOM) و CSS (CSSOM) استفاده می‌کنند. پلتفرم های موبایل همچنین از درخت ها برای نشان دادن سلسله مراتب دید خود استفاده می کنند.

</Intro>

<YouWillLearn>

* How React "sees" component structures
* What a render tree is and what it is useful for
* What a module dependency tree is and what it is useful for

/////
* How React ساختارهای کامپوننت را "می بیند".
* درخت رندر چیست و چه کاربردی دارد
* درخت وابستگی ماژول چیست و چه کاربردی دارد

</YouWillLearn>

## Your UI as a tree(رابط کاربری شما به عنوان یک درخت) {/*your-ui-as-a-tree*/}

Trees are a relationship model between items and UI is often represented using tree structures. For example, browsers use tree structures to model HTML ([DOM](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction)) and CSS ([CSSOM](https://developer.mozilla.org/docs/Web/API/CSS_Object_Model)). Mobile platforms also use trees to represent their view hierarchy.

/////
درخت‌ها یک مدل رابطه بین موارد هستند و رابط کاربری (UI) اغلب با استفاده از ساختارهای درختی نمایش داده می‌شود. به عنوان مثال، مرورگرها از ساختارهای درختی برای مدل کردن HTML (DOM) و CSS (CSSOM) استفاده می‌کنند. پلتفرم‌های موبایل همچنین از درخت‌ها برای نمایش سلسله مراتب نمایش استفاده می‌کنند.

////
درختان یک مدل رابطه بین آیتم ها هستند و UI اغلب با استفاده از ساختارهای درختی نشان داده می شود. برای مثال، مرورگرها از ساختارهای درختی برای مدل‌سازی HTML (DOM) و CSS (CSSOM) استفاده می‌کنند. پلتفرم های موبایل همچنین از درخت ها برای نشان دادن سلسله مراتب دید خود استفاده می کنند.

<Diagram name="preserving_state_dom_tree" height={193} width={864} alt="Diagram with three sections arranged horizontally. In the first section, there are three rectangles stacked vertically, with labels 'Component A', 'Component B', and 'Component C'. Transitioning to the next pane is an arrow with the React logo on top labeled 'React'. The middle section contains a tree of components, with the root labeled 'A' and two children labeled 'B' and 'C'. The next section is again transitioned using an arrow with the React logo on top labeled 'React'. The third and final section is a wireframe of a browser, containing a tree of 8 nodes, which has only a subset highlighted (indicating the subtree from the middle section).">

React creates a UI tree from your component. In this example, the UI tree is then used to render to the DOM.

/////
React یک درخت رابط کاربری از component شما ایجاد می کند. در این مثال، درخت UI برای ارائه به DOM استفاده می شود.

////
React یک درخت رابط کاربری از کامپوننت شما ایجاد می کند. در این مثال، درخت UI برای ارائه به DOM استفاده می شود.
</Diagram>

Like browsers and mobile platforms, React also uses tree structures to manage and model the relationship between components in a React app. These trees are useful tools to understand how data flows through a React app and how to optimize rendering and app size.

/////
مانند مرورگرها و پلتفرم‌های موبایل، React نیز از ساختارهای درختی برای مدیریت و مدل‌سازی رابطه بین اجزا در برنامه React استفاده می‌کند. این درخت‌ها ابزارهای مفیدی برای درک نحوه جریان داده‌ها از طریق برنامه React و نحوه بهینه‌سازی رندر و اندازه برنامه هستند.

//////
مانند مرورگرها و پلتفرم‌های موبایل، React نیز از ساختارهای درختی برای مدیریت و مدل‌سازی رابطه بین اجزا در برنامه React استفاده می‌کند. این درخت‌ها ابزارهای مفیدی برای درک نحوه جریان داده‌ها از طریق برنامه React و نحوه بهینه‌سازی رندر و اندازه برنامه هستند.

## The Render Tree(درخت رندر) {/*the-render-tree*/}

A major feature of components is the ability to compose components of other components. As we [nest components](/learn/your-first-component#nesting-and-organizing-components), we have the concept of parent and child components, where each parent component may itself be a child of another component.

When we render a React app, we can model this relationship in a tree, known as the render tree.

Here is a React app that renders inspirational quotes.

/////
یکی از ویژگی های اصلی کامپوننت ها توانایی ترکیب اجزای اجزای دیگر است. همانطور که ما [مؤلفه‌های آشیانه] (/learn/your-first-component#nesting-and-organizing-components)، مفهوم مولفه‌های والد و فرزند را داریم، که در آن هر مؤلفه والد ممکن است خود فرزند جزء دیگری باشد.

هنگامی که یک برنامه React را رندر می کنیم، می توانیم این رابطه را در یک درخت که به درخت رندر معروف است مدل سازی کنیم.

در اینجا یک برنامه React است که نقل قول های الهام بخش را ارائه می دهد.

//////
یکی از ویژگی های اصلی کامپوننت ها توانایی ترکیب اجزای اجزای دیگر است. همانطور که ما [مؤلفه‌های آشیانه] (/learn/your-first-component#nesting-and-organizing-components)، مفهوم مولفه‌های والد و فرزند را داریم، که در آن هر مؤلفه والد ممکن است خود فرزند جزء دیگری باشد.

هنگامی که یک برنامه React را رندر می کنیم، می توانیم این رابطه را در یک درخت که به درخت رندر معروف است مدل سازی کنیم.

در اینجا یک برنامه React است که نقل قول های الهام بخش را ارائه می دهد.

<Sandpack>

```js App.js
import FancyText from './FancyText';
import InspirationGenerator from './InspirationGenerator';
import Copyright from './Copyright';

export default function App() {
  return (
    <>
      <FancyText title text="Get Inspired App" />
      <InspirationGenerator>
        <Copyright year={2004} />
      </InspirationGenerator>
    </>
  );
}

```

```js FancyText.js
export default function FancyText({title, text}) {
  return title
    ? <h1 className='fancy title'>{text}</h1>
    : <h3 className='fancy cursive'>{text}</h3>
}
```

```js InspirationGenerator.js
import * as React from 'react';
import quotes from './quotes';
import FancyText from './FancyText';

export default function InspirationGenerator({children}) {
  const [index, setIndex] = React.useState(0);
  const quote = quotes[index];
  const next = () => setIndex((index + 1) % quotes.length);

  return (
    <>
      <p>Your inspirational quote is:</p>
      <FancyText text={quote} />
      <button onClick={next}>Inspire me again</button>
      {children}
    </>
  );
}
```

```js Copyright.js
export default function Copyright({year}) {
  return <p className='small'>©️ {year}</p>;
}
```

```js quotes.js
export default [
  "Don’t let yesterday take up too much of today.” — Will Rogers",
  "Ambition is putting a ladder against the sky.",
  "A joy that's shared is a joy made double.",
  ];
```

```css
.fancy {
  font-family: 'Georgia';
}
.title {
  color: #007AA3;
  text-decoration: underline;
}
.cursive {
  font-style: italic;
}
.small {
  font-size: 10px;
}
```

</Sandpack>

<Diagram name="render_tree" height={250} width={500} alt="Tree graph with five nodes. Each node represents a component. The root of the tree is App, with two arrows extending from it to 'InspirationGenerator' and 'FancyText'. The arrows are labelled with the word 'renders'. 'InspirationGenerator' node also has two arrows pointing to nodes 'FancyText' and 'Copyright'.">

React creates a *render tree*, a UI tree, composed of the rendered components.
////
React یک *render tree* ایجاد می کند، یک درخت UI، که از اجزای رندر شده تشکیل شده است.
////
React یک درخت رندر، یک درخت رابط کاربری، که از کامپوننت‌های رندر شده تشکیل شده است، ایجاد می‌کند1.

</Diagram>

From the example app, we can construct the above render tree.

The tree is composed of nodes, each of which represents a component. `App`, `FancyText`, `Copyright`, to name a few, are all nodes in our tree.

The root node in a React render tree is the [root component](/learn/importing-and-exporting-components#the-root-component-file) of the app. In this case, the root component is `App` and it is the first component React renders. Each arrow in the tree points from a parent component to a child component.

////
از برنامه مثال، می توانیم درخت رندر بالا را بسازیم.

درخت از گره هایی تشکیل شده است که هر کدام نشان دهنده یک جزء هستند. «برنامه»، `«FancyText»`، «`Copyright`، برای نام بردن از چند مورد، همگی گره‌های درخت ما هستند.

گره ریشه در درخت رندر React، [root component](/learn/importing-and-exporting-components#the-root-component-file) برنامه است. در این مورد، کامپوننت ریشه `«App»` است و اولین مؤلفه ای است که React رندر می کند. هر فلش در درخت از یک جزء والد به یک جزء فرزند اشاره می کند.

//////
از برنامه مثال، می‌توانیم درخت رندر بالا را ساختار دهی کنیم.

این درخت از گره‌هایی تشکیل شده است که هر کدام یک کامپوننت را نشان می‌دهند. App، FancyText، Copyright و …، همه گره‌های درخت ما هستند.

گره ریشه درخت رندر React، کامپوننت ریشه برنامه است. در این مورد، کامپوننت ریشه App است و اولین کامپوننتی است که React رندر می‌کند. هر پیکان در درخت از کامپوننت والد به کامپوننت فرزند اشاره دارد.

<DeepDive>

#### Where are the HTML tags in the render tree?(تگ های HTML در درخت رندر کجا هستند1.) {/*where-are-the-html-elements-in-the-render-tree*/}

Where are the HTML tags in the render tree?
تگ های HTML در درخت رندر کجا هستند1.

You'll notice in the above render tree, there is no mention of the HTML tags that each component renders. This is because the render tree is only composed of React [components](learn/your-first-component#components-ui-building-blocks).

////
متوجه خواهید شد که در درخت رندر بالا، هیچ اشاره ای به تگ های HTML که هر جزء ارائه می دهد وجود ندارد. این به این دلیل است که درخت رندر فقط از React [components] (learn/your-first-component#components-ui-building-blocks) تشکیل شده است.

/////
در درخت رندر بالا، هیچ اشاره‌ای به تگ‌های HTMLی که هر کامپوننت رندر می‌کند وجود ندارد. این به این دلیل است که درخت رندر فقط از کامپوننت‌های React تشکیل شده است1

React, as a UI framework, is platform agnostic. On react.dev, we showcase examples that render to the web, which uses HTML markup as its UI primitives. But a React app could just as likely render to a mobile or desktop platform, which may use different UI primitives like [UIView](https://developer.apple.com/documentation/uikit/uiview) or [FrameworkElement](https://learn.microsoft.com/en-us/dotnet/api/system.windows.frameworkelement?view=windowsdesktop-7.0).

These platform UI primitives are not a part of React. React render trees can provide insight to our React app regardless of what platform your app renders to.

////
React، به عنوان یک چارچوب رابط کاربری، پلتفرم آگنوستیک است. در react.dev، نمونه‌هایی را به نمایش می‌گذاریم که به وب ارائه می‌شوند، که از نشانه‌گذاری HTML به‌عنوان UI اولیه خود استفاده می‌کند. اما یک برنامه React می‌تواند به همان اندازه روی پلتفرم موبایل یا دسکتاپ ارائه شود، که ممکن است از رابط‌های ابتدایی مختلف مانند [UIView](https://developer.apple.com/documentation/uikit/uiview) یا [FrameworkElement] (https: //learn.microsoft.com/en-us/dotnet/api/system.windows.frameworkelement?view=windowsdesktop-7.0).

این پلتفرم‌های اولیه UI بخشی از React نیستند. درخت‌های رندر React می‌توانند بینشی را برای برنامه React ما ارائه دهند، صرف نظر از اینکه برنامه شما در چه پلتفرمی رندر می‌شود.

////
React به عنوان یک چارچوب رابط کاربری، بدون توجه به پلتفرم استفاده شده، کار می‌کند. در react.dev، ما نمونه‌هایی را به نمایش می‌گذاریم که به وب رندر می‌شوند و از نشانگر HTML به عنوان اولویت‌های رابط کاربری خود استفاده می‌کنند. اما یک برنامه React می‌تواند به همان اندازه به یک پلتفرم موبایل یا دسکتاپ رندر شود که ممکن است از نشانگرهای رابط کاربری متفاوتی مانند UIView یا FrameworkElement استفاده کند1.

این عناصر اولیه رابط کاربری پلتفرم، بخشی از React نیستند. درخت رندر React می‌تواند برای برنامه React ما، بدون توجه به پلتفرمی که برنامه شما روی آن رندر می‌شود، برای ما راهنمایی کند1.

</DeepDive>

A render tree represents a single render pass of a React application. With [conditional rendering](/learn/conditional-rendering), a parent component may render different children depending on the data passed.

We can update the app to conditionally render either an inspirational quote or color.

/////
درخت رندر نشان دهنده یک پاس رندر یک برنامه React است. با [rendering conditional](/learn/conditional-rendering)، یک جزء والد ممکن است بسته به داده های ارسال شده، فرزندان متفاوتی را ارائه دهد.

می‌توانیم برنامه را به‌روزرسانی کنیم تا به‌صورت مشروط یک نقل قول یا رنگ الهام‌بخش ارائه کند.

////
یک درخت رندر، یک عبور رندر تکی از یک برنامه React را نشان می‌دهد. با رندر کردن شرطی، یک کامپوننت والد ممکن است به تعداد فرزندان متفاوتی رندر کند که به داده‌های ارسال شده بستگی دارد1.

ما می‌توانیم برنامه را به‌گونه‌ای به‌روزرسانی کنیم که به شرطی یک نقل قول الهام بخش یا رنگ را رندر کند1.

<Sandpack>

```js App.js
import FancyText from './FancyText';
import InspirationGenerator from './InspirationGenerator';
import Copyright from './Copyright';

export default function App() {
  return (
    <>
      <FancyText title text="Get Inspired App" />
      <InspirationGenerator>
        <Copyright year={2004} />
      </InspirationGenerator>
    </>
  );
}

```

```js FancyText.js
export default function FancyText({title, text}) {
  return title
    ? <h1 className='fancy title'>{text}</h1>
    : <h3 className='fancy cursive'>{text}</h3>
}
```

```js Color.js
export default function Color({value}) {
  return <div className="colorbox" style={{backgroundColor: value}} />
}
```

```js InspirationGenerator.js
import * as React from 'react';
import inspirations from './inspirations';
import FancyText from './FancyText';
import Color from './Color';

export default function InspirationGenerator({children}) {
  const [index, setIndex] = React.useState(0);
  const inspiration = inspirations[index];
  const next = () => setIndex((index + 1) % inspirations.length);

  return (
    <>
      <p>Your inspirational {inspiration.type} is:</p>
      {inspiration.type === 'quote'
      ? <FancyText text={inspiration.value} />
      : <Color value={inspiration.value} />}

      <button onClick={next}>Inspire me again</button>
      {children}
    </>
  );
}
```

```js Copyright.js
export default function Copyright({year}) {
  return <p className='small'>©️ {year}</p>;
}
```

```js inspirations.js
export default [
  {type: 'quote', value: "Don’t let yesterday take up too much of today.” — Will Rogers"},
  {type: 'color', value: "#B73636"},
  {type: 'quote', value: "Ambition is putting a ladder against the sky."},
  {type: 'color', value: "#256266"},
  {type: 'quote', value: "A joy that's shared is a joy made double."},
  {type: 'color', value: "#F9F2B4"},
];
```

```css
.fancy {
  font-family: 'Georgia';
}
.title {
  color: #007AA3;
  text-decoration: underline;
}
.cursive {
  font-style: italic;
}
.small {
  font-size: 10px;
}
.colorbox {
  height: 100px;
  width: 100px;
  margin: 8px;
}
```
</Sandpack>

<Diagram name="conditional_render_tree" height={250} width={561} alt="Tree graph with six nodes. The top node of the tree is labelled 'App' with two arrows extending to nodes labelled 'InspirationGenerator' and 'FancyText'. The arrows are solid lines and are labelled with the word 'renders'. 'InspirationGenerator' node also has three arrows. The arrows to nodes 'FancyText' and 'Color' are dashed and labelled with 'renders?'. The last arrow points to the node labelled 'Copyright' and is solid and labelled with 'renders'.">

With conditional rendering, across different renders, the render tree may render different components.
با رندر کردن شرطی، درخت رندر ممکن است در رندرهای مختلف، اجزای متفاوتی را رندر کند1.

</Diagram>

In this example, depending on what `inspiration.type` is, we may render `<FancyText>` or `<Color>`. The render tree may be different for each render pass.

Although render trees may differ across render passes, these trees are generally helpful for identifying what the *top-level* and *leaf components* are in a React app. Top-level components are the components nearest to the root component and affect the rendering performance of all the components beneath them and often contain the most complexity. Leaf components are near the bottom of the tree and have no child components and are often frequently re-rendered.

Identifying these categories of components are useful for understanding data flow and performance of your app.

/////
در این مثال، بسته به اینکه `«inspiration.type»` چیست، ممکن است `<FancyText>` یا `<Color>` را رندر کنیم. درخت رندر ممکن است برای هر پاس رندر متفاوت باشد.

اگرچه درخت‌های رندر ممکن است در بین پاس‌های رندر متفاوت باشند، این درخت‌ها عموماً برای تشخیص اینکه چه اجزای *سطح بالا* و *برگ* در برنامه React هستند مفید هستند. کامپوننت های سطح بالا نزدیک ترین مؤلفه ها به مؤلفه ریشه هستند و بر عملکرد رندر همه مؤلفه های زیر آنها تأثیر می گذارند و اغلب دارای بیشترین پیچیدگی هستند. اجزای برگ در نزدیکی پایین درخت قرار دارند و هیچ جزء فرزندی ندارند و اغلب دوباره رندر می شوند.

شناسایی این دسته از مؤلفه ها برای درک جریان داده و عملکرد برنامه شما مفید است.

/////

در این مثال، بسته به نوع inspiration.type، ممکن است `<FancyText>` یا `<Color>` را رندر کنیم. درخت رندر ممکن است برای هر عبور رندر متفاوت باشد.

اگرچه درخت‌های رندر ممکن است در عبورهای رندر مختلف متفاوت باشند، اما این درخت‌ها به طور کلی برای شناسایی اجزای بالادست و برگ‌های برنامه React مفید هستند. اجزای بالادست، اجزایی هستند که نزدیکترین کامپوننت به کامپوننت ریشه هستند و بر روی عملکرد رندر تمام کامپوننت‌های زیر آن‌ها تأثیر می‌گذارند و اغلب پیچیدگی بیشتری دارند. برگ‌ها نزدیک به پایین درخت هستند و هیچ کامپوننت فرزندی ندارند و اغلب مجدداً رندر می‌شوند.

شناسایی این دسته از اجزا برای درک جریان داده و عملکرد برنامه شما مفید است.

## The Module Dependency Tree(درخت وابستگی ماژول) {/*the-module-dependency-tree*/}

Another relationship in a React app that can be modeled with a tree are an app's module dependencies. As we [break up our components](/learn/importing-and-exporting-components#exporting-and-importing-a-component) and logic into separate files, we create [JS modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) where we may export components, functions, or constants.

Each node in a module dependency tree is a module and each branch represents an `import` statement in that module.

If we take the previous Inspirations app, we can build a module dependency tree, or dependency tree for short.

/////
یکی دیگر از روابط در برنامه React که می تواند با یک درخت مدل شود، وابستگی های ماژول برنامه است. همانطور که کامپوننت ها و منطق خود را به فایل های جداگانه تقسیم می کنیم، ماژول های JS ایجاد می کنیم که ممکن است اجزا، توابع یا ثابت ها را صادر کنیم.

هر گره در درخت وابستگی ماژول یک ماژول است و هر شاخه بیانگر یک عبارت import در آن ماژول است.

اگر از برنامه Inspiration های قبلی استفاده کنیم، می توانیم درخت وابستگی ماژول یا به اختصار درخت وابستگی بسازیم.

/////
یکی دیگر از رابطه‌هایی که در یک برنامه React وجود دارند و می‌توانند با یک درخت مدل شوند، وابستگی‌های ماژول برنامه هستند. با تقسیم کردن کامپوننت‌ها و منطق برنامه به فایل‌های جداگانه، ماژول‌های JS ایجاد می‌کنیم که می‌توانیم در آن‌ها کامپوننت‌ها، توابع یا ثابت‌ها را صادر کنیم.

هر گره در درخت وابستگی ماژول، یک ماژول است و هر شاخه نشان‌دهنده یک عبارت import در آن ماژول است.

اگر برنامه Inspirations قبلی را در نظر بگیریم، می‌توانیم یک درخت وابستگی ماژول، یا درخت وابستگی کوتاه، بسازیم1.

<Diagram name="module_dependency_tree" height={250} width={658} alt="A tree graph with seven nodes. Each node is labelled with a module name. The top level node of the tree is labelled 'App.js'. There are three arrows pointing to the modules 'InspirationGenerator.js', 'FancyText.js' and 'Copyright.js' and the arrows are labelled with 'imports'. From the 'InspirationGenerator.js' node, there are three arrows that extend to three modules: 'FancyText.js', 'Color.js', and 'inspirations.js'. The arrows are labelled with 'imports'.">

The module dependency tree for the Inspirations app.

</Diagram>

The root node of the tree is the root module, also known as the entrypoint file. It often is the module that contains the root component.

Comparing to the render tree of the same app, there are similar structures but some notable differences:

* The nodes that make-up the tree represent modules, not components.
* Non-component modules, like `inspirations.js`, are also represented in this tree. The render tree only encapsulates components.
* `Copyright.js` appears under `App.js` but in the render tree, `Copyright`, the component, appears as a child of `InspirationGenerator`. This is because `InspirationGenerator` accepts JSX as [children props](/learn/passing-props-to-a-component#passing-jsx-as-children), so it renders `Copyright` as a child component but does not import the module.

Dependency trees are useful to determine what modules are necessary to run your React app. When building a React app for production, there is typically a build step that will bundle all the necessary JavaScript to ship to the client. The tool responsible for this is called a [bundler](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Understanding_client-side_tools/Overview#the_modern_tooling_ecosystem), and bundlers will use the dependency tree to determine what modules should be included.

As your app grows, often the bundle size does too. Large bundle sizes are expensive for a client to download and run. Large bundle sizes can delay the time for your UI to get drawn. Getting a sense of your app's dependency tree may help with debugging these issues.

[comment]: <> (perhaps we should also deep dive on conditional imports)

///////
گره ریشه درخت ماژول ریشه است که به عنوان فایل ورودی نیز شناخته می شود. اغلب ماژول حاوی مولفه ریشه است.

در مقایسه با درخت رندر همان برنامه، ساختارهای مشابهی وجود دارد اما تفاوت های قابل توجهی وجود دارد:

گره هایی که درخت را می سازند نشان دهنده ماژول ها هستند نه اجزا.
ماژول های غیر مولفه مانند inspirations.js نیز در این درخت نمایش داده می شوند. درخت رندر فقط اجزاء را در بر می گیرد.
Copyright.js در زیر App.js ظاهر می شود اما در درخت رندر، Copyright، جزء، به عنوان فرزند InspirationGenerator ظاهر می شود. این به این دلیل است که InspirationGenerator JSX را به عنوان ابزار کودک می پذیرد، بنابراین کپی رایت را به عنوان یک جزء فرزند ارائه می کند اما ماژول را وارد نمی کند.
درخت‌های وابستگی برای تعیین ماژول‌های لازم برای اجرای برنامه React مفید هستند. هنگام ساخت یک برنامه React برای تولید، معمولاً یک مرحله ساخت وجود دارد که تمام جاوا اسکریپت لازم را برای ارسال به مشتری بسته بندی می کند. ابزار مسئول این باندلر نامیده می شود و باندلرها از درخت وابستگی برای تعیین اینکه چه ماژول هایی باید گنجانده شوند استفاده می کنند.

همانطور که برنامه شما رشد می کند، اغلب اندازه بسته نیز رشد می کند. دانلود و اجرای سایز بسته‌های بزرگ برای مشتری گران است. اندازه‌های بسته بزرگ می‌توانند زمان ترسیم رابط کاربری شما را به تاخیر بیندازند. دریافت درک درخت وابستگی برنامه شما ممکن است به رفع اشکال این مشکلات کمک کند.

/////////

راس‌گره درخت، ماژول ریشه است که به عنوان پرونده ورودی نیز شناخته می‌شود. این ماژول معمولاً شامل کامپوننت ریشه است.

در مقایسه با درخت رندر همان برنامه، ساختارهای مشابهی وجود دارد اما تفاوت‌های قابل توجهی وجود دارد:

گره‌هایی که درخت را تشکیل می‌دهند، ماژول‌ها هستند، نه کامپوننت‌ها. ماژول‌های غیر کامپوننتی مانند inspirations.js نیز در این درخت نشان داده می‌شوند. درخت رندر فقط شامل کامپوننت‌ها است. Copyright.js در زیر App.js ظاهر می‌شود، اما در درخت رندر، Copyright به عنوان فرزند InspirationGenerator ظاهر می‌شود. این به این دلیل است که InspirationGenerator JSX را به عنوان props فرزندان خود قبول می‌کند، بنابراین Copyright را به عنوان یک کامپوننت فرزند رندر می‌کند اما ماژول را وارد نمی‌کند. درخت‌های وابستگی برای تعیین ماژول‌های مورد نیاز برای اجرای برنامه React شما مفید هستند. هنگام ساخت یک برنامه React برای تولید، معمولاً یک مرحله ساخت وجود دارد که تمام جاوا اسکریپت‌های لازم را برای ارسال به مشتری بسته‌بندی می‌کند. ابزاری که مسئول این کار است، باندلر نامیده می‌شود و باندلرها از درخت وابستگی برای تعیین ماژول‌هایی که باید شامل شوند، استفاده می‌کنند.

همانطور که برنامه شما رشد می‌کند، اغلب اندازه باندل نیز افزایش می‌یابد. اندازه باندل بزرگ، هزینه برای مشتری برای دانلود و اجرا است. اندازه باندل بزرگ می‌تواند زمان رسم واسط کاربری شما را تأخیر بیاورد. درک درخت وابستگی برنامه شما ممکن است به شما در اشکال زدایی این مسائل کمک کند.

<Recap>

* Trees are a common way to represent the relationship between entities. They are often used to model UI.
* Render trees represent the nested relationship between React components across a single render.
* With conditional rendering, the render tree may change across different renders. With different prop values, components may render different children components.
* Render trees help identify what the top-level and leaf components are. Top-level components affect the rendering performance of all components beneath them and leaf components are often re-rendered frequently. Identifying them is useful for understanding and debugging rendering performance.
* Dependency trees represent the module dependencies in a React app.
* Dependency trees are used by build tools to bundle the necessary code to ship an app.
* Dependency trees are useful for debugging large bundle sizes that slow time to paint and expose opportunities for optimizing what code is bundled.

//////

* درختان روشی متداول برای نشان دادن رابطه بین موجودیت ها هستند. آنها اغلب برای مدل سازی UI استفاده می شوند.
* درخت های رندر نشان دهنده رابطه تودرتو بین اجزای React در یک رندر هستند.
* با رندر شرطی، درخت رندر ممکن است در رندرهای مختلف تغییر کند. با مقادیر مختلف پایه، کامپوننت ها ممکن است اجزای فرزند متفاوتی را ارائه دهند.
* درختان رندر به شناسایی اجزای سطح بالا و برگ کمک می کنند. اجزای سطح بالا بر عملکرد رندر همه اجزای زیر آنها تأثیر می گذارد و اجزای برگ اغلب به طور مکرر دوباره رندر می شوند. شناسایی آنها برای درک و رفع اشکال عملکرد رندر مفید است.
* درخت های وابستگی نشان دهنده وابستگی های ماژول در یک برنامه React هستند.
* درخت‌های وابستگی توسط ابزارهای ساخت برای بسته‌بندی کدهای لازم برای ارسال یک برنامه استفاده می‌شوند.
* درخت‌های وابستگی برای اشکال‌زدایی اندازه‌های بسته بزرگ مفید هستند که زمان رنگ‌آمیزی را کاهش می‌دهند و فرصت‌هایی را برای بهینه‌سازی کدهای همراه ارائه می‌دهند.

////
* درخت‌ها روش معمولی برای نشان دادن رابطه بین موجودیت‌ها هستند. آن‌ها اغلب برای مدل‌سازی رابط کاربری استفاده می‌شوند.
* درخت‌های رندر، رابطه تو در تو بین کامپوننت‌های React را در یک عبور رندر نشان می‌دهند.
* با رندر کردن شرطی، درخت رندر ممکن است در رندرهای مختلف تغییر کند. با مقادیر prop متفاوت، کامپوننت‌ها ممکن است فرزندان متفاوتی را رندر کنند.
* درخت‌های رندر به شناسایی اجزای بالادست و برگ‌های کامپوننت‌ها کمک می‌کنند. اجزای بالادست بر روی عملکرد رندر تمام کامپوننت‌های زیر آن‌ها تأثیر می‌گذارند و برگ‌ها اغلب مجدداً رندر می‌شوند. شناسایی آن‌ها برای درک و اشکال‌زدایی عملکرد رندر مفید است.
* درخت‌های وابستگی، وابستگی‌های ماژول در یک برنامه React را نشان می‌دهند.
* ابزارهای ساخت از درخت‌های وابستگی برای بسته‌بندی کد لازم برای ارسال یک برنامه استفاده می‌کنند.
* درخت‌های وابستگی برای اشکال‌زدایی اندازه باندل بزرگ که زمان رسم رابط کاربری را کند می‌کند و فرصت‌های بهینه‌سازی کد بسته‌بندی شده را نشان می‌دهند، مفید هستند.

</Recap>


<Recap>

>این صفحه در مورد درخت render و درخت ماژول در React آموزش می‌دهد. درخت render یک درخت سلسله مراتبی از کامپوننت‌های React است که به ترتیب اجرای آن‌ها، UI را رندر می‌کند. این درخت، از ریشه شروع شده و به سمت پایین پیمایش می‌شود. از طرفی، درخت ماژول، یک درخت سلسله مراتبی از کامپوننت‌های React است که به ترتیب اجرای آن‌ها، ماژول‌هایی را رندر می‌کند. این درخت، از ریشه شروع شده و به سمت پایین پیمایش می‌شود.

>درخت ماژول، یک درخت سلسله مراتبی از کامپوننت‌های React است که به ترتیب اجرای آن‌ها، ماژول‌هایی را رندر می‌کند. این درخت، از ریشه شروع شده و به سمت پایین پیمایش می‌شود. هر ماژول، می‌تواند شامل چندین کامپوننت باشد. ماژول‌ها، به صورت مستقل و قابل استفاده مجدد طراحی می‌شوند و می‌توانند در برنامه‌های دیگر نیز استفاده شوند. برای مثال، فرض کنید یک برنامه‌ی React داریم که شامل چندین صفحه است. هر صفحه، می‌تواند شامل چندین ماژول باشد. هر ماژول، می‌تواند شامل چندین کامپوننت باشد. در نهایت، هر کامپوننت، می‌تواند شامل چندین المان باشد.

>درخت ماژول، یک درخت سلسله مراتبی از کامپوننت‌های React است که به ترتیب اجرای آن‌ها، ماژول‌هایی را رندر می‌کند. هر ماژول، می‌تواند شامل چندین کامپوننت باشد. ماژول‌ها، به صورت مستقل و قابل استفاده مجدد طراحی می‌شوند و می‌توانند در برنامه‌های دیگر نیز استفاده شوند. برای مثال، فرض کنید یک برنامه‌ی React داریم که شامل چندین صفحه است. هر صفحه، می‌تواند شامل چندین ماژول باشد. هر ماژول، می‌تواند شامل چندین کامپوننت باشد. در نهایت، هر کامپوننت، می‌تواند شامل چندین المان باشد.
</Recap>

مثالی از درخت رندر:

```js
import React, { useState } from 'react';
import ReactDOM from 'react-dom';

const App = () => {

  return (
    <div>
      <h1>React tutorial</h1>
      <h2>Render Tree</h2>
      <MyComponent/>
    </div>
  );
};

ReactDOM.render(<App />, document.getElementById('root'));

```

////////////////////////

در درخت ماژول ابتدا کامپوننت ریشه ی ما که والد باقی کامپوننت های ما هستن اجرا میشه و TrunkComponent رو رندر میکنه و TrunkComponent که تنه ی درخت ماژول ما هست BranchComponent که شاخه ی درخت ماژول ما هست رو رندر میکنه و BranchComponent هم میاد و ابتدا leafComponent 1 و سپس LeafComponent 2 رو رندر میکنه و ۱۱۱

```jsx
const Root = () => (
   <TrunkComponent/>
)


export default const TrunkComponent = () => (
  <BranchComponent/>
)


export default const BranchComponent = () => (
  <>
   <LeafComponent1/>
   <LeafComponent2/>
  </>
)


export default const LeafComponent1 = () => (
  <>
   <h2>hello World!</h2>
   <p>Leaf Component 1</p>
  </>
)

export default const LeafComponent2 = () => (
   <p>Leaf Component 2</p>
)

```

بله، البته. این کد یک مثال از درخت ماژول در React است:

```js


// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));

// src/App.js
import React from 'react';
import BranchComponent from './BranchComponent';

const App = () => (
  <BranchComponent />
);

export default App;

// src/BranchComponent.js
import React from 'react';
import TrunkComponent from './TrunkComponent';

const BranchComponent = () => (
  <TrunkComponent />
);

export default BranchComponent;

// src/TrunkComponent.js
import React from 'react';
import LeafComponent1 from './LeafComponent1';
import LeafComponent2 from './LeafComponent2';

const TrunkComponent = () => (
  <>
    <LeafComponent1 />
    <LeafComponent2 />
  </>
);

export default TrunkComponent;

// src/LeafComponent1.js
import React from 'react';

const LeafComponent1 = () => (
  <>
    <h2>Hello World!</h2>
    <p>Leaf Component 1</p>
  </>
);

export default LeafComponent1;

// src/LeafComponent2.js
import React from 'react';

const LeafComponent2 = () => (
  <p>Leaf Component 2</p>
);

export default LeafComponent2;
```

[TODO]: <> (Add challenges)
