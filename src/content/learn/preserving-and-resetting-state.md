---
title: Preserving and Resetting State(حفظ و بازنشانی state)
---

<Intro>

State is isolated between components. React keeps track of which state belongs to which component based on their place in the UI tree. You can control when to preserve state and when to reset it between re-renders.
////////////////
وضعیت بین اجزای مختلف جدا شده است. ری‌اکت بر اساس محل قرارگیری اجزای مختلف در درخت رابط کاربری، پیگیری می‌کند که هر وضعیت به کدام اجزا تعلق دارد. شما می‌توانید کنترل کنید که زمانی که وضعیت را حفظ کنید و زمانی که بین بازنمایی‌های مجدد آن را بازنشانی کنید1.

</Intro>

<YouWillLearn>

* When React chooses to preserve or reset the state
* How to force React to reset component's state
* How keys and types affect whether the state is preserved

////////////
* زمانی که ری‌اکت تصمیم می‌گیرد که وضعیت را حفظ کند یا بازنشانی کند
* چگونه می‌توانید ری‌اکت را مجبور به بازنشانی وضعیت اجزای خود کنید
* چگونگی تاثیر کلیدها و انواع بر حفظ وضعیت¹.

</YouWillLearn>

## State is tied to a position in the render tree(State به موقعیتی در درخت رندر گره خورده است) {/*state-is-tied-to-a-position-in-the-tree*/}

React builds [render trees](learn/understanding-your-ui-as-a-tree#the-render-tree) for the component structure in your UI.

When you give a component state, you might think the state "lives" inside the component. But the state is actually held inside React. React associates each piece of state it's holding with the correct component by where that component sits in the render tree.
//////////////////
ری‌اکت برای ساختار کامپوننت رابط کاربری شما، [درخت بازنمایی](learn/understanding-your-ui-as-a-tree#the-render-tree) را ایجاد می‌کند.

وقتی شما state به یک کامپوننت می‌دهید، ممکن است فکر کنید که وضعیت درون کامپوننت "زندگی" می‌کند. اما وضعیت در واقع در داخل ری‌اکت نگهداری می‌شود. ری‌اکت هر قطعه state که نگه می‌دارد را با کامپوننت مناسب درخت بازنمایی مرتبط می‌کند¹.

Here, there is only one `<Counter />` JSX tag, but it's rendered at two different positions:
////////////////
در اینجا، فقط یک برچسب JSX `<Counter />` وجود دارد، اما در دو موقعیت مختلف رندر می‌شود¹.

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const counter = <Counter />;
  return (
    <div>
      {counter}
      {counter}
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Here's how these look as a tree:    
در اینجا نحوه نمایش آنها به صورت درختان را مشاهده می‌کنید¹.

<DiagramGroup>

<Diagram name="preserving_state_tree" height={248} width={395} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. Each of the children are labeled 'Counter' and both contain a state bubble labeled 'count' with value 0.">

React tree

</Diagram>

</DiagramGroup>

**These are two separate counters because each is rendered at its own position in the tree.** You don't usually have to think about these positions to use React, but it can be useful to understand how it works.
/////////////////
**اینها دو شمارنده مجزا هستند زیرا هر کدام در موقعیت خاص خود در درخت رندر می شوند.** معمولاً برای استفاده از React لازم نیست به این موقعیت ها فکر کنید، اما درک نحوه عملکرد آن می تواند مفید باشد.

In React, each component on the screen has fully isolated state. For example, if you render two `Counter` components side by side, each of them will get its own, independent, `score` and `hover` states.
////////////////
در React، هر کامپوننت روی صفحه دارای حالت کاملا ایزوله است. برای مثال، اگر دو مؤلفه «Counter» را در کنار هم رندر کنید، هر کدام از آنها حالت‌های مستقل، «امتیاز» و «هوور» خود را خواهند داشت.

Try clicking both counters and notice they don't affect each other:
روی هر دو شمارنده کلیک کنید و متوجه شوید که روی یکدیگر تأثیری ندارند:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  return (
    <div>
      <Counter />
      <Counter />
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

As you can see, when one counter is updated, only the state for that component is updated:
/////////////////
همانطور که می‌بینید، هنگامی که یک شمارنده به‌روزرسانی می‌شود، فقط وضعیت آن کامپوننت به‌روزرسانی می‌شود:

<DiagramGroup>

<Diagram name="preserving_state_increment" height={248} width={441} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is labeled 'Counter' and contains a state bubble labeled 'count' with value 1. The state bubble of the right child is highlighted in yellow to indicate its value has updated.">

Updating state

</Diagram>

</DiagramGroup>


React will keep the state around for as long as you render the same component at the same position in the tree. To see this, increment both counters, then remove the second component by unchecking "Render the second counter" checkbox, and then add it back by ticking it again:
/////////////////////
React وضعیت را تا زمانی که شما همان کامپوننت را در همان موقعیت در درخت رندر کنید، نگه می‌دارد. برای دیدن این، هر دو شمارنده را افزایش دهید، سپس کامپوننت دوم را با عدم انتخاب جعبه انتخاب "رندر شمارنده دوم" حذف کنید و سپس با مجدداً تیک زدن آن را دوباره اضافه کنید..

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [showB, setShowB] = useState(true);
  return (
    <div>
      <Counter />
      {showB && <Counter />} 
      <label>
        <input
          type="checkbox"
          checked={showB}
          onChange={e => {
            setShowB(e.target.checked)
          }}
        />
        Render the second counter
      </label>
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Notice how the moment you stop rendering the second counter, its state disappears completely. That's because when React removes a component, it destroys its state.
/////////////////////////
توجه کنید که در لحظه‌ای که شما از رندر کردن شمارنده دوم متوقف می‌شوید، وضعیت آن به‌طور کامل ناپدید می‌شود. این به این دلیل است که هنگامی که React یک کامپوننت را حذف می‌کند، وضعیت آن را نیز از بین می‌برد.

<DiagramGroup>

<Diagram name="preserving_state_remove_component" height={253} width={422} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is missing, and in its place is a yellow 'poof' image, highlighting the component being deleted from the tree.">

Deleting a component

</Diagram>

</DiagramGroup>

When you tick "Render the second counter", a second `Counter` and its state are initialized from scratch (`score = 0`) and added to the DOM.
//////////////////////////////
هنگامی که شما جعبه انتخاب "رندر شمارنده دوم" را تیک می‌زنید، یک `Counter` دوم و وضعیت آن از ابتدا (`score = 0`) مقداردهی می‌شود و به DOM اضافه می‌شود.

<DiagramGroup>

<Diagram name="preserving_state_add_component" height={258} width={500} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The entire right child node is highlighted in yellow, indicating that it was just added to the tree.">

Adding a component

</Diagram>

</DiagramGroup>

**React preserves a component's state for as long as it's being rendered at its position in the UI tree.** If it gets removed, or a different component gets rendered at the same position, React discards its state.
//////////////////
**React وضعیت را تا زمانی که شما همان کامپوننت را در همان موقعیت در درخت رندر کنید، نگه می‌دارد.** اگر آن را حذف کنید یا کامپوننت دیگری در همان موقعیت رندر شود، React وضعیت را از بین می‌برد.

## Same component at the same position preserves state(یک component در همان موقعیت وضعیت را حفظ می کند.) {/*same-component-at-the-same-position-preserves-state*/}

In this example, there are two different `<Counter />` tags:
در این مثال، دو تگ مختلف `<Counter />` وجود دارد:
<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <Counter isFancy={true} /> 
      ) : (
        <Counter isFancy={false} /> 
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

When you tick or clear the checkbox, the counter state does not get reset. Whether `isFancy` is `true` or `false`, you always have a `<Counter />` as the first child of the `div` returned from the root `App` component:
////////////////////
هنگامی که شما جعبه انتخاب را تیک یا عدم تیک می‌کنید، وضعیت شمارنده بازنشانی نمی‌شود. برای هر دو حالت `isFancy` برابر با `true` یا `false`، همیشه یک `<Counter />` به عنوان فرزند اول کامپوننت `div` بازگشتی از کامپوننت `App` را دارید.

<DiagramGroup>

<Diagram name="preserving_state_same_component" height={461} width={600} alt="Diagram with two sections separated by an arrow transitioning between them. Each section contains a layout of components with a parent labeled 'App' containing a state bubble labeled isFancy. This component has one child labeled 'div', which leads to a prop bubble containing isFancy (highlighted in purple) passed down to the only child. The last child is labeled 'Counter' and contains a state bubble with label 'count' and value 3 in both diagrams. In the left section of the diagram, nothing is highlighted and the isFancy parent state value is false. In the right section of the diagram, the isFancy parent state value has changed to true and it is highlighted in yellow, and so is the props bubble below, which has also changed its isFancy value to true.">

Updating the `App` state does not reset the `Counter` because `Counter` stays in the same position
///////////////
یک جزء در همان موقعیت وضعیت را حفظ می کند. هنگامی که وضعیت App به روز می شود، Counter باقی می ماند و وضعیت آن را بازنشانی نمی کند.

</Diagram>

</DiagramGroup>


It's the same component at the same position, so from React's perspective, it's the same counter.
//////////////////
یک جزء در همان موقعیت وضعیت را حفظ می کند. هنگامی که وضعیت App به روز می شود، Counter باقی می ماند و وضعیت آن را بازنشانی نمی کند.

<Pitfall>

Remember that **it's the position in the UI tree--not in the JSX markup--that matters to React!** This component has two `return` clauses with different `<Counter />` JSX tags inside and outside the `if`:
///////////////////
یادآور شوید که برای React، موقعیت درخت رابط کاربری و نه نشانگر JSX مهم است! این کامپوننت دارای دو عبارت return با برچسب‌های JSX متفاوت `<Counter />` در داخل و خارج از if است.

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  if (isFancy) {
    return (
      <div>
        <Counter isFancy={true} />
        <label>
          <input
            type="checkbox"
            checked={isFancy}
            onChange={e => {
              setIsFancy(e.target.checked)
            }}
          />
          Use fancy styling
        </label>
      </div>
    );
  }
  return (
    <div>
      <Counter isFancy={false} />
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

You might expect the state to reset when you tick checkbox, but it doesn't! This is because **both of these `<Counter />` tags are rendered at the same position.** React doesn't know where you place the conditions in your function. All it "sees" is the tree you return.

In both cases, the `App` component returns a `<div>` with `<Counter />` as a first child. To React, these two counters have the same "address": the first child of the first child of the root. This is how React matches them up between the previous and next renders, regardless of how you structure your logic.
///////////////////
شاید انتظار داشته باشید که وضعیت شمارنده با تیک زدن جعبه انتخاب بازنشانی شود، اما این اتفاق نمی افتد! این به این دلیل است که هر دو برچسب `<Counter />` در همان موقعیت رندر می شوند. React نمی داند که شما شرایط را در تابع خود کجا قرار داده اید. همه چیزی که React "می بیند"، درختی است که شما باز می گردانید.

در هر دو حالت، کامپوننت App یک `<div>` با `<Counter />` به عنوان فرزند اول باز می گرداند. برای React، این دو شمارنده "آدرس" یکسانی دارند: فرزند اول فرزند اول ریشه. این چگونگی تطبیق دادن React بین رندرهای قبلی و بعدی است، بدون توجه به اینکه منطق شما چگونه ساختاردهی شده است..


</Pitfall>

## Different components at the same position reset state(اجزای مختلف در همان موقعیت وضعیت را بازنشانی می کنند.) {/*different-components-at-the-same-position-reset-state*/}

In this example, ticking the checkbox will replace `<Counter>` with a `<p>`:
در این مثال، تیک زدن چک باکس، `<Counter>` را با `<p>` جایگزین می کند:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isPaused, setIsPaused] = useState(false);
  return (
    <div>
      {isPaused ? (
        <p>See you later!</p> 
      ) : (
        <Counter /> 
      )}
      <label>
        <input
          type="checkbox"
          checked={isPaused}
          onChange={e => {
            setIsPaused(e.target.checked)
          }}
        />
        Take a break
      </label>
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Here, you switch between _different_ component types at the same position. Initially, the first child of the `<div>` contained a `Counter`. But when you swapped in a `p`, React removed the `Counter` from the UI tree and destroyed its state.
//////////////////
در اینجا، شما بین انواع مختلفی از کامپوننت در همان موقعیت تغییر می‌دهید. در ابتدا، فرزند اول `<div>` شامل یک Counter بود. اما وقتی شما یک p جایگزین کردید، React شمارنده را از درخت رابط کاربری حذف کرد و وضعیت آن را نابود کرد.

<DiagramGroup>

<Diagram name="preserving_state_diff_pt1" height={290} width={753} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'Counter' containing a state bubble labeled 'count' with value 3. The middle section has the same 'div' parent, but the child component has now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'p', highlighted in yellow.">

When `Counter` changes to `p`, the `Counter` is deleted and the `p` is added
هنگامی که Counter به p تغییر می‌کند، Counter حذف شده و p اضافه می‌شود.


</Diagram>

</DiagramGroup>

<DiagramGroup>

<Diagram name="preserving_state_diff_pt2" height={290} width={753} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'p'. The middle section has the same 'div' parent, but the child component has now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, highlighted in yellow.">

When switching back, the `p` is deleted and the `Counter` is added

</Diagram>

</DiagramGroup>

Also, **when you render a different component in the same position, it resets the state of its entire subtree.** To see how this works, increment the counter and then tick the checkbox:
/////////////////
همچنین، هنگامی که شما یک کامپوننت متفاوت را در همان موقعیت رندر می‌کنید، وضعیت کل زیردرخت را بازنشانی می‌کند. برای دیدن اینکه این چگونه کار می‌کند، شمارنده را افزایش دهید و سپس جعبه انتخاب را تیک بزنید:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <div>
          <Counter isFancy={true} /> 
        </div>
      ) : (
        <section>
          <Counter isFancy={false} />
        </section>
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

The counter state gets reset when you click the checkbox. Although you render a `Counter`, the first child of the `div` changes from a `div` to a `section`. When the child `div` was removed from the DOM, the whole tree below it (including the `Counter` and its state) was destroyed as well.
//////////////////
هنگامی که شما جعبه انتخاب را تیک می‌زنید، وضعیت شمارنده بازنشانی می‌شود. اگرچه شما یک `Counter` را رندر می‌کنید، فرزند اول `<div>` از `div` به `section` تغییر می‌کند. هنگامی که فرزند `div` از DOM حذف شد، کل درخت زیر آن (شامل `Counter` و وضعیت آن) نیز نابود شد.


<DiagramGroup>

<Diagram name="preserving_state_diff_same_pt1" height={350} width={794} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'section', which has a single child labeled 'Counter' containing a state bubble labeled 'count' with value 3. The middle section has the same 'div' parent, but the child components have now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'div', highlighted in yellow, also with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, all highlighted in yellow.">

When `section` changes to `div`, the `section` is deleted and the new `div` is added
/////////////
هنگامی که section به div تغییر می‌کند، section حذف شده و div جدید اضافه می‌شود.

</Diagram>

</DiagramGroup>

<DiagramGroup>

<Diagram name="preserving_state_diff_same_pt2" height={350} width={794} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'div', which has a single child labeled 'Counter' containing a state bubble labeled 'count' with value 0. The middle section has the same 'div' parent, but the child components have now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'section', highlighted in yellow, also with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, all highlighted in yellow.">

When switching back, the `div` is deleted and the new `section` is added
////////////
هنگام بازگشت، div حذف شده و section جدید اضافه می‌شود 1.

</Diagram>

</DiagramGroup>

As a rule of thumb, **if you want to preserve the state between re-renders, the structure of your tree needs to "match up"** from one render to another. If the structure is different, the state gets destroyed because React destroys state when it removes a component from the tree.
///////////////////
به طور کلی، اگر می‌خواهید وضعیت را بین بازنشانی‌های مجدد حفظ کنید، ساختار درخت شما باید از یک بازنشانی به بازنشانی دیگر “مطابقت داشته باشد”. اگر ساختار متفاوت باشد، وضعیت نابود می‌شود زیرا React وضعیت را هنگام حذف یک کامپوننت از درخت نابود می‌کند 1.

<Pitfall>

This is why you should not nest component function definitions.

Here, the `MyTextField` component function is defined *inside* `MyComponent`:
///////////
این همان دلیلی است که شما نباید تعریف توابع کامپوننت را تو در تو کنید.

در اینجا، تابع کامپوننت `MyTextField` *درون* `MyComponent` تعریف شده است.

<Sandpack>

```js
import { useState } from 'react';

export default function MyComponent() {
  const [counter, setCounter] = useState(0);

  function MyTextField() {
    const [text, setText] = useState('');

    return (
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
    );
  }

  return (
    <>
      <MyTextField />
      <button onClick={() => {
        setCounter(counter + 1)
      }}>Clicked {counter} times</button>
    </>
  );
}
```

</Sandpack>


Every time you click the button, the input state disappears! This is because a *different* `MyTextField` function is created for every render of `MyComponent`. You're rendering a *different* component in the same position, so React resets all state below. This leads to bugs and performance problems. To avoid this problem, **always declare component functions at the top level, and don't nest their definitions.**
///////////////
هر بار که دکمه را کلیک می‌کنید، وضعیت ورودی ناپدید می‌شود! این به این دلیل است که برای هر رندر از MyComponent، یک تابع متفاوت MyTextField ایجاد می‌شود. شما در همان موقعیت یک کامپوننت متفاوت را رندر می‌کنید، بنابراین React تمام وضعیت‌های زیرین را بازنشانی می‌کند. این باعث ایجاد باگ‌ها و مشکلات عملکردی می‌شود. برای جلوگیری از این مشکل، همیشه توابع کامپوننت را در سطح بالا تعریف کنید و تعریف‌های آن‌ها را تو در تو نکنید.
</Pitfall>

## Resetting state at the same position(تنظیم مجدد state در همان موقعیت) {/*resetting-state-at-the-same-position*/}

By default, React preserves state of a component while it stays at the same position. Usually, this is exactly what you want, so it makes sense as the default behavior. But sometimes, you may want to reset a component's state. Consider this app that lets two players keep track of their scores during each turn:
//////////////
به طور پیش‌فرض، React وضعیت یک کامپوننت را تا زمانی که در همان موقعیت باقی بماند، حفظ می‌کند. معمولاً، این دقیقاً آنچه که می‌خواهید است، بنابراین به عنوان رفتار پیش‌فرض منطقی است. اما گاهی اوقات ممکن است بخواهید وضعیت یک کامپوننت را بازنشانی کنید. به عنوان مثال، در نظر بگیرید این برنامه که به دو بازیکن اجازه می‌دهد در هر نوبت امتیازات خود را نگه دارند:

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter person="Taylor" />
      ) : (
        <Counter person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Currently, when you change the player, the score is preserved. The two `Counter`s appear in the same position, so React sees them as *the same* `Counter` whose `person` prop has changed.

But conceptually, in this app they should be two separate counters. They might appear in the same place in the UI, but one is a counter for Taylor, and another is a counter for Sarah.

There are two ways to reset state when switching between them:

1. Render components in different positions
2. Give each component an explicit identity with `key`
////////////////

در حال حاضر، هنگامی که شما بازیکن را تغییر می‌دهید، امتیاز نگه داشته می‌شود. دو `Counter` در همان موقعیت ظاهر می‌شوند، بنابراین React آن‌ها را به عنوان یک `Counter` *مشاهده می‌کند* که `person` آن تغییر کرده است.

اما از نظر مفهومی، در این برنامه آن‌ها باید دو شمارنده جداگانه باشند. آن‌ها ممکن است در همان محل در رابط کاربری ظاهر شوند، اما یکی شمارنده تیلور و دیگری شمارنده سارا است.

دو روش برای بازنشانی وضعیت هنگام تغییر بین آن‌ها وجود دارد:

1. رندر کردن کامپوننت‌ها در موقعیت‌های مختلف
2. به هر کامپوننت یک هویت صریح با `key` بدهید..

### Option 1: Rendering a component in different positions(گزینه 1: رندر کردن یک جزء در موقعیت های مختلف) {/*option-1-rendering-a-component-in-different-positions*/}

If you want these two `Counter`s to be independent, you can render them in two different positions:
///////////////
اگر می‌خواهید این دو Counter مستقل باشند، می‌توانید آن‌ها را در دو موقعیت متفاوت رندر کنید.
<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

* Initially, `isPlayerA` is `true`. So the first position contains `Counter` state, and the second one is empty.
* When you click the "Next player" button the first position clears but the second one now contains a `Counter`.
/////////////////
* در ابتدا، `isPlayerA` برابر `true` است. بنابراین موقعیت اول وضعیت `Counter` را دارد و موقعیت دوم خالی است.
* هنگامی که دکمه "بازیکن بعدی" را کلیک می‌کنید، موقعیت اول پاک می‌شود اما موقعیت دوم حالا شامل یک `Counter` است ¹.

<DiagramGroup>

<Diagram name="preserving_state_diff_position_p1" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'true'. The only child, arranged to the left, is labeled Counter with a state bubble labeled 'count' and value 0. All of the left child is highlighted in yellow, indicating it was added.">

Initial state

</Diagram>

<Diagram name="preserving_state_diff_position_p2" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'false'. The state bubble is highlighted in yellow, indicating that it has changed. The left child is replaced with a yellow 'poof' image indicating that it has been deleted and there is a new child on the right, highlighted in yellow indicating that it was added. The new child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0.">

Clicking "next"

</Diagram>

<Diagram name="preserving_state_diff_position_p3" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'true'. The state bubble is highlighted in yellow, indicating that it has changed. There is a new child on the left, highlighted in yellow indicating that it was added. The new child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is replaced with a yellow 'poof' image indicating that it has been deleted.">

Clicking "next" again

</Diagram>

</DiagramGroup>

Each `Counter`'s state gets destroyed each time it's removed from the DOM. This is why they reset every time you click the button.

This solution is convenient when you only have a few independent components rendered in the same place. In this example, you only have two, so it's not a hassle to render both separately in the JSX.
////////////////
هر بار که Counter از DOM حذف می‌شود، وضعیت آن نابود می‌شود. به همین دلیل هر بار که دکمه را کلیک می‌کنید، بازنشانی می‌شوند.

این راه‌حل در صورتی مناسب است که تنها چند کامپوننت مستقل در همان موقعیت رندر شده باشند. در این مثال، تنها دو کامپوننت دارید، بنابراین رندر کردن هر دو به صورت جداگانه در JSX مشکلی ندارد.

### Option 2: Resetting state with a key(گزینه 2: تنظیم مجدد حالت با یک کلید) {/*option-2-resetting-state-with-a-key*/}

There is also another, more generic, way to reset a component's state.
////////////////
همچنین، راه دیگری برای بازنشانی وضعیت یک کامپوننت وجود دارد که بیشتر عمومی است.

You might have seen `key`s when [rendering lists.](/learn/rendering-lists#keeping-list-items-in-order-with-key) Keys aren't just for lists! You can use keys to make React distinguish between any components. By default, React uses order within the parent ("first counter", "second counter") to discern between components. But keys let you tell React that this is not just a *first* counter, or a *second* counter, but a specific counter--for example, *Taylor's* counter. This way, React will know *Taylor's* counter wherever it appears in the tree!
////////////////
شاید کلیدها را هنگام [رندر لیست‌ها](https://learnreactapp.com/rendering-lists#keeping-list-items-in-order-with-key) دیده باشید. کلیدها فقط برای لیست‌ها نیستند! شما می‌توانید از کلیدها برای تمایز دادن بین هر کامپوننتی استفاده کنید. به طور پیش‌فرض، React از ترتیب درون والدین ("شمارنده اول"، "شمارنده دوم") برای تمایز دادن بین کامپوننت‌ها استفاده می‌کند. اما کلیدها به شما اجازه می‌دهند به React بگویید که این فقط یک *شمارنده اول* یا یک *شمارنده دوم* نیست، بلکه یک شمارنده خاص است - به عنوان مثال، شمارنده *تیلور*. به این ترتیب، React هر جایی که در درخت ظاهر شود، شمارنده *تیلور* را خواهد شناخت!.

In this example, the two `<Counter />`s don't share state even though they appear in the same place in JSX:
///////////////
در این مثال، دو `<Counter />` حالت را به اشتراک نمی‌گذارند، حتی اگر در همان مکان در JSX ظاهر شوند.

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Switching between Taylor and Sarah does not preserve the state. This is because **you gave them different `key`s:**
////////////////////////
"Switching between Taylor and Sarah does not preserve the state. This is because you gave them different keys" به فارسی می‌شود: "تغییر دادن بین Taylor و Sarah حالت را حفظ نمی‌کند. این به این دلیل است که شما به آن‌ها کلید‌های متفاوتی داده‌اید."

```js
{isPlayerA ? (
  <Counter key="Taylor" person="Taylor" />
) : (
  <Counter key="Sarah" person="Sarah" />
)}
```

Specifying a `key` tells React to use the `key` itself as part of the position, instead of their order within the parent. This is why, even though you render them in the same place in JSX, React sees them as two different counters, and so they will never share state. Every time a counter appears on the screen, its state is created. Every time it is removed, its state is destroyed. Toggling between them resets their state over and over.
///////////////////
"Specifying a `key` tells React to use the `key` itself as part of the position, instead of their order within the parent. This is why, even though you render them in the same place in JSX, React sees them as two different counters, and so they will never share state. Every time a counter appears on the screen, its state is created. Every time it is removed, its state is destroyed. Toggling between them resets their state over and over." به فارسی می‌شود: "تعیین کلید به React می‌گوید که از خود کلید به عنوان بخشی از موقعیت استفاده کند، به جای ترتیب آن‌ها درون والدین. به همین دلیل، با وجود اینکه آن‌ها را در همان مکان در JSX رندر می‌کنید، React آن‌ها را به عنوان دو شمارنده متفاوت می‌بیند و بنابراین هرگز حالت را به اشتراک نمی‌گذارند. هر بار که یک شمارنده در صفحه نمایش ظاهر می‌شود، حالت آن ایجاد می‌شود. هر بار که حذف می‌شود، حالت آن نابود می‌شود. تغییر دادن بین آن‌ها حالت آن‌ها را مرتباً بازنشانی می‌کند.".

<Note>

Remember that keys are not globally unique. They only specify the position *within the parent*.
////////////////////
"Remember that keys are not globally unique. They only specify the position *within the parent*." به فارسی می‌شود: "به خاطر داشته باشید که کلیدها به صورت جهانی منحصر به فرد نیستند. آن‌ها تنها موقعیت را *درون والدین* مشخص می‌کنند."

</Note>

### Resetting a form with a key(بازنشانی فرم با کلید) {/*resetting-a-form-with-a-key*/}

Resetting state with a key is particularly useful when dealing with forms.
//////////
به فارسی می‌شود: “تنظیم مجدد حالت با استفاده از کلید به خصوص در هنگام کار با فرم‌ها مفید است.”

In this chat app, the `<Chat>` component contains the text input state:
///////////////
در این برنامه چت، کامپوننت `<Chat>` شامل حالت ورودی متن است.

<Sandpack>

```js App.js
import { useState } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';

export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat contact={to} />
    </div>
  )
}

const contacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js ContactList.js
export default function ContactList({
  selectedContact,
  contacts,
  onSelect
}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact);
            }}>
              {contact.name}
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js Chat.js
import { useState } from 'react';

export default function Chat({ contact }) {
  const [text, setText] = useState('');
  return (
    <section className="chat">
      <textarea
        value={text}
        placeholder={'Chat to ' + contact.name}
        onChange={e => setText(e.target.value)}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat, .contact-list {
  float: left;
  margin-bottom: 20px;
}
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

Try entering something into the input, and then press "Alice" or "Bob" to choose a different recipient. You will notice that the input state is preserved because the `<Chat>` is rendered at the same position in the tree.
//////////////////////////
 به فارسی می‌شود: “سعی کنید چیزی را در ورودی وارد کنید، سپس “Alice” یا “Bob” را برای انتخاب گیرنده متفاوت فشار دهید. شما متوجه خواهید شد که حالت ورودی به دلیل اینکه `<Chat>` در همان موقعیت در درخت رندر می‌شود، حفظ شده است.”

**In many apps, this may be the desired behavior, but not in a chat app!** You don't want to let the user send a message they already typed to a wrong person due to an accidental click. To fix it, add a `key`:
/////////////////
“در بسیاری از برنامه‌ها، این ممکن است رفتار مورد نظر باشد، اما در یک برنامه چت نیست! شما نمی‌خواهید کاربر پیامی را که قبلاً تایپ کرده است به دلیل کلیک تصادفی به شخص اشتباهی ارسال کند. برای رفع این مشکل، یک key اضافه کنید.”

```js
<Chat key={to.id} contact={to} />
```

This ensures that when you select a different recipient, the `Chat` component will be recreated from scratch, including any state in the tree below it. React will also re-create the DOM elements instead of reusing them.
///////////////
“این اطمینان را به شما می‌دهد که هرگاه شما گیرنده متفاوتی را انتخاب کنید، کامپوننت `Chat` از ابتدا بازسازی می‌شود، شامل هر حالتی در درخت زیر آن. React همچنین عناصر DOM را دوباره ایجاد می‌کند، به جای استفاده مجدد از آن‌ها.”

Now switching the recipient always clears the text field:
/////////////////
“حالا هرگاه گیرنده را تغییر دهید، همیشه فیلد متن پاک می‌شود.” به فارسی می‌شود: “حالا هرگاه گیرنده را تغییر دهید، همیشه فیلد متن پاک می‌شود.”

<Sandpack>

```js App.js
import { useState } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';

export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat key={to.id} contact={to} />
    </div>
  )
}

const contacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js ContactList.js
export default function ContactList({
  selectedContact,
  contacts,
  onSelect
}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact);
            }}>
              {contact.name}
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js Chat.js
import { useState } from 'react';

export default function Chat({ contact }) {
  const [text, setText] = useState('');
  return (
    <section className="chat">
      <textarea
        value={text}
        placeholder={'Chat to ' + contact.name}
        onChange={e => setText(e.target.value)}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat, .contact-list {
  float: left;
  margin-bottom: 20px;
}
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

<DeepDive>

#### Preserving state for removed components {/*preserving-state-for-removed-components*/}

In a real chat app, you'd probably want to recover the input state when the user selects the previous recipient again. There are a few ways to keep the state "alive" for a component that's no longer visible:

- You could render _all_ chats instead of just the current one, but hide all the others with CSS. The chats would not get removed from the tree, so their local state would be preserved. This solution works great for simple UIs. But it can get very slow if the hidden trees are large and contain a lot of DOM nodes.
- You could [lift the state up](/learn/sharing-state-between-components) and hold the pending message for each recipient in the parent component. This way, when the child components get removed, it doesn't matter, because it's the parent that keeps the important information. This is the most common solution.
- You might also use a different source in addition to React state. For example, you probably want a message draft to persist even if the user accidentally closes the page. To implement this, you could have the `Chat` component initialize its state by reading from the [`localStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage), and save the drafts there too.

No matter which strategy you pick, a chat _with Alice_ is conceptually distinct from a chat _with Bob_, so it makes sense to give a `key` to the `<Chat>` tree based on the current recipient.

</DeepDive>

<Recap>

- React keeps state for as long as the same component is rendered at the same position.
- State is not kept in JSX tags. It's associated with the tree position in which you put that JSX.
- You can force a subtree to reset its state by giving it a different key.
- Don't nest component definitions, or you'll reset state by accident.

</Recap>



<Challenges>

#### Fix disappearing input text {/*fix-disappearing-input-text*/}

This example shows a message when you press the button. However, pressing the button also accidentally resets the input. Why does this happen? Fix it so that pressing the button does not reset the input text.

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  if (showHint) {
    return (
      <div>
        <p><i>Hint: Your favorite city?</i></p>
        <Form />
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      </div>
    );
  }
  return (
    <div>
      <Form />
      <button onClick={() => {
        setShowHint(true);
      }}>Show hint</button>
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>

<Solution>

The problem is that `Form` is rendered in different positions. In the `if` branch, it is the second child of the `<div>`, but in the `else` branch, it is the first child. Therefore, the component type in each position changes. The first position changes between holding a `p` and a `Form`, while the second position changes between holding a `Form` and a `button`. React resets the state every time the component type changes.

The easiest solution is to unify the branches so that `Form` always renders in the same position:

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  return (
    <div>
      {showHint &&
        <p><i>Hint: Your favorite city?</i></p>
      }
      <Form />
      {showHint ? (
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      ) : (
        <button onClick={() => {
          setShowHint(true);
        }}>Show hint</button>
      )}
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>


Technically, you could also add `null` before `<Form />` in the `else` branch to match the `if` branch structure:

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  if (showHint) {
    return (
      <div>
        <p><i>Hint: Your favorite city?</i></p>
        <Form />
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      </div>
    );
  }
  return (
    <div>
      {null}
      <Form />
      <button onClick={() => {
        setShowHint(true);
      }}>Show hint</button>
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>

This way, `Form` is always the second child, so it stays in the same position and keeps its state. But this approach is much less obvious and introduces a risk that someone else will remove that `null`.

</Solution>

#### Swap two form fields {/*swap-two-form-fields*/}

This form lets you enter first and last name. It also has a checkbox controlling which field goes first. When you tick the checkbox, the "Last name" field will appear before the "First name" field.

It almost works, but there is a bug. If you fill in the "First name" input and tick the checkbox, the text will stay in the first input (which is now "Last name"). Fix it so that the input text *also* moves when you reverse the order.

<Hint>

It seems like for these fields, their position within the parent is not enough. Is there some way to tell React how to match up the state between re-renders?

</Hint>

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [reverse, setReverse] = useState(false);
  let checkbox = (
    <label>
      <input
        type="checkbox"
        checked={reverse}
        onChange={e => setReverse(e.target.checked)}
      />
      Reverse order
    </label>
  );
  if (reverse) {
    return (
      <>
        <Field label="Last name" /> 
        <Field label="First name" />
        {checkbox}
      </>
    );
  } else {
    return (
      <>
        <Field label="First name" /> 
        <Field label="Last name" />
        {checkbox}
      </>
    );    
  }
}

function Field({ label }) {
  const [text, setText] = useState('');
  return (
    <label>
      {label}:{' '}
      <input
        type="text"
        value={text}
        placeholder={label}
        onChange={e => setText(e.target.value)}
      />
    </label>
  );
}
```

```css
label { display: block; margin: 10px 0; }
```

</Sandpack>

<Solution>

Give a `key` to both `<Field>` components in both `if` and `else` branches. This tells React how to "match up" the correct state for either `<Field>` even if their order within the parent changes:

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [reverse, setReverse] = useState(false);
  let checkbox = (
    <label>
      <input
        type="checkbox"
        checked={reverse}
        onChange={e => setReverse(e.target.checked)}
      />
      Reverse order
    </label>
  );
  if (reverse) {
    return (
      <>
        <Field key="lastName" label="Last name" /> 
        <Field key="firstName" label="First name" />
        {checkbox}
      </>
    );
  } else {
    return (
      <>
        <Field key="firstName" label="First name" /> 
        <Field key="lastName" label="Last name" />
        {checkbox}
      </>
    );    
  }
}

function Field({ label }) {
  const [text, setText] = useState('');
  return (
    <label>
      {label}:{' '}
      <input
        type="text"
        value={text}
        placeholder={label}
        onChange={e => setText(e.target.value)}
      />
    </label>
  );
}
```

```css
label { display: block; margin: 10px 0; }
```

</Sandpack>

</Solution>

#### Reset a detail form {/*reset-a-detail-form*/}

This is an editable contact list. You can edit the selected contact's details and then either press "Save" to update it, or "Reset" to undo your changes.

When you select a different contact (for example, Alice), the state updates but the form keeps showing the previous contact's details. Fix it so that the form gets reset when the selected contact changes.

<Sandpack>

```js App.js
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
        initialData={selectedContact}
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

```js ContactList.js
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

```js EditContact.js
import { useState } from 'react';

export default function EditContact({ initialData, onSave }) {
  const [name, setName] = useState(initialData.name);
  const [email, setEmail] = useState(initialData.email);
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
          id: initialData.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(initialData.name);
        setEmail(initialData.email);
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

<Solution>

Give `key={selectedId}` to the `EditContact` component. This way, switching between different contacts will reset the form:

<Sandpack>

```js App.js
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
        key={selectedId}
        initialData={selectedContact}
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

```js ContactList.js
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

```js EditContact.js
import { useState } from 'react';

export default function EditContact({ initialData, onSave }) {
  const [name, setName] = useState(initialData.name);
  const [email, setEmail] = useState(initialData.email);
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
          id: initialData.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(initialData.name);
        setEmail(initialData.email);
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

#### Clear an image while it's loading {/*clear-an-image-while-its-loading*/}

When you press "Next", the browser starts loading the next image. However, because it's displayed in the same `<img>` tag, by default you would still see the previous image until the next one loads. This may be undesirable if it's important for the text to always match the image. Change it so that the moment you press "Next", the previous image immediately clears.

<Hint>

Is there a way to tell React to re-create the DOM instead of reusing it?

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const hasNext = index < images.length - 1;

  function handleClick() {
    if (hasNext) {
      setIndex(index + 1);
    } else {
      setIndex(0);
    }
  }

  let image = images[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h3>
        Image {index + 1} of {images.length}
      </h3>
      <img src={image.src} />
      <p>
        {image.place}
      </p>
    </>
  );
}

let images = [{
  place: 'Penang, Malaysia',
  src: 'https://i.imgur.com/FJeJR8M.jpg'
}, {
  place: 'Lisbon, Portugal',
  src: 'https://i.imgur.com/dB2LRbj.jpg'
}, {
  place: 'Bilbao, Spain',
  src: 'https://i.imgur.com/z08o2TS.jpg'
}, {
  place: 'Valparaíso, Chile',
  src: 'https://i.imgur.com/Y3utgTi.jpg'
}, {
  place: 'Schwyz, Switzerland',
  src: 'https://i.imgur.com/JBbMpWY.jpg'
}, {
  place: 'Prague, Czechia',
  src: 'https://i.imgur.com/QwUKKmF.jpg'
}, {
  place: 'Ljubljana, Slovenia',
  src: 'https://i.imgur.com/3aIiwfm.jpg'
}];
```

```css
img { width: 150px; height: 150px; }
```

</Sandpack>

<Solution>

You can provide a `key` to the `<img>` tag. When that `key` changes, React will re-create the `<img>` DOM node from scratch. This causes a brief flash when each image loads, so it's not something you'd want to do for every image in your app. But it makes sense if you want to ensure the image always matches the text.

<Sandpack>

```js
import { useState } from 'react';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const hasNext = index < images.length - 1;

  function handleClick() {
    if (hasNext) {
      setIndex(index + 1);
    } else {
      setIndex(0);
    }
  }

  let image = images[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h3>
        Image {index + 1} of {images.length}
      </h3>
      <img key={image.src} src={image.src} />
      <p>
        {image.place}
      </p>
    </>
  );
}

let images = [{
  place: 'Penang, Malaysia',
  src: 'https://i.imgur.com/FJeJR8M.jpg'
}, {
  place: 'Lisbon, Portugal',
  src: 'https://i.imgur.com/dB2LRbj.jpg'
}, {
  place: 'Bilbao, Spain',
  src: 'https://i.imgur.com/z08o2TS.jpg'
}, {
  place: 'Valparaíso, Chile',
  src: 'https://i.imgur.com/Y3utgTi.jpg'
}, {
  place: 'Schwyz, Switzerland',
  src: 'https://i.imgur.com/JBbMpWY.jpg'
}, {
  place: 'Prague, Czechia',
  src: 'https://i.imgur.com/QwUKKmF.jpg'
}, {
  place: 'Ljubljana, Slovenia',
  src: 'https://i.imgur.com/3aIiwfm.jpg'
}];
```

```css
img { width: 150px; height: 150px; }
```

</Sandpack>

</Solution>

#### Fix misplaced state in the list {/*fix-misplaced-state-in-the-list*/}

In this list, each `Contact` has state that determines whether "Show email" has been pressed for it. Press "Show email" for Alice, and then tick the "Show in reverse order" checkbox. You will notice that it's _Taylor's_ email that is expanded now, but Alice's--which has moved to the bottom--appears collapsed.

Fix it so that the expanded state is associated with each contact, regardless of the chosen ordering.

<Sandpack>

```js App.js
import { useState } from 'react';
import Contact from './Contact.js';

export default function ContactList() {
  const [reverse, setReverse] = useState(false);

  const displayedContacts = [...contacts];
  if (reverse) {
    displayedContacts.reverse();
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          value={reverse}
          onChange={e => {
            setReverse(e.target.checked)
          }}
        />{' '}
        Show in reverse order
      </label>
      <ul>
        {displayedContacts.map((contact, i) =>
          <li key={i}>
            <Contact contact={contact} />
          </li>
        )}
      </ul>
    </>
  );
}

const contacts = [
  { id: 0, name: 'Alice', email: 'alice@mail.com' },
  { id: 1, name: 'Bob', email: 'bob@mail.com' },
  { id: 2, name: 'Taylor', email: 'taylor@mail.com' }
];
```

```js Contact.js
import { useState } from 'react';

export default function Contact({ contact }) {
  const [expanded, setExpanded] = useState(false);
  return (
    <>
      <p><b>{contact.name}</b></p>
      {expanded &&
        <p><i>{contact.email}</i></p>
      }
      <button onClick={() => {
        setExpanded(!expanded);
      }}>
        {expanded ? 'Hide' : 'Show'} email
      </button>
    </>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li {
  margin-bottom: 20px;
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

<Solution>

The problem is that this example was using index as a `key`:

```js
{displayedContacts.map((contact, i) =>
  <li key={i}>
```

However, you want the state to be associated with _each particular contact_.

Using the contact ID as a `key` instead fixes the issue:

<Sandpack>

```js App.js
import { useState } from 'react';
import Contact from './Contact.js';

export default function ContactList() {
  const [reverse, setReverse] = useState(false);

  const displayedContacts = [...contacts];
  if (reverse) {
    displayedContacts.reverse();
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          value={reverse}
          onChange={e => {
            setReverse(e.target.checked)
          }}
        />{' '}
        Show in reverse order
      </label>
      <ul>
        {displayedContacts.map(contact =>
          <li key={contact.id}>
            <Contact contact={contact} />
          </li>
        )}
      </ul>
    </>
  );
}

const contacts = [
  { id: 0, name: 'Alice', email: 'alice@mail.com' },
  { id: 1, name: 'Bob', email: 'bob@mail.com' },
  { id: 2, name: 'Taylor', email: 'taylor@mail.com' }
];
```

```js Contact.js
import { useState } from 'react';

export default function Contact({ contact }) {
  const [expanded, setExpanded] = useState(false);
  return (
    <>
      <p><b>{contact.name}</b></p>
      {expanded &&
        <p><i>{contact.email}</i></p>
      }
      <button onClick={() => {
        setExpanded(!expanded);
      }}>
        {expanded ? 'Hide' : 'Show'} email
      </button>
    </>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li {
  margin-bottom: 20px;
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

State is associated with the tree position. A `key` lets you specify a named position instead of relying on order.

</Solution>

</Challenges>
