---
title: 'Synchronizing with Effects(همگام سازی با افکت ها)'
---

<Intro>

Some components need to synchronize with external systems. For example, you might want to control a non-React component based on the React state, set up a server connection, or send an analytics log when a component appears on the screen. *Effects* let you run some code after rendering so that you can synchronize your component with some system outside of React.
//////////////////
بعضی از کامپوننت‌ها نیاز دارند با سیستم‌های خارجی همگام شوند. به عنوان مثال، شما ممکن است بخواهید یک کامپوننت غیر-React را بر اساس state React کنترل کنید، ارتباط با یک سرور را برقرار کنید یا زمانی که یک کامپوننت روی صفحه نمایش ظاهر می‌شود، یک لاگ analytics ارسال کنید. *Effects* به شما اجازه می‌دهند کدی را پس از رندرینگ اجرا کنید تا بتوانید کامپوننت خود را با یک سیستم خارج از React همگام کنید.

</Intro>

<YouWillLearn>

- What Effects are
- How Effects are different from events
- How to declare an Effect in your component
- How to skip re-running an Effect unnecessarily
- Why Effects run twice in development and how to fix them

/////////////
- Effects چیست؟
- تفاوت Effects با events چیست؟
- چگونه یک Effect را در کامپوننت خود تعریف کنیم؟
- چگونه از اجرای مجدد یک Effect بدون دلیل اصلی آن صرف‌نظر کنیم؟
- چرا Effects در حالت توسعه دو بار اجرا می‌شوند و چگونه آن‌ها را برطرف کنیم؟

</YouWillLearn>

## Effects چیست؟ و چگونه با events تفاوت دارند؟ {/*what-are-effects-and-how-are-they-different-from-events*/}

Before getting to Effects, you need to be familiar with two types of logic inside React components:

- **Rendering code** (introduced in [Describing the UI](/learn/describing-the-ui)) lives at the top level of your component. This is where you take the props and state, transform them, and return the JSX you want to see on the screen. [Rendering code must be pure.](/learn/keeping-components-pure) Like a math formula, it should only _calculate_ the result, but not do anything else.

- **Event handlers** (introduced in [Adding Interactivity](/learn/adding-interactivity)) are nested functions inside your components that *do* things rather than just calculate them. An event handler might update an input field, submit an HTTP POST request to buy a product, or navigate the user to another screen. Event handlers contain ["side effects"](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) (they change the program's state) caused by a specific user action (for example, a button click or typing).

Sometimes this isn't enough. Consider a `ChatRoom` component that must connect to the chat server whenever it's visible on the screen. Connecting to a server is not a pure calculation (it's a side effect) so it can't happen during rendering. However, there is no single particular event like a click that causes `ChatRoom` to be displayed.

***Effects* let you specify side effects that are caused by rendering itself, rather than by a particular event.** Sending a message in the chat is an *event* because it is directly caused by the user clicking a specific button. However, setting up a server connection is an *Effect* because it should happen no matter which interaction caused the component to appear. Effects run at the end of a [commit](/learn/render-and-commit) after the screen updates. This is a good time to synchronize the React components with some external system (like network or a third-party library).
////////////////////////

قبل از ورود به Effects، باید با دو نوع منطق درون کامپوننت‌های React آشنا شوید:

- **کد رندرینگ** (معرفی شده در [Describing the UI](/learn/describing-the-ui)) در سطح بالای کامپوننت شما قرار دارد. اینجاست که شما props و state را می‌گیرید، آن‌ها را تبدیل می‌کنید و JSX را که می‌خواهید روی صفحه نمایش ببینید، برمی‌گردانید. [کد رندرینگ باید خالص باشد.](/learn/keeping-components-pure) مانند یک فرمول ریاضی، فقط باید نتیجه را _محاسبه_ کند، اما هیچ کار دیگری انجام ندهد.

- **Event handlers** (معرفی شده در [Adding Interactivity](/learn/adding-interactivity)) توابع تو در تو درون کامپوننت‌های شما هستند که *کارهایی را انجام می‌دهند* به جای اینکه فقط آن‌ها را محاسبه کنند. یک event handler ممکن است یک فیلد ورودی را به‌روزرسانی کند، یک درخواست HTTP POST برای خرید یک محصول ارسال کند یا کاربر را به صفحه دیگری هدایت کند. Event handlers شامل ["side effects"](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) (آن‌ها وضعیت برنامه را تغییر می‌دهند) که توسط یک عمل خاص کاربر (به عنوان مثال، کلیک روی یک دکمه یا تایپ) ایجاد می‌شوند.

گاهی این کافی نیست. به عنوان مثال، یک کامپوننت `ChatRoom` باید هر زمان که روی صفحه نمایش قابل مشاهده است، به سرور چت متصل شود. اتصال به سرور محاسبه خالص نیست (یک side effect است) بنابراین نمی‌تواند در طول رندرینگ انجام شود. با این حال، هیچ رویداد خاصی مانند کلیک وجود ندارد که باعث شود `ChatRoom` نمایش داده شود.

***Effects* به شما اجازه می‌دهند تا side effect هایی را که توسط خود رندرینگ ایجاد می‌شوند، مشخص کنید، نه توسط یک رویداد خاص.** ارسال پیام در چت یک *event* است زیرا مستقیماً توسط کاربر با کلیک روی یک دکمه خاص ایجاد می‌شود. با این حال، راه‌اندازی اتصال به سرور یک *Effect* است زیرا باید بدون در نظر گرفتن

<Note>

Here and later in this text, capitalized "Effect" refers to the React-specific definition above, i.e. a side effect caused by rendering. To refer to the broader programming concept, we'll say "side effect".
////////////////////
اینجا و در ادامهٔ متن، "Effect" با حروف بزرگ به تعریف خاص React اشاره دارد، به عبارتی side effectی که توسط رندرینگ ایجاد می‌شود. برای اشاره به مفهوم برنامه‌نویسی گسترده‌تر، "side effect" را خواهیم گفت.

</Note>


## شاید نیازی به Effect نداشته باشید {/*you-might-not-need-an-effect*/}

**Don't rush to add Effects to your components.** Keep in mind that Effects are typically used to "step out" of your React code and synchronize with some *external* system. This includes browser APIs, third-party widgets, network, and so on. If your Effect only adjusts some state based on other state, [you might not need an Effect.](/learn/you-might-not-need-an-effect)
//////////////////////
**به زودی به اضافه کردن Effects به کامپوننت‌های خود شتاب ندهید.** به خاطر داشته باشید که Effects معمولاً برای "خارج شدن" از کد React شما و همگام‌سازی با سیستم *خارجی* استفاده می‌شوند. این شامل API های مرورگر، ویجت های شخص ثالث، شبکه و غیره است. اگر Effect شما فقط بر اساس state دیگری state را تنظیم می‌کند، [شاید نیازی به Effect نداشته باشید.](/learn/you-might-not-need-an-effect)

## چگونه یک Effect بنویسیم {/*how-to-write-an-effect*/}

To write an Effect, follow these three steps:

1. **Declare an Effect.** By default, your Effect will run after every render.
2. **Specify the Effect dependencies.** Most Effects should only re-run *when needed* rather than after every render. For example, a fade-in animation should only trigger when a component appears. Connecting and disconnecting to a chat room should only happen when the component appears and disappears, or when the chat room changes. You will learn how to control this by specifying *dependencies.*
3. **Add cleanup if needed.** Some Effects need to specify how to stop, undo, or clean up whatever they were doing. For example, "connect" needs "disconnect", "subscribe" needs "unsubscribe", and "fetch" needs either "cancel" or "ignore". You will learn how to do this by returning a *cleanup function*.

Let's look at each of these steps in detail.
///////////////////
برای نوشتن یک Effect، این سه مرحله را دنبال کنید:

1. **یک Effect تعریف کنید.** به طور پیش‌فرض، Effect شما پس از هر رندر اجرا می‌شود.
2. **وابستگی‌های Effect را مشخص کنید.** بیشتر Effects باید فقط *زمانی که نیاز است* و نه پس از هر رندر، مجدداً اجرا شوند. به عنوان مثال، یک انیمیشن fade-in فقط باید زمانی رخ دهد که یک کامپوننت ظاهر شود. اتصال و قطع اتصال به یک اتاق چت فقط باید زمانی رخ دهد که کامپوننت ظاهر و ناپدید می‌شود یا زمانی که اتاق چت تغییر می‌کند. با مشخص کردن *وابستگی‌ها*، یاد خواهید گرفت که چگونه این کار را کنترل کنید.
3. **در صورت نیاز، cleanup را اضافه کنید.** برخی از Effects باید مشخص کنند که چگونه باید توقف، لغو یا پاک کردن هر چیزی که در حال انجام آن بود، انجام شود. به عنوان مثال، "connect" به "disconnect" نیاز دارد، "subscribe" به "unsubscribe" نیاز دارد و "fetch" به "cancel" یا "ignore" نیاز دارد. با بازگرداندن یک *cleanup function*، یاد خواهید گرفت که چگونه این کار را انجام دهید.

بیایید به هر یک از این مراحل به تفصیل نگاه کنیم.

### مرحله 1: تعریف یک Effect {/*step-1-declare-an-effect*/}

To declare an Effect in your component, import the [`useEffect` Hook](/reference/react/useEffect) from React:
//////////////
برای تعریف یک Effect در کامپوننت خود، [`useEffect` Hook](/reference/react/useEffect) را از React وارد کنید:

```js
import { useEffect } from 'react';
```

Then, call it at the top level of your component and put some code inside your Effect:
سپس، آن را در سطح بالای کامپوننت خود فراخوانی کرده و کدی را درون Effect خود قرار دهید:


```js {2-4}
function MyComponent() {
  useEffect(() => {
    // Code here will run after *every* render
  });
  return <div />;
}
```

Every time your component renders, React will update the screen *and then* run the code inside `useEffect`. In other words, **`useEffect` "delays" a piece of code from running until that render is reflected on the screen.**
/////////////
هر زمان که کامپوننت شما رندر می‌شود، React صفحه را به‌روزرسانی می‌کند و سپس کد درون `useEffect` را اجرا می‌کند. به عبارت دیگر، **`useEffect` یک قطعه کد را "تأخیر" می‌دهد تا زمانی که آن رندر بر روی صفحه نمایش نمایش داده شود، اجرا شود.**

Let's see how you can use an Effect to synchronize with an external system. Consider a `<VideoPlayer>` React component. It would be nice to control whether it's playing or paused by passing an `isPlaying` prop to it:
//////////////
بیایید ببینیم چگونه می‌توانید از یک Effect برای همگام‌سازی با یک سیستم خارجی استفاده کنید. یک کامپوننت React `<VideoPlayer>` را در نظر بگیرید. خوب است که با انتقال یک `isPlaying` prop به آن، بتوانید کنترل کنید که آیا در حال پخش است یا متوقف شده است:

```js
<VideoPlayer isPlaying={isPlaying} />;
```

Your custom `VideoPlayer` component renders the built-in browser [`<video>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video) tag:
/////////////////////
کامپوننت سفارشی `VideoPlayer` شما برچسب مرورگر داخلی [`<video>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video) را رندر می‌کند:

```js
function VideoPlayer({ src, isPlaying }) {
  // TODO: do something with isPlaying
  return <video src={src} />;
}
```

However, the browser `<video>` tag does not have an `isPlaying` prop. The only way to control it is to manually call the [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) and [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) methods on the DOM element. **You need to synchronize the value of `isPlaying` prop, which tells whether the video _should_ currently be playing, with calls like `play()` and `pause()`.**
///////////////
با این حال، برچسب مرورگر `<video>` دارای `isPlaying` prop نیست. تنها راه کنترل آن، فراخوانی دستی روش‌های [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) و [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) روی عنصر DOM است. **شما باید مقدار `isPlaying` prop را که مشخص می‌کند آیا ویدیو در حال حاضر باید پخش شود یا نه، با فراخوانی‌هایی مانند `play()` و `pause()` همگام کنید.**

We'll need to first [get a ref](/learn/manipulating-the-dom-with-refs) to the `<video>` DOM node.
ابتدا باید [یک ref](/learn/manipulating-the-dom-with-refs) به عنصر DOM `<video>` بگیریم.

You might be tempted to try to call `play()` or `pause()` during rendering, but that isn't correct:
شما ممکن است تلاش کنید که در طول رندرینگ `play()` یا `pause()` را فراخوانی کنید، اما این درست نیست:


<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  if (isPlaying) {
    ref.current.play();  // Calling these while rendering isn't allowed.
  } else {
    ref.current.pause(); // Also, this crashes.
  }

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  return (
    <>
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

The reason this code isn't correct is that it tries to do something with the DOM node during rendering. In React, [rendering should be a pure calculation](/learn/keeping-components-pure) of JSX and should not contain side effects like modifying the DOM.

Moreover, when `VideoPlayer` is called for the first time, its DOM does not exist yet! There isn't a DOM node yet to call `play()` or `pause()` on, because React doesn't know what DOM to create until you return the JSX.

The solution here is to **wrap the side effect with `useEffect` to move it out of the rendering calculation:**
//////////////////
دلیل اینکه این کد درست نیست، این است که سعی می‌کند در طول رندرینگ چیزی با عنصر DOM انجام دهد. در React، [رندرینگ باید یک محاسبه خالص](/learn/keeping-components-pure) از JSX باشد و نباید شامل side effect هایی مانند تغییر DOM باشد.

علاوه بر این، زمانی که `VideoPlayer` برای اولین بار فراخوانی می‌شود، DOM آن هنوز وجود ندارد! هنوز یک عنصر DOM برای فراخوانی `play()` یا `pause()` وجود ندارد، زیرا React نمی‌داند کدام DOM را باید ایجاد کند تا شما JSX را برگردانید.

راه‌حل در اینجا این است که **side effect را با `useEffect` بپیچید تا آن را از محاسبه رندرینگ خارج کنید:**

```js {6,12}
import { useEffect, useRef } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}
```

By wrapping the DOM update in an Effect, you let React update the screen first. Then your Effect runs.

When your `VideoPlayer` component renders (either the first time or if it re-renders), a few things will happen. First, React will update the screen, ensuring the `<video>` tag is in the DOM with the right props. Then React will run your Effect. Finally, your Effect will call `play()` or `pause()` depending on the value of `isPlaying`.

Press Play/Pause multiple times and see how the video player stays synchronized to the `isPlaying` value:
///////////////////
با پیچیدن به‌روزرسانی DOM در یک Effect، به React اجازه می‌دهید ابتدا صفحه را به‌روزرسانی کند. سپس Effect شما اجرا می‌شود.

زمانی که کامپوننت `VideoPlayer` شما رندر می‌شود (به طور اولیه یا اگر دوباره رندر شود)، چندین چیز رخ می‌دهد. ابتدا، React صفحه را به‌روزرسانی می‌کند و اطمینان حاصل می‌کند که برچسب `<video>` با props مناسب در DOM قرار دارد. سپس React Effect شما را اجرا می‌کند. در نهایت، Effect شما با توجه به مقدار `isPlaying`، `play()` یا `pause()` را فراخوانی خواهد کرد.

چندین بار Play/Pause را فشار دهید و ببینید که چگونه پخش‌کننده ویدیو با مقدار `isPlaying` همگام می‌ماند:

<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  return (
    <>
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

In this example, the "external system" you synchronized to React state was the browser media API. You can use a similar approach to wrap legacy non-React code (like jQuery plugins) into declarative React components.

Note that controlling a video player is much more complex in practice. Calling `play()` may fail, the user might play or pause using the built-in browser controls, and so on. This example is very simplified and incomplete.
/////////////////////
در این مثال، "سیستم خارجی" که شما به state React همگام‌سازی کردید، API رسانه‌ای مرورگر بود. می‌توانید از یک رویکرد مشابه برای پیچیدن کد‌های غیر-React (مانند پلاگین‌های jQuery) به کامپوننت‌های React اعلامی استفاده کنید.

توجه داشته باشید که کنترل کردن یک پخش‌کننده ویدیو در عمل بسیار پیچیده‌تر است. فراخوانی `play()` ممکن است شکست بخورد، کاربر ممکن است با استفاده از کنترل‌های مرورگر داخلی پخش یا متوقف کند و غیره. این مثال بسیار ساده و ناقص است.

<Pitfall>

By default, Effects run after *every* render. This is why code like this will **produce an infinite loop:**
////////////////////////
به طور پیش‌فرض، Effects پس از *هر* رندر اجرا می‌شوند. به همین دلیل کدی مانند این، **یک حلقه بی‌نهایت تولید خواهد کرد:**

```js
const [count, setCount] = useState(0);
useEffect(() => {
  setCount(count + 1);
});
```

Effects run as a *result* of rendering. Setting state *triggers* rendering. Setting state immediately in an Effect is like plugging a power outlet into itself. The Effect runs, it sets the state, which causes a re-render, which causes the Effect to run, it sets the state again, this causes another re-render, and so on.

Effects should usually synchronize your components with an *external* system. If there's no external system and you only want to adjust some state based on other state, [you might not need an Effect.](/learn/you-might-not-need-an-effect)
//////////////////
Effects به عنوان *نتیجه* رندرینگ اجرا می‌شوند. تنظیم state، رندرینگ را *تریگر* می‌کند. تنظیم state در یک Effect به مانند وصل کردن پریز برق به خودش است. Effect اجرا می‌شود، state را تنظیم می‌کند، که باعث می‌شود دوباره رندرینگ شود، که باعث می‌شود Effect اجرا شود، state را دوباره تنظیم می‌کند، این باعث می‌شود دوباره رندرینگ شود و غیره.

Effects معمولاً کامپوننت‌های شما را با یک سیستم *خارجی* همگام می‌کنند. اگر هیچ سیستم خارجی وجود ندارد و فقط می‌خواهید برخی state را بر اساس state دیگر تنظیم کنید، [شاید نیازی به Effect نداشته باشید.](/learn/you-might-not-need-an-effect)

</Pitfall>

### مرحله 2: وابستگی‌های Effect را مشخص کنید {/*step-2-specify-the-effect-dependencies*/}

By default, Effects run after *every* render. Often, this is **not what you want:**

- Sometimes, it's slow. Synchronizing with an external system is not always instant, so you might want to skip doing it unless it's necessary. For example, you don't want to reconnect to the chat server on every keystroke.
- Sometimes, it's wrong. For example, you don't want to trigger a component fade-in animation on every keystroke. The animation should only play once when the component appears for the first time.

To demonstrate the issue, here is the previous example with a few `console.log` calls and a text input that updates the parent component's state. Notice how typing causes the Effect to re-run:
///////////////////
به طور پیش‌فرض، Effects پس از *هر* رندر اجرا می‌شوند. اغلب، این **آنچه شما می‌خواهید نیست:**

- گاهی این کار کند است. همگام‌سازی با یک سیستم خارجی همیشه فوری نیست، بنابراین ممکن است بخواهید آن را بدون اینکه لزومی وجود داشته باشد، انجام ندهید. به عنوان مثال، شما نمی‌خواهید در هر ضربه کلید، به سرور چت مجدداً متصل شوید.
- گاهی این اشتباه است. به عنوان مثال، شما نمی‌خواهید هر ضربه کلید، یک انیمیشن fade-in را فعال کنید. این انیمیشن باید فقط یک بار در زمان ظاهر شدن کامپوننت پخش شود.

برای نشان دادن مشکل، اینجا مثال قبلی با چند `console.log` و یک ورودی متنی که state کامپوننت والد را به‌روزرسانی می‌کند، آمده است. توجه کنید که تایپ کردن باعث می‌شود Effect مجدداً اجرا شود:
<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      console.log('Calling video.play()');
      ref.current.play();
    } else {
      console.log('Calling video.pause()');
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
input, button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

You can tell React to **skip unnecessarily re-running the Effect** by specifying an array of *dependencies* as the second argument to the `useEffect` call. Start by adding an empty `[]` array to the above example on line 14:
//////////
شما می‌توانید به React بگویید که با مشخص کردن یک آرایه از *وابستگی‌ها* به عنوان دومین آرگومان فراخوانی `useEffect`، **از اجرای مجدد Effect بدون دلیل اصلی صرف‌نظر کند**. با اضافه کردن یک آرایه خالی `[]` به مثال بالا در خط 14، شروع کنید:.

```js {3}
  useEffect(() => {
    // ...
  }, []);
```

You should see an error saying `React Hook useEffect has a missing dependency: 'isPlaying'`:
شما باید یک خطایی با پیام `React Hook useEffect has a missing dependency: 'isPlaying'` ببینید:

<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      console.log('Calling video.play()');
      ref.current.play();
    } else {
      console.log('Calling video.pause()');
      ref.current.pause();
    }
  }, []); // This causes an error

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
input, button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

The problem is that the code inside of your Effect *depends on* the `isPlaying` prop to decide what to do, but this dependency was not explicitly declared. To fix this issue, add `isPlaying` to the dependency array:
///////////////////
مشکل این است که کد درون Effect شما برای تصمیم‌گیری در مورد چه کاری انجام دهد، *وابسته به* prop `isPlaying` است، اما این وابستگی به صورت صریح اعلام نشده است. برای رفع این مشکل، `isPlaying` را به آرایه وابستگی اضافه کنید:

```js {2,7}
  useEffect(() => {
    if (isPlaying) { // It's used here...
      // ...
    } else {
      // ...
    }
  }, [isPlaying]); // ...so it must be declared here!
```

Now all dependencies are declared, so there is no error. Specifying `[isPlaying]` as the dependency array tells React that it should skip re-running your Effect if `isPlaying` is the same as it was during the previous render. With this change, typing into the input doesn't cause the Effect to re-run, but pressing Play/Pause does:
////////////////////
همه‌ی وابستگی‌ها اعلام شده‌اند، بنابراین هیچ خطایی وجود ندارد. مشخص کردن `[isPlaying]` به عنوان آرایه‌ی وابستگی به React می‌گوید که اگر `isPlaying` در رندر قبلی با همان مقداری که در رندر فعلی دارد، یکسان باشد، باید اجرای Effect را نادیده بگیرد. با این تغییر، تایپ در ورودی باعث اجرای مجدد Effect نمی‌شود، اما فشردن دکمه‌ی پخش/توقف باعث اجرای مجدد آن می‌شود.

<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      console.log('Calling video.play()');
      ref.current.play();
    } else {
      console.log('Calling video.pause()');
      ref.current.pause();
    }
  }, [isPlaying]);

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
input, button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

The dependency array can contain multiple dependencies. React will only skip re-running the Effect if *all* of the dependencies you specify have exactly the same values as they had during the previous render. React compares the dependency values using the [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison. See the [`useEffect` reference](/reference/react/useEffect#reference) for details.

**Notice that you can't "choose" your dependencies.** You will get a lint error if the dependencies you specified don't match what React expects based on the code inside your Effect. This helps catch many bugs in your code. If you don't want some code to re-run, [*edit the Effect code itself* to not "need" that dependency.](/learn/lifecycle-of-reactive-effects#what-to-do-when-you-dont-want-to-re-synchronize)
////////////////
آرایه‌ی وابستگی می‌تواند شامل چندین وابستگی باشد. React فقط در صورتی Effect را اجرا نمی‌کند که *همه* وابستگی‌هایی که شما مشخص کرده‌اید دقیقاً با مقادیری که در رندر قبلی داشته‌اند، یکسان باشند. React مقادیر وابستگی را با استفاده از مقایسه‌ی [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) مقایسه می‌کند. برای جزئیات بیشتر به [مرجع `useEffect`](/reference/react/useEffect#reference) مراجعه کنید.

**توجه کنید که نمی‌توانید وابستگی‌های خود را "انتخاب" کنید.** اگر وابستگی‌هایی که مشخص کرده‌اید با کد داخل Effect شما مطابقت نداشته باشند، خطای لینت خواهید گرفت. این کمک می‌کند تا بسیاری از باگ‌های کد شما را پیدا کنید. اگر نمی‌خواهید کدی اجرا شود، [*کد Effect را خودتان ویرایش کنید* تا به آن وابستگی "نیاز" نباشد.](/learn/lifecycle-of-reactive-effects#what-to-do-when-you-dont-want-to-re-synchronize)

<Pitfall>

The behaviors without the dependency array and with an *empty* `[]` dependency array are different:
بدون آرایه‌ی وابستگی و با یک آرایه‌ی وابستگی خالی، رفتارها متفاوت هستند.

```js {3,7,11}
useEffect(() => {
  // This runs after every render
});

useEffect(() => {
  // This runs only on mount (when the component appears)
}, []);

useEffect(() => {
  // This runs on mount *and also* if either a or b have changed since the last render
}, [a, b]);
```

We'll take a close look at what "mount" means in the next step.
در مرحله‌ی بعد، به معنای "mount" به دقت نگاه خواهیم کرد.

</Pitfall>

<DeepDive>

#### Why was the ref omitted from the dependency array?(ترجمه کن: #### چرا ref از آرایه‌ی وابستگی حذف شده است؟ ) {/*why-was-the-ref-omitted-from-the-dependency-array*/}

This Effect uses _both_ `ref` and `isPlaying`, but only `isPlaying` is declared as a dependency:
این Effect از هر دو `ref` و `isPlaying` استفاده می‌کند، اما فقط `isPlaying` به عنوان وابستگی اعلام شده است:

```js {9}
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying]);
```

This is because the `ref` object has a *stable identity:* React guarantees [you'll always get the same object](/reference/react/useRef#returns) from the same `useRef` call on every render. It never changes, so it will never by itself cause the Effect to re-run. Therefore, it does not matter whether you include it or not. Including it is fine too:
///////////////////
این به این دلیل است که شی `ref` یک *هویت پایدار* دارد: React تضمین می‌کند که [شما همیشه همان شی](/reference/react/useRef#returns) را از همان `useRef` در هر رندر دریافت خواهید کرد. هرگز تغییر نمی‌کند، بنابراین به تنهایی باعث اجرای مجدد Effect نمی‌شود. بنابراین، مهم نیست که آیا آن را شامل می‌کنید یا خیر. شامل کردن آن هم خوب است:

```js {9}
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying, ref]);
```

The [`set` functions](/reference/react/useState#setstate) returned by `useState` also have stable identity, so you will often see them omitted from the dependencies too. If the linter lets you omit a dependency without errors, it is safe to do.

Omitting always-stable dependencies only works when the linter can "see" that the object is stable. For example, if `ref` was passed from a parent component, you would have to specify it in the dependency array. However, this is good because you can't know whether the parent component always passes the same ref, or passes one of several refs conditionally. So your Effect _would_ depend on which ref is passed.
/////////////////
[`set` functions](/reference/react/useState#setstate) که توسط `useState` برگشت داده می‌شوند، همچنین هویت پایداری دارند، بنابراین اغلب آن‌ها را از وابستگی‌ها حذف می‌کنید. اگر لینتر به شما اجازه داد که یک وابستگی را بدون خطا حذف کنید، این کار ایمن است.

حذف وابستگی‌های همیشه پایدار فقط زمانی کار می‌کند که لینتر می‌تواند ببیند که شی پایدار است. به عنوان مثال، اگر `ref` از یک کامپوننت والد منتقل شده بود، شما باید آن را در آرایه‌ی وابستگی مشخص کنید. با این حال، این خوب است زیرا نمی‌توانید بدانید که کامپوننت والد همیشه همان `ref` را انتقال می‌دهد یا یکی از چندین `ref` را به صورت شرطی انتقال می‌دهد. بنابراین، Effect شما به `ref` منتقل شده وابسته است.

</DeepDive>

### Step 3: Add cleanup if needed(مرحله‌ی ۳: اضافه کردن تمیز کردن در صورت نیاز ) {/*step-3-add-cleanup-if-needed*/}

Consider a different example. You're writing a `ChatRoom` component that needs to connect to the chat server when it appears. You are given a `createConnection()` API that returns an object with `connect()` and `disconnect()` methods. How do you keep the component connected while it is displayed to the user?
/////////////////
مثال دیگری را در نظر بگیرید. شما در حال نوشتن یک کامپوننت `ChatRoom` هستید که نیاز به اتصال به سرور چت دارد هنگامی که ظاهر می‌شود. شما یک API به نام `createConnection()` دارید که یک شی با متد‌های `connect()` و `disconnect()` برمی‌گرداند. چگونه می‌توانید کامپوننت را در حالت اتصال نگه دارید در حالی که به کاربر نمایش داده می‌شود؟


Start by writing the Effect logic:
ابتدا منطق Effect را بنویسید:

```js
useEffect(() => {
  const connection = createConnection();
  connection.connect();
});
```

It would be slow to connect to the chat after every re-render, so you add the dependency array:
اتصال به چت پس از هر بار رندر شدن کند خواهد بود، بنابراین آرایه‌ی وابستگی را اضافه کنید:


```js {4}
useEffect(() => {
  const connection = createConnection();
  connection.connect();
}, []);
```

**The code inside the Effect does not use any props or state, so your dependency array is `[]` (empty). This tells React to only run this code when the component "mounts", i.e. appears on the screen for the first time.**
/////////////////
**کد داخل Effect از هیچ props یا state استفاده نمی‌کند، بنابراین آرایه‌ی وابستگی شما `[]` (خالی) است. این به React می‌گوید که فقط این کد را هنگامی که کامپوننت "mount" می‌شود، یعنی برای اولین بار در صفحه نمایش داده می‌شود، اجرا کند.**



Let's try running this code:
بیایید این کد را اجرا کنیم:

<Sandpack>

```js
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

```js chat.js
export function createConnection() {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting...');
    },
    disconnect() {
      console.log('❌ Disconnected.');
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
```

</Sandpack>

This Effect only runs on mount, so you might expect `"✅ Connecting..."` to be printed once in the console. **However, if you check the console, `"✅ Connecting..."` gets printed twice. Why does it happen?**

Imagine the `ChatRoom` component is a part of a larger app with many different screens. The user starts their journey on the `ChatRoom` page. The component mounts and calls `connection.connect()`. Then imagine the user navigates to another screen--for example, to the Settings page. The `ChatRoom` component unmounts. Finally, the user clicks Back and `ChatRoom` mounts again. This would set up a second connection--but the first connection was never destroyed! As the user navigates across the app, the connections would keep piling up.

Bugs like this are easy to miss without extensive manual testing. To help you spot them quickly, in development React remounts every component once immediately after its initial mount.

Seeing the `"✅ Connecting..."` log twice helps you notice the real issue: your code doesn't close the connection when the component unmounts.

To fix the issue, return a *cleanup function* from your Effect:
///
این Effect فقط در زمان mount اجرا می‌شود، بنابراین شما ممکن است انتظار داشته باشید که `"✅ Connecting..."` یک بار در کنسول چاپ شود. **با این حال، اگر کنسول را بررسی کنید، `"✅ Connecting..."` دو بار چاپ می‌شود. چرا؟**

تصور کنید که کامپوننت `ChatRoom` بخشی از یک برنامه بزرگ با صفحات مختلف است. کاربر سفر خود را در صفحه `ChatRoom` شروع می‌کند. کامپوننت mount می‌شود و `connection.connect()` را فراخوانی می‌کند. سپس تصور کنید که کاربر به صفحه دیگری منتقل می‌شود - به عنوان مثال، به صفحه تنظیمات. کامپوننت `ChatRoom` unmount می‌شود. در نهایت، کاربر برمی‌گردد و `ChatRoom` دوباره mount می‌شود. این یک اتصال دوم را تنظیم می‌کند - اما اتصال اول هرگز نابود نشده است! همانطور که کاربر در برنامه حرکت می‌کند، اتصالات به صورت پشت سر هم اضافه می‌شوند.

باگ‌هایی مانند این بدون آزمون دستی جامع، سخت است که تشخیص داده شوند. برای کمک به شما در شناسایی سریع آن‌ها، React در حالت توسعه، هر کامپوننت را یک بار بلافاصله پس از mount اولیه آن remount می‌کند.

دیدن لاگ `"✅ Connecting..."` دو بار به شما کمک می‌کند تا به مشکل واقعی توجه کنید: کد شما هنگام unmount کردن کامپوننت، اتصال را بسته نمی‌کند.

برای رفع مشکل، یک *تابع تمیز کننده* را از Effect خود بازگردانید:


```js {4-6}
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []);
```

React will call your cleanup function each time before the Effect runs again, and one final time when the component unmounts (gets removed). Let's see what happens when the cleanup function is implemented:
/////////////
React هر بار قبل از اجرای مجدد Effect، تابع تمیز کننده شما را فراخوانی می‌کند و یک بار نهایی هنگامی که کامپوننت unmount می‌شود (حذف می‌شود). بیایید ببینیم چه اتفاقی می‌افتد وقتی تابع تمیز کننده پیاده‌سازی می‌شود:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

```js chat.js
export function createConnection() {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting...');
    },
    disconnect() {
      console.log('❌ Disconnected.');
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
```

</Sandpack>

Now you get three console logs in development:
اکنون سه لاگ کنسول در حال توسعه دارید:

1. `"✅ Connecting..."`
2. `"❌ Disconnected."`
3. `"✅ Connecting..."`

**This is the correct behavior in development.** By remounting your component, React verifies that navigating away and back would not break your code. Disconnecting and then connecting again is exactly what should happen! When you implement the cleanup well, there should be no user-visible difference between running the Effect once vs running it, cleaning it up, and running it again. There's an extra connect/disconnect call pair because React is probing your code for bugs in development. This is normal--don't try to make it go away!

**In production, you would only see `"✅ Connecting..."` printed once.** Remounting components only happens in development to help you find Effects that need cleanup. You can turn off [Strict Mode](/reference/react/StrictMode) to opt out of the development behavior, but we recommend keeping it on. This lets you find many bugs like the one above.
/////////////////
 این رفتار در حالت توسعه درست است. با remount کردن کامپوننت خود، React بررسی می‌کند که ناوبری به دور و بر نمی‌تواند کد شما را خراب کند. قطع و وصل کردن دوباره دقیقاً آنچه باید اتفاق بیفتد! هنگامی که تمیز کردن را خوب پیاده‌سازی می‌کنید، بین اجرای Effect یک بار و اجرای آن، تمیز کردن آن و اجرای آن دوباره، تفاوتی قابل مشاهده برای کاربر وجود نخواهد داشت. یک جفت تماس connect/disconnect اضافی وجود دارد زیرا React در حال تست کد شما برای باگ‌ها در حالت توسعه است. این طبیعی است - سعی نکنید آن را برطرف کنید!

**در محصول، فقط یک بار `"✅ Connecting..."` چاپ می‌شود.** Remounting کامپوننت‌ها فقط در حالت توسعه اتفاق می‌افتد تا به شما کمک کند Effects نیاز به تمیز کردن داشته باشند. شما می‌توانید [Strict Mode](/reference/react/StrictMode) را خاموش کنید تا از رفتار توسعه خارج شوید، اما ما پیشنهاد می‌کنیم آن را روشن نگه دارید. این به شما کمک می‌کند تا باگ‌هایی مانند باگ بالا را پیدا کنید.

## چگونه می‌توان Effect را در حالت توسعه دوباره اجرا شده را کنترل کرد؟ {/*how-to-handle-the-effect-firing-twice-in-development*/}

React intentionally remounts your components in development to find bugs like in the last example. **The right question isn't "how to run an Effect once", but "how to fix my Effect so that it works after remounting".**
///////////////////////
React در حالت توسعه، کامپوننت‌های شما را بازمانده می‌کند تا باگ‌هایی مانند مثال قبل را پیدا کند. 
**سوال درست نیست "چگونه یک Effect را یک بار اجرا کنیم"، بلکه "چگونه Effect خود را به گونه‌ای تصحیح کنیم که پس از بازماندن دوباره کار کند".**

Usually, the answer is to implement the cleanup function.  The cleanup function should stop or undo whatever the Effect was doing. The rule of thumb is that the user shouldn't be able to distinguish between the Effect running once (as in production) and a _setup → cleanup → setup_ sequence (as you'd see in development).
//////////////
معمولاً، پاسخ پیاده‌سازی تابع تمیز کننده است. تابع تمیز کننده باید هر آنچه Effect در حال انجام آن بود را متوقف یا باطل کند. قاعده عمومی این است که کاربر نباید بتواند بین اجرای یک Effect (مانند در محصول) و یک دنباله _setup → cleanup → setup_ (مانند در حالت توسعه) تمایز قائل شود.

Most of the Effects you'll write will fit into one of the common patterns below.
بیشتر Effectsی که می‌نویسید، در یکی از الگوهای معمول زیر جا می‌گیرند.

### کنترل کردن ویجت‌های غیر-React {/*controlling-non-react-widgets*/}

Sometimes you need to add UI widgets that aren't written to React. For example, let's say you're adding a map component to your page. It has a `setZoomLevel()` method, and you'd like to keep the zoom level in sync with a `zoomLevel` state variable in your React code. Your Effect would look similar to this:
////////////////////////
گاهی اوقات نیاز دارید ویجت‌های UI را اضافه کنید که به React نوشته نشده‌اند. به عنوان مثال، فرض کنید که یک کامپوننت نقشه را به صفحه خود اضافه می‌کنید. این یک متد `setZoomLevel()` دارد و شما می‌خواهید سطح بزرگنمایی را با یک متغیر state به نام `zoomLevel` در کد React خود هماهنگ کنید. Effect شما شبیه به این خواهد بود

```js
useEffect(() => {
  const map = mapRef.current;
  map.setZoomLevel(zoomLevel);
}, [zoomLevel]);
```

Note that there is no cleanup needed in this case. In development, React will call the Effect twice, but this is not a problem because calling `setZoomLevel` twice with the same value does not do anything. It may be slightly slower, but this doesn't matter because it won't remount needlessly in production.
///////////////////////////
توجه کنید که در این مورد نیازی به تمیز کردن وجود ندارد. در حالت توسعه، React Effect را دو بار فراخوانی می‌کند، اما این یک مشکل نیست زیرا فراخوانی `setZoomLevel` دو بار با همان مقدار، هیچ کاری انجام نمی‌دهد. این ممکن است کمی کندتر باشد، اما این مهم نیست زیرا در محصول به طور ناپدیدی remount نمی‌شود.

Some APIs may not allow you to call them twice in a row. For example, the [`showModal`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement/showModal) method of the built-in [`<dialog>`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement) element throws if you call it twice. Implement the cleanup function and make it close the dialog:
///////////////////
برخی از APIها ممکن است به شما اجازه ندهند که آن‌ها را دو بار پشت سر هم فراخوانی کنید. به عنوان مثال، متد [`showModal`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement/showModal) از المان `<dialog>` داخلی [built-in](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement)، اگر آن را دو بار فراخوانی کنید، خطا می‌دهد. تابع تمیز کننده را پیاده‌سازی کنید و آن را بسته کنید:.

```js {4}
useEffect(() => {
  const dialog = dialogRef.current;
  dialog.showModal();
  return () => dialog.close();
}, []);
```

In development, your Effect will call `showModal()`, then immediately `close()`, and then `showModal()` again. This has the same user-visible behavior as calling `showModal()` once, as you would see in production.
///////////
در حالت توسعه، Effect شما `showModal()` را فراخوانی می‌کند، سپس به طور فوری `close()` را فراخوانی می‌کند و سپس دوباره `showModal()` را فراخوانی می‌کند. این رفتاری است که کاربر می‌تواند آن را مشاهده کند و همان رفتاری است که در محصول مشاهده می‌کنید که `showModal()` یک بار فراخوانی می‌شود.

### Subscribing to events {/*subscribing-to-events*/}

If your Effect subscribes to something, the cleanup function should unsubscribe:
اگر افکت شما مشترک چیزی باشد، تابع پاکسازی باید اشتراک را لغو کند:

```js {6}
useEffect(() => {
  function handleScroll(e) {
    console.log(window.scrollX, window.scrollY);
  }
  window.addEventListener('scroll', handleScroll);
  return () => window.removeEventListener('scroll', handleScroll);
}, []);
```

In development, your Effect will call `addEventListener()`, then immediately `removeEventListener()`, and then `addEventListener()` again with the same handler. So there would be only one active subscription at a time. This has the same user-visible behavior as calling `addEventListener()` once, as in production.
////////
در حالت توسعه، Effect شما `addEventListener()` را فراخوانی می‌کند، سپس به طور فوری `removeEventListener()` را فراخوانی می‌کند و سپس دوباره `addEventListener()` را با همان handler فراخوانی می‌کند. بنابراین، همیشه یک اشتراک فعال وجود دارد. این رفتاری است که کاربر می‌تواند آن را مشاهده کند و همان رفتاری است که در محصول مشاهده می‌کنید که `addEventListener()` یک بار فراخوانی می‌شود.


### Triggering animations {/*triggering-animations*/}

If your Effect animates something in, the cleanup function should reset the animation to the initial values:
/////////////////////
اگر Effect شما چیزی را انیمیت می‌کند، تابع تمیز کننده باید انیمیشن را به مقادیر اولیه بازنشانی کند:


```js {4-6}
useEffect(() => {
  const node = ref.current;
  node.style.opacity = 1; // Trigger the animation
  return () => {
    node.style.opacity = 0; // Reset to the initial value
  };
}, []);
```

In development, opacity will be set to `1`, then to `0`, and then to `1` again. This should have the same user-visible behavior as setting it to `1` directly, which is what would happen in production. If you use a third-party animation library with support for tweening, your cleanup function should reset the timeline to its initial state.
/////////////
در حالت توسعه، opacity به `1` تنظیم می‌شود، سپس به `0` و سپس دوباره به `1`. این باید رفتاری را که در محصول به `1` تنظیم می‌شود، داشته باشد که در محصول اتفاق می‌افتد. اگر از کتابخانه‌ی انیمیشن شخص ثالث با پشتیبانی از tweening استفاده می‌کنید، تابع تمیز کننده شما باید timeline را به حالت اولیه بازنشانی کند.

### Fetching data {/*fetching-data*/}

If your Effect fetches something, the cleanup function should either [abort the fetch](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) or ignore its result:
//////////////
اگر افکت شما چیزی را واکشی کند، تابع پاکسازی باید یا [واکشی را لغو کند] (https://developer.mozilla.org/en-US/docs/Web/API/AbortController) یا نتیجه آن را نادیده بگیرد:

```js {2,6,13-15}
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```

You can't "undo" a network request that already happened, but your cleanup function should ensure that the fetch that's _not relevant anymore_ does not keep affecting your application. If the `userId` changes from `'Alice'` to `'Bob'`, cleanup ensures that the `'Alice'` response is ignored even if it arrives after `'Bob'`.
////////////////
شما نمی‌توانید یک درخواست شبکه‌ای که قبلاً انجام شده است را "باطل" کنید، اما تابع پاک‌سازی شما باید اطمینان حاصل کند که درخواستی که دیگر "مربوط نیست" دیگر بر روی برنامه شما تأثیر نگذارد. اگر `userId` از `'Alice'` به `'Bob'` تغییر کند، پاک‌سازی مطمئن می‌شود که پاسخ `'Alice'` حتی اگر بعد از `'Bob'` دریافت شود، نادیده گرفته می‌شود.

**In development, you will see two fetches in the Network tab.** There is nothing wrong with that. With the approach above, the first Effect will immediately get cleaned up so its copy of the `ignore` variable will be set to `true`. So even though there is an extra request, it won't affect the state thanks to the `if (!ignore)` check.
//////////////
در حال توسعه، شما دو درخواست را در بخش شبکه مشاهده خواهید کرد. هیچ مشکلی با آن وجود ندارد. با رویکرد فوق، اولین اثر به طور فوری پاک می‌شود، بنابراین کپی متغیر `ignore` آن به `true` تنظیم می‌شود. بنابراین، حتی اگر یک درخواست اضافی وجود داشته باشد، به دلیل بررسی `if (!ignore)`، وضعیت را تحت تأثیر قرار نخواهد داد.

**In production, there will only be one request.** If the second request in development is bothering you, the best approach is to use a solution that deduplicates requests and caches their responses between components:
/////////////
در محیط تولید، تنها یک درخواست وجود خواهد داشت. اگر درخواست دوم در محیط توسعه شما را آزار می‌دهد، بهترین رویکرد استفاده از یک راه‌حل است که درخواست‌ها را تکرار نمی‌کند و پاسخ‌های آن‌ها را بین اجزای مختلف کش می‌کند.

```js
function TodoList() {
  const todos = useSomeDataLibrary(`/api/user/${userId}/todos`);
  // ...
```

This will not only improve the development experience, but also make your application feel faster. For example, the user pressing the Back button won't have to wait for some data to load again because it will be cached. You can either build such a cache yourself or use one of the many alternatives to manual fetching in Effects.
///////////////
این کار نه تنها تجربه‌ی توسعه را بهبود می‌بخشد، بلکه برنامه‌ی شما را سریع‌تر نیز می‌کند. به عنوان مثال، کاربری که دکمه‌ی بازگشت را فشار می‌دهد، نیازی به انتظار برای بارگذاری دوباره‌ی برخی از داده‌ها ندارد زیرا آن‌ها کش شده‌اند. شما می‌توانید یک کش مانند این را خودتان بسازید یا از یکی از جایگزین‌های موجود برای دریافت دستی در اثرها استفاده کنید.

<DeepDive>

#### What are good alternatives to data fetching in Effects? {/*what-are-good-alternatives-to-data-fetching-in-effects*/}

Writing `fetch` calls inside Effects is a [popular way to fetch data](https://www.robinwieruch.de/react-hooks-fetch-data/), especially in fully client-side apps. This is, however, a very manual approach and it has significant downsides:
//////////////
نوشتن تماس‌های `fetch` درون اثرها یک [روش محبوب برای دریافت داده‌ها](https://www.robinwieruch.de/react-hooks-fetch-data/) است، به خصوص در برنامه‌های کاملاً سمت مشتری. با این حال، این رویکرد بسیار دستی است و دارای معایب قابل توجهی است:

- **Effects don't run on the server.** This means that the initial server-rendered HTML will only include a loading state with no data. The client computer will have to download all JavaScript and render your app only to discover that now it needs to load the data. This is not very efficient.
- **Fetching directly in Effects makes it easy to create "network waterfalls".** You render the parent component, it fetches some data, renders the child components, and then they start fetching their data. If the network is not very fast, this is significantly slower than fetching all data in parallel.
- **Fetching directly in Effects usually means you don't preload or cache data.** For example, if the component unmounts and then mounts again, it would have to fetch the data again.
- **It's not very ergonomic.** There's quite a bit of boilerplate code involved when writing `fetch` calls in a way that doesn't suffer from bugs like [race conditions.](https://maxrozen.com/race-conditions-fetching-data-react-with-useeffect)
//////////////////
- **اثرها در سرور اجرا نمی‌شوند.** این بدان معنی است که HTML اولیه‌ی سرور-رندر شده تنها شامل یک حالت بارگذاری با هیچ داده‌ای نیست. کامپیوتر مشتری باید تمام جاوااسکریپت‌ها را دانلود کرده و برنامه‌ی شما را رندر کند تا دریابد که حالا باید داده‌ها را بارگیری کند. این روش بسیار کارآمد نیست.
- **دریافت مستقیم در اثرها، ایجاد "شلال شبکه" را آسان می‌کند.** شما کامپوننت والد را رندر می‌کنید، برخی از داده‌ها را دریافت می‌کند، کامپوننت‌های فرزند را رندر می‌کند و سپس آن‌ها شروع به دریافت داده‌های خود می‌کنند. اگر شبکه خیلی سریع نباشد، این روش به طور قابل توجهی کندتر از دریافت همه‌ی داده‌ها به صورت موازی است.
- **دریافت مستقیم در اثرها معمولاً به این معنی است که داده‌ها را پیش‌بارگیری یا کش نمی‌کنید.** به عنوان مثال، اگر کامپوننت unmount شود و سپس دوباره mount شود، باید داده‌ها را دوباره بارگیری کند.
- **این روش بسیار کارآمد نیست.** زمانی که می‌خواهید تماس‌های `fetch` را به گونه‌ای بنویسید که از اشکالاتی مانند [شرایط رقابتی](https://maxrozen.com/race-conditions-fetching-data-react-with-useeffect) رنج نبرند، کد بسیاری از قالب‌های تکراری دارد.


This list of downsides is not specific to React. It applies to fetching data on mount with any library. Like with routing, data fetching is not trivial to do well, so we recommend the following approaches:

- **If you use a [framework](/learn/start-a-new-react-project#production-grade-react-frameworks), use its built-in data fetching mechanism.** Modern React frameworks have integrated data fetching mechanisms that are efficient and don't suffer from the above pitfalls.
- **Otherwise, consider using or building a client-side cache.** Popular open source solutions include [React Query](https://tanstack.com/query/latest), [useSWR](https://swr.vercel.app/), and [React Router 6.4+.](https://beta.reactrouter.com/en/main/start/overview) You can build your own solution too, in which case you would use Effects under the hood, but add logic for deduplicating requests, caching responses, and avoiding network waterfalls (by preloading data or hoisting data requirements to routes).

You can continue fetching data directly in Effects if neither of these approaches suit you.
////////////////
این لیست از معایب، به React خاص نیست. بلکه برای دریافت داده در هر کتابخانه‌ای استفاده می‌شود. همانند مسیریابی، دریافت داده، کاری ساده نیست، بنابراین ما رویکردهای زیر را توصیه می‌کنیم:

- **اگر از یک [چارچوب](/learn/start-a-new-react-project#production-grade-react-frameworks) استفاده می‌کنید، از مکانیزم داخلی آن برای دریافت داده استفاده کنید.** چارچوب‌های React مدرن، مکانیزم‌های دریافت داده‌ای را که کارآمد هستند و از مشکلات فوق رنج نمی‌برند، یکپارچه کرده‌اند.
- **در غیر این صورت، در نظر داشته باشید که از یک کش سمت مشتری استفاده کنید یا آن را بسازید.** راه‌حل‌های محبوب متن‌باز شامل [React Query](https://tanstack.com/query/latest)، [useSWR](https://swr.vercel.app/) و [React Router 6.4+](https://beta.reactrouter.com/en/main/start/overview) هستند. شما همچنین می‌توانید راه‌حل خودتان را بسازید، در این صورت از اثرها در زیرساخت استفاده می‌کنید، اما منطق تکرار درخواست‌ها، کش کردن پاسخ‌ها و جلوگیری از شلال شبکه (با پیش‌بارگیری داده‌ها یا بالا بردن نیازمندی‌های داده به مسیرها) را اضافه می‌کنید.

اگر هیچ یک از این روش‌ها مناسب شما نیست، می‌توانید مستقیماً به واکشی داده‌ها در Effects ادامه دهید.
</DeepDive>

### Sending analytics {/*sending-analytics*/}

Consider this code that sends an analytics event on the page visit:
این کد را در نظر بگیرید که یک رویداد تحلیلی را در بازدید از صفحه ارسال می کند:

```js
useEffect(() => {
  logVisit(url); // Sends a POST request
}, [url]);
```

In development, `logVisit` will be called twice for every URL, so you might be tempted to try to fix that. **We recommend keeping this code as is.** Like with earlier examples, there is no *user-visible* behavior difference between running it once and running it twice. From a practical point of view, `logVisit` should not do anything in development because you don't want the logs from the development machines to skew the production metrics. Your component remounts every time you save its file, so it logs extra visits in development anyway.
////////////////
در محیط توسعه، برای هر URL، `logVisit` دوبار فراخوانی می‌شود، بنابراین شما ممکن است تلاش کنید آن را برطرف کنید. **ما توصیه می‌کنیم که این کد را به همان شکلی که هست نگه دارید.** مانند مثال‌های قبلی، تفاوتی در رفتار *قابل مشاهده توسط کاربر* بین اجرای یک بار و دو بار وجود ندارد. از نظر عملی، `logVisit` در محیط توسعه باید هیچ کاری انجام ندهد، زیرا شما نمی‌خواهید لاگ‌های ماشین‌های توسعه، معیارهای تولید را تحریف کنند. هر بار که فایل کامپوننت شما ذخیره می‌شود، کامپوننت شما remount می‌شود، بنابراین در هر صورت، در محیط توسعه، بازدیدهای اضافی را لاگ می‌کند.

**In production, there will be no duplicate visit logs.**

To debug the analytics events you're sending, you can deploy your app to a staging environment (which runs in production mode) or temporarily opt out of [Strict Mode](/reference/react/StrictMode) and its development-only remounting checks. You may also send analytics from the route change event handlers instead of Effects. For more precise analytics, [intersection observers](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) can help track which components are in the viewport and how long they remain visible.
/////////////////
**در محیط تولید، لاگ بازدید تکراری وجود نخواهد داشت.**

برای اشکال‌زدایی رویدادهای تحلیلی که ارسال می‌کنید، می‌توانید برنامه‌ی خود را در یک محیط استیجینگ (که در حالت تولید اجرا می‌شود) راه‌اندازی کنید یا به طور موقت از [Strict Mode](/reference/react/StrictMode) و بررسی‌های remounting تنها در محیط توسعه آن خارج شوید. همچنین می‌توانید تحلیلی خود را از کنترل‌کننده‌های رویداد تغییر مسیر به جای اثرها ارسال کنید. برای تحلیل دقیق‌تر، [intersection observers](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) می‌توانند به شما کمک کنند که کدام کامپوننت‌ها در دیدگاه هستند و چقدر طول می‌کشد تا مشاهده شوند.

### Not an Effect: Initializing the application {/*not-an-effect-initializing-the-application*/}

Some logic should only run once when the application starts. You can put it outside your components:
////////////////
بعضی از منطق‌ها فقط هنگام شروع برنامه باید یک بار اجرا شوند. شما می‌توانید آن‌ها را خارج از کامپوننت‌های خود قرار دهید.

```js {2-3}
if (typeof window !== 'undefined') { // Check if we're running in the browser.
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```

This guarantees that such logic only runs once after the browser loads the page.
این تضمین می کند که چنین منطقی فقط یک بار پس از بارگیری صفحه توسط مرورگر اجرا می شود.

### Not an Effect: Buying a product {/*not-an-effect-buying-a-product*/}

Sometimes, even if you write a cleanup function, there's no way to prevent user-visible consequences of running the Effect twice. For example, maybe your Effect sends a POST request like buying a product:
////////////////////
گاهی اوقات، حتی اگر یک تابع پاک‌سازی بنویسید، راهی برای جلوگیری از پیامدهای قابل مشاهده توسط کاربر از اجرای دوباره‌ی اثر وجود ندارد. به عنوان مثال، شاید اثر شما مانند خرید یک محصول، یک درخواست POST ارسال کند:

```js {2-3}
useEffect(() => {
  // 🔴 Wrong: This Effect fires twice in development, exposing a problem in the code.
  fetch('/api/buy', { method: 'POST' });
}, []);
```

You wouldn't want to buy the product twice. However, this is also why you shouldn't put this logic in an Effect. What if the user goes to another page and then presses Back? Your Effect would run again. You don't want to buy the product when the user *visits* a page; you want to buy it when the user *clicks* the Buy button.

Buying is not caused by rendering; it's caused by a specific interaction. It should run only when the user presses the button. **Delete the Effect and move your `/api/buy` request into the Buy button event handler:**
///////////////////////////
شما نمی‌خواهید محصول را دو بار خریداری کنید. با این حال، همین دلیل است که نباید این منطق را در یک اثر قرار دهید. اگر کاربر به صفحه‌ی دیگری بروید و سپس دکمه‌ی بازگشت را فشار دهد، اثر شما دوباره اجرا می‌شود. شما نمی‌خواهید محصول را هنگامی که کاربر یک صفحه را *بازدید* می‌کند، خریداری کنید؛ شما می‌خواهید آن را هنگامی که کاربر دکمه‌ی خرید را *فشار می‌دهد*، خریداری کنید.

خرید، ناشی از رندرینگ نیست؛ بلکه ناشی از یک تعامل خاص است. باید فقط هنگامی که کاربر دکمه را فشار می‌دهد، اجرا شود. **اثر را حذف کرده و درخواست `/api/buy` خود را در کنترل‌کننده‌ی رویداد دکمه‌ی خرید قرار دهید:**

```js {2-3}
  function handleClick() {
    // ✅ Buying is an event because it is caused by a particular interaction.
    fetch('/api/buy', { method: 'POST' });
  }
```

**This illustrates that if remounting breaks the logic of your application, this usually uncovers existing bugs.** From a user's perspective, visiting a page shouldn't be different from visiting it, clicking a link, then pressing Back to view the page again. React verifies that your components abide by this principle by remounting them once in development.
///////////////
این نشان می‌دهد که اگر remounting منطق برنامه شما را خراب کند، این معمولاً باگ‌های موجود را آشکار می‌کند. از دیدگاه کاربر، بازدید از یک صفحه نباید با بازدید از آن، کلیک بر روی یک لینک و سپس فشار دادن دکمه‌ی بازگشت برای مشاهده‌ی صفحه مجدداً متفاوت باشد. React با remounting کردن کامپوننت‌ها یک بار در محیط توسعه، از این اصل اطمینان حاصل می‌کند که کامپوننت‌های شما این اصل را رعایت می‌کنند.

## Putting it all together {/*putting-it-all-together*/}

This playground can help you "get a feel" for how Effects work in practice.

This example uses [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) to schedule a console log with the input text to appear three seconds after the Effect runs. The cleanup function cancels the pending timeout. Start by pressing "Mount the component":
/////////////////
این محیط بازی‌سازی می‌تواند به شما کمک کند تا "احساس" کارکرد اثرها در عمل را پیدا کنید.

در این مثال از [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) برای زمانبندی یک لاگ کنسول با متن ورودی استفاده شده است که سه ثانیه پس از اجرای اثر ظاهر می‌شود. تابع پاک‌سازی زمانبندی درخواست را لغو می‌کند. با فشار دادن "Mount the component" شروع کنید:

<Sandpack>

```js
import { useState, useEffect } from 'react';

function Playground() {
  const [text, setText] = useState('a');

  useEffect(() => {
    function onTimeout() {
      console.log('⏰ ' + text);
    }

    console.log('🔵 Schedule "' + text + '" log');
    const timeoutId = setTimeout(onTimeout, 3000);

    return () => {
      console.log('🟡 Cancel "' + text + '" log');
      clearTimeout(timeoutId);
    };
  }, [text]);

  return (
    <>
      <label>
        What to log:{' '}
        <input
          value={text}
          onChange={e => setText(e.target.value)}
        />
      </label>
      <h1>{text}</h1>
    </>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Unmount' : 'Mount'} the component
      </button>
      {show && <hr />}
      {show && <Playground />}
    </>
  );
}
```

</Sandpack>

You will see three logs at first: `Schedule "a" log`, `Cancel "a" log`, and `Schedule "a" log` again. Three second later there will also be a log saying `a`. As you learned earlier, the extra schedule/cancel pair is because React remounts the component once in development to verify that you've implemented cleanup well.

Now edit the input to say `abc`. If you do it fast enough, you'll see `Schedule "ab" log` immediately followed by `Cancel "ab" log` and `Schedule "abc" log`. **React always cleans up the previous render's Effect before the next render's Effect.** This is why even if you type into the input fast, there is at most one timeout scheduled at a time. Edit the input a few times and watch the console to get a feel for how Effects get cleaned up.

Type something into the input and then immediately press "Unmount the component". Notice how unmounting cleans up the last render's Effect. Here, it clears the last timeout before it has a chance to fire.

Finally, edit the component above and comment out the cleanup function so that the timeouts don't get cancelled. Try typing `abcde` fast. What do you expect to happen in three seconds? Will `console.log(text)` inside the timeout print the *latest* `text` and produce five `abcde` logs? Give it a try to check your intuition!

Three seconds later, you should see a sequence of logs (`a`, `ab`, `abc`, `abcd`, and `abcde`) rather than five `abcde` logs. **Each Effect "captures" the `text` value from its corresponding render.**  It doesn't matter that the `text` state changed: an Effect from the render with `text = 'ab'` will always see `'ab'`. In other words, Effects from each render are isolated from each other. If you're curious how this works, you can read about [closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures).
///////////////////////
شما در ابتدا سه لاگ خواهید دید: `Schedule "a" log`، `Cancel "a" log` و دوباره `Schedule "a" log`. سه ثانیه بعد نیز یک لاگ با متن `a` ظاهر خواهد شد. همانطور که قبلاً یاد گرفتید، زوج schedule/cancel اضافی به دلیل remounting کامپوننت React یک بار در محیط توسعه است که برای بررسی پاک‌سازی مناسب پیاده‌سازی شده است.

حالا ورودی را به `abc` تغییر دهید. اگر به اندازه کافی سریع انجام دهید، `Schedule "ab" log` را فوراً دنبال شده از `Cancel "ab" log` و `Schedule "abc" log` خواهید دید. **React همیشه Effect رندر قبلی را پاک می‌کند، قبل از اجرای Effect رندر بعدی.** به همین دلیل، حتی اگر سریع به ورودی تایپ کنید، در هر لحظه حداکثر یک timeout زمان‌بندی شده وجود دارد. چند بار ورودی را ویرایش کرده و کنسول را برای درک بهتر نحوه پاک‌سازی Effect‌ها تماشا کنید.

چیزی را در ورودی تایپ کرده و سپس به طور فوری "Unmount the component" را فشار دهید. توجه کنید که unmounting پاک‌سازی Effect رندر قبلی را انجام می‌دهد. در اینجا، قبل از اینکه timeout فعال شود، آخرین timeout را پاک می‌کند.

در نهایت، کامپوننت بالا را ویرایش کرده و تابع پاک‌سازی را کامنت کنید تا timeout‌ها لغو نشوند. سریع `abcde` را تایپ کنید. چه چیزی را در سه ثانیه انتظار دارید؟ آیا `console.log(text)` در timeout، `text` *آخرین* را چاپ می‌کند و پنج لاگ `abcde` را تولید می‌کند؟ برای بررسی حدس خود، امتحان کنید!

سه ثانیه بعد، یک دنباله از لاگ‌ها (`a`، `ab`، `abc`، `abcd` و `abcde`) به جای پنج لاگ `abcde` خواهید دید. **هر Effect، مقدار `text` را از رندر مربوطه خود "capture" می‌کند.** این مهم نیست که وضعیت `text` تغییر کرده باشد: Effect از رندر با `text = 'ab'` همیشه `'ab'` را می‌بیند. به عبارت دیگر، Effect‌های هر رندر از هم جدا هستند. اگر علاقه‌مند به اینکه این چگونه کار می‌کند، می‌توانید درباره [closures](https://developer.mozilla.org/en.

<DeepDive>

#### Each render has its own Effects {/*each-render-has-its-own-effects*/}

You can think of `useEffect` as "attaching" a piece of behavior to the render output. Consider this Effect:
/////////
می‌توانید از `useEffect` به عنوان "ضمیمه کردن" یک قطعه از رفتار به خروجی رندر استفاده کنید. به عنوان مثال، این Effect را در نظر بگیرید:
```js
export default function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to {roomId}!</h1>;
}
```

Let's see what exactly happens as the user navigates around the app.
بیایید ببینیم وقتی کاربر در اطراف برنامه حرکت می کند دقیقاً چه اتفاقی می افتد.

#### Initial render {/*initial-render*/}

The user visits `<ChatRoom roomId="general" />`. Let's [mentally substitute](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time) `roomId` with `'general'`:

```js
  // JSX for the first render (roomId = "general")
  return <h1>Welcome to general!</h1>;
```

**The Effect is *also* a part of the rendering output.** The first render's Effect becomes:
**افکت *هم* بخشی از خروجی رندر است.** اولین افکت رندر تبدیل می شود:

```js
  // Effect for the first render (roomId = "general")
  () => {
    const connection = createConnection('general');
    connection.connect();
    return () => connection.disconnect();
  },
  // Dependencies for the first render (roomId = "general")
  ['general']
```

React runs this Effect, which connects to the `'general'` chat room.
React این افکت را اجرا می کند که به اتاق چت «عمومی» متصل می شود.

#### Re-render with same dependencies {/*re-render-with-same-dependencies*/}

Let's say `<ChatRoom roomId="general" />` re-renders. The JSX output is the same:
فرض کنید `<ChatRoom roomId="general" />` رندر مجدد می شود. خروجی JSX یکسان است:

```js
  // JSX for the second render (roomId = "general")
  return <h1>Welcome to general!</h1>;
```

React sees that the rendering output has not changed, so it doesn't update the DOM.
React می بیند که خروجی رندر تغییر نکرده است، بنابراین DOM را به روز نمی کند.

The Effect from the second render looks like this:
اثر رندر دوم به این صورت است:
Effect از رندر دوم به شکل زیر است:


```js
  // Effect for the second render (roomId = "general")
  () => {
    const connection = createConnection('general');
    connection.connect();
    return () => connection.disconnect();
  },
  // Dependencies for the second render (roomId = "general")
  ['general']
```

React compares `['general']` from the second render with `['general']` from the first render. **Because all dependencies are the same, React *ignores* the Effect from the second render.** It never gets called.
////////////////////
React `['general']` را از رندر دوم با `['general']` از رندر اول مقایسه می‌کند. **به دلیل اینکه تمام وابستگی‌ها یکسان هستند، React اثر رندر دوم را *نادیده می‌گیرد*.** هرگز فراخوانی نمی‌شود.

#### Re-render with different dependencies {/*re-render-with-different-dependencies*/}

Then, the user visits `<ChatRoom roomId="travel" />`. This time, the component returns different JSX:
///////////////
سپس، کاربر از `<ChatRoom roomId="travel" />` بازدید می کند. این بار، کامپوننت JSX متفاوتی را برمی گرداند:

```js
  // JSX for the third render (roomId = "travel")
  return <h1>Welcome to travel!</h1>;
```

React updates the DOM to change `"Welcome to general"` into `"Welcome to travel"`.
React با به‌روزرسانی DOM، "Welcome to general" را به "Welcome to travel" تغییر می‌دهد.

The Effect from the third render looks like this:
Effect از رندر سوم به شکل زیر است:

```js
  // Effect for the third render (roomId = "travel")
  () => {
    const connection = createConnection('travel');
    connection.connect();
    return () => connection.disconnect();
  },
  // Dependencies for the third render (roomId = "travel")
  ['travel']
```

React compares `['travel']` from the third render with `['general']` from the second render. One dependency is different: `Object.is('travel', 'general')` is `false`. The Effect can't be skipped.
/////////////
React `['travel']` را از رندر سوم با `['general']` از رندر دوم مقایسه می‌کند. یک وابستگی متفاوت است: `Object.is('travel', 'general')` برابر `false` است. Effect نمی‌تواند نادیده گرفته شود.

**Before React can apply the Effect from the third render, it needs to clean up the last Effect that _did_ run.** The second render's Effect was skipped, so React needs to clean up the first render's Effect. If you scroll up to the first render, you'll see that its cleanup calls `disconnect()` on the connection that was created with `createConnection('general')`. This disconnects the app from the `'general'` chat room.
///////
**قبل از اینکه React بتواند Effect رندر سوم را اعمال کند، باید آخرین Effectی که اجرا شده است را پاک کند.** Effect رندر دوم نادیده گرفته شد، بنابراین React باید Effect رندر اول را پاک کند. اگر به بالا بروید تا رندر اول، خواهید دید که پاک‌سازی آن، `disconnect()` را روی اتصالی که با `createConnection('general')` ایجاد شده بود، فراخوانی می‌کند. این برنامه را از اتاق چت `'general'` قطع می‌کند.

After that, React runs the third render's Effect. It connects to the `'travel'` chat room.
بعد از آن، React Effect رندر سوم را اجرا می‌کند. به اتاق چت `'travel'` متصل می‌شود..

#### Unmount {/*unmount*/}

Finally, let's say the user navigates away, and the `ChatRoom` component unmounts. React runs the last Effect's cleanup function. The last Effect was from the third render. The third render's cleanup destroys the `createConnection('travel')` connection. So the app disconnects from the `'travel'` room.
////////////////////////
در نهایت، فرض کنید کاربر به صفحه‌ی دیگری بروید و کامپوننت `ChatRoom` unmount شود. React تابع پاک‌سازی آخرین Effect را اجرا می‌کند. آخرین Effect از رندر سوم بود. پاک‌سازی رندر سوم، اتصال `createConnection('travel')` را نابود می‌کند. بنابراین برنامه از اتاق چت `'travel'` قطع می‌شود.

#### Development-only behaviors {/*development-only-behaviors*/}

When [Strict Mode](/reference/react/StrictMode) is on, React remounts every component once after mount (state and DOM are preserved). This [helps you find Effects that need cleanup](#step-3-add-cleanup-if-needed) and exposes bugs like race conditions early. Additionally, React will remount the Effects whenever you save a file in development. Both of these behaviors are development-only.
/////////////////////
وقتی Strict Mode فعال است، React هر کامپوننت را یک بار پس از نصب دوباره نصب می‌کند (state و DOM حفظ می‌شوند). این به شما کمک می‌کند تا اثراتی را که نیاز به cleanup دارند را پیدا کنید (به مرحله ۳ اضافه کنید) و باگ‌هایی مانند race condition را در زمان سریعتر پیدا کنید. همچنین، React هر زمان که یک فایل را در حالت توسعه ذخیره کردید، اثرات را مجددا نصب می‌کند. هر دوی این رفتارها فقط در محیط توسعه اعمال می‌شوند.

</DeepDive>

<Recap>

- Unlike events, Effects are caused by rendering itself rather than a particular interaction.
- Effects let you synchronize a component with some external system (third-party API, network, etc).
- By default, Effects run after every render (including the initial one).
- React will skip the Effect if all of its dependencies have the same values as during the last render.
- You can't "choose" your dependencies. They are determined by the code inside the Effect.
- Empty dependency array (`[]`) corresponds to the component "mounting", i.e. being added to the screen.
- In Strict Mode, React mounts components twice (in development only!) to stress-test your Effects.
- If your Effect breaks because of remounting, you need to implement a cleanup function.
- React will call your cleanup function before the Effect runs next time, and during the unmount.
//////////////////
- برخلاف رویدادها، Effects ناشی از خود رندرینگ هستند نه یک تعامل خاص.
- Effects به شما امکان می‌دهند تا یک کامپوننت را با یک سیستم خارجی هماهنگ کنید (API شخص ثالث، شبکه و غیره).
- به طور پیش‌فرض، Effects بعد از هر رندر اجرا می‌شوند (شامل رندر اولیه نیز).
- اگر همه وابستگی‌های یک اثر مقداری مشابه با زمان رندر گذشته داشته باشند، ریکت اثر را رد خواهد کرد.
- شما نمی‌توانید "وابستگی‌های" خود را "انتخاب" کنید. آن‌ها بر اساس کد درون اثر تعیین می‌شوند.
- آرایه وابستگی‌های خالی (`[]`) مربوط به "نصب کامپوننت" است، یعنی وقتی به صفحه افزوده می‌شود.
- در حالت سختگیرانه، ریکت کامپوننت‌ها را دو بار نصب می‌کند (فقط در محیط توسعه!) تا Effects شما را تست کند.
- اگر اثر شما به دلیل بازنصب کردن خراب شود، باید یک تابع تمیزکاری پیاده‌سازی کنید.
- ریکت قبل از اجرای بعدی اثر و در هنگام عدم نصب، تابع تمیزکاری شما را فراخوانی خواهد کرد.
</Recap>

<Challenges>

#### Focus a field on mount {/*focus-a-field-on-mount*/}

In this example, the form renders a `<MyInput />` component.

Use the input's [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) method to make `MyInput` automatically focus when it appears on the screen. There is already a commented out implementation, but it doesn't quite work. Figure out why it doesn't work, and fix it. (If you're familiar with the `autoFocus` attribute, pretend that it does not exist: we are reimplementing the same functionality from scratch.)

<Sandpack>

```js MyInput.js active
import { useEffect, useRef } from 'react';

export default function MyInput({ value, onChange }) {
  const ref = useRef(null);

  // TODO: This doesn't quite work. Fix it.
  // ref.current.focus()    

  return (
    <input
      ref={ref}
      value={value}
      onChange={onChange}
    />
  );
}
```

```js App.js hidden
import { useState } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const [show, setShow] = useState(false);
  const [name, setName] = useState('Taylor');
  const [upper, setUpper] = useState(false);
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} form</button>
      <br />
      <hr />
      {show && (
        <>
          <label>
            Enter your name:
            <MyInput
              value={name}
              onChange={e => setName(e.target.value)}
            />
          </label>
          <label>
            <input
              type="checkbox"
              checked={upper}
              onChange={e => setUpper(e.target.checked)}
            />
            Make it uppercase
          </label>
          <p>Hello, <b>{upper ? name.toUpperCase() : name}</b></p>
        </>
      )}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>


To verify that your solution works, press "Show form" and verify that the input receives focus (becomes highlighted and the cursor is placed inside). Press "Hide form" and "Show form" again. Verify the input is highlighted again.

`MyInput` should only focus _on mount_ rather than after every render. To verify that the behavior is right, press "Show form" and then repeatedly press the "Make it uppercase" checkbox. Clicking the checkbox should _not_ focus the input above it.

<Solution>

Calling `ref.current.focus()` during render is wrong because it is a *side effect*. Side effects should either be placed inside an event handler or be declared with `useEffect`. In this case, the side effect is _caused_ by the component appearing rather than by any specific interaction, so it makes sense to put it in an Effect.

To fix the mistake, wrap the `ref.current.focus()` call into an Effect declaration. Then, to ensure that this Effect runs only on mount rather than after every render, add the empty `[]` dependencies to it.

<Sandpack>

```js MyInput.js active
import { useEffect, useRef } from 'react';

export default function MyInput({ value, onChange }) {
  const ref = useRef(null);

  useEffect(() => {
    ref.current.focus();
  }, []);

  return (
    <input
      ref={ref}
      value={value}
      onChange={onChange}
    />
  );
}
```

```js App.js hidden
import { useState } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const [show, setShow] = useState(false);
  const [name, setName] = useState('Taylor');
  const [upper, setUpper] = useState(false);
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} form</button>
      <br />
      <hr />
      {show && (
        <>
          <label>
            Enter your name:
            <MyInput
              value={name}
              onChange={e => setName(e.target.value)}
            />
          </label>
          <label>
            <input
              type="checkbox"
              checked={upper}
              onChange={e => setUpper(e.target.checked)}
            />
            Make it uppercase
          </label>
          <p>Hello, <b>{upper ? name.toUpperCase() : name}</b></p>
        </>
      )}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

</Solution>

#### Focus a field conditionally {/*focus-a-field-conditionally*/}

This form renders two `<MyInput />` components.

Press "Show form" and notice that the second field automatically gets focused. This is because both of the `<MyInput />` components try to focus the field inside. When you call `focus()` for two input fields in a row, the last one always "wins".

Let's say you want to focus the first field. The first `MyInput` component now receives a boolean `shouldFocus` prop set to `true`. Change the logic so that `focus()` is only called if the `shouldFocus` prop received by `MyInput` is `true`.

<Sandpack>

```js MyInput.js active
import { useEffect, useRef } from 'react';

export default function MyInput({ shouldFocus, value, onChange }) {
  const ref = useRef(null);

  // TODO: call focus() only if shouldFocus is true.
  useEffect(() => {
    ref.current.focus();
  }, []);

  return (
    <input
      ref={ref}
      value={value}
      onChange={onChange}
    />
  );
}
```

```js App.js hidden
import { useState } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const [show, setShow] = useState(false);
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  const [upper, setUpper] = useState(false);
  const name = firstName + ' ' + lastName;
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} form</button>
      <br />
      <hr />
      {show && (
        <>
          <label>
            Enter your first name:
            <MyInput
              value={firstName}
              onChange={e => setFirstName(e.target.value)}
              shouldFocus={true}
            />
          </label>
          <label>
            Enter your last name:
            <MyInput
              value={lastName}
              onChange={e => setLastName(e.target.value)}
              shouldFocus={false}
            />
          </label>
          <p>Hello, <b>{upper ? name.toUpperCase() : name}</b></p>
        </>
      )}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

To verify your solution, press "Show form" and "Hide form" repeatedly. When the form appears, only the *first* input should get focused. This is because the parent component renders the first input with `shouldFocus={true}` and the second input with `shouldFocus={false}`. Also check that both inputs still work and you can type into both of them.

<Hint>

You can't declare an Effect conditionally, but your Effect can include conditional logic.

</Hint>

<Solution>

Put the conditional logic inside the Effect. You will need to specify `shouldFocus` as a dependency because you are using it inside the Effect. (This means that if some input's `shouldFocus` changes from `false` to `true`, it will focus after mount.)

<Sandpack>

```js MyInput.js active
import { useEffect, useRef } from 'react';

export default function MyInput({ shouldFocus, value, onChange }) {
  const ref = useRef(null);

  useEffect(() => {
    if (shouldFocus) {
      ref.current.focus();
    }
  }, [shouldFocus]);

  return (
    <input
      ref={ref}
      value={value}
      onChange={onChange}
    />
  );
}
```

```js App.js hidden
import { useState } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const [show, setShow] = useState(false);
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  const [upper, setUpper] = useState(false);
  const name = firstName + ' ' + lastName;
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} form</button>
      <br />
      <hr />
      {show && (
        <>
          <label>
            Enter your first name:
            <MyInput
              value={firstName}
              onChange={e => setFirstName(e.target.value)}
              shouldFocus={true}
            />
          </label>
          <label>
            Enter your last name:
            <MyInput
              value={lastName}
              onChange={e => setLastName(e.target.value)}
              shouldFocus={false}
            />
          </label>
          <p>Hello, <b>{upper ? name.toUpperCase() : name}</b></p>
        </>
      )}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

</Solution>

#### Fix an interval that fires twice {/*fix-an-interval-that-fires-twice*/}

This `Counter` component displays a counter that should increment every second. On mount, it calls [`setInterval`.](https://developer.mozilla.org/en-US/docs/Web/API/setInterval) This causes `onTick` to run every second. The `onTick` function increments the counter.

However, instead of incrementing once per second, it increments twice. Why is that? Find the cause of the bug and fix it.

<Hint>

Keep in mind that `setInterval` returns an interval ID, which you can pass to [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval) to stop the interval.

</Hint>

<Sandpack>

```js Counter.js active
import { useState, useEffect } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    function onTick() {
      setCount(c => c + 1);
    }

    setInterval(onTick, 1000);
  }, []);

  return <h1>{count}</h1>;
}
```

```js App.js hidden
import { useState } from 'react';
import Counter from './Counter.js';

export default function Form() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} counter</button>
      <br />
      <hr />
      {show && <Counter />}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

<Solution>

When [Strict Mode](/reference/react/StrictMode) is on (like in the sandboxes on this site), React remounts each component once in development. This causes the interval to be set up twice, and this is why each second the counter increments twice.

However, React's behavior is not the *cause* of the bug: the bug already exists in the code. React's behavior makes the bug more noticeable. The real cause is that this Effect starts a process but doesn't provide a way to clean it up.

To fix this code, save the interval ID returned by `setInterval`, and implement a cleanup function with [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval):

<Sandpack>

```js Counter.js active
import { useState, useEffect } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    function onTick() {
      setCount(c => c + 1);
    }

    const intervalId = setInterval(onTick, 1000);
    return () => clearInterval(intervalId);
  }, []);

  return <h1>{count}</h1>;
}
```

```js App.js hidden
import { useState } from 'react';
import Counter from './Counter.js';

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} counter</button>
      <br />
      <hr />
      {show && <Counter />}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

In development, React will still remount your component once to verify that you've implemented cleanup well. So there will be a `setInterval` call, immediately followed by `clearInterval`, and `setInterval` again. In production, there will be only one `setInterval` call. The user-visible behavior in both cases is the same: the counter increments once per second.

</Solution>

#### Fix fetching inside an Effect {/*fix-fetching-inside-an-effect*/}

This component shows the biography for the selected person. It loads the biography by calling an asynchronous function `fetchBio(person)` on mount and whenever `person` changes. That asynchronous function returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) which eventually resolves to a string. When fetching is done, it calls `setBio` to display that string under the select box.

<Sandpack>

```js App.js
import { useState, useEffect } from 'react';
import { fetchBio } from './api.js';

export default function Page() {
  const [person, setPerson] = useState('Alice');
  const [bio, setBio] = useState(null);

  useEffect(() => {
    setBio(null);
    fetchBio(person).then(result => {
      setBio(result);
    });
  }, [person]);

  return (
    <>
      <select value={person} onChange={e => {
        setPerson(e.target.value);
      }}>
        <option value="Alice">Alice</option>
        <option value="Bob">Bob</option>
        <option value="Taylor">Taylor</option>
      </select>
      <hr />
      <p><i>{bio ?? 'Loading...'}</i></p>
    </>
  );
}
```

```js api.js hidden
export async function fetchBio(person) {
  const delay = person === 'Bob' ? 2000 : 200;
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('This is ' + person + '’s bio.');
    }, delay);
  })
}

```

</Sandpack>


There is a bug in this code. Start by selecting "Alice". Then select "Bob" and then immediately after that select "Taylor". If you do this fast enough, you will notice that bug: Taylor is selected, but the paragraph below says "This is Bob's bio."

Why does this happen? Fix the bug inside this Effect.

<Hint>

If an Effect fetches something asynchronously, it usually needs cleanup.

</Hint>

<Solution>

To trigger the bug, things need to happen in this order:

- Selecting `'Bob'` triggers `fetchBio('Bob')`
- Selecting `'Taylor'` triggers `fetchBio('Taylor')`
- **Fetching `'Taylor'` completes *before* fetching `'Bob'`**
- The Effect from the `'Taylor'` render calls `setBio('This is Taylor’s bio')`
- Fetching `'Bob'` completes
- The Effect from the `'Bob'` render calls `setBio('This is Bob’s bio')`

This is why you see Bob's bio even though Taylor is selected. Bugs like this are called [race conditions](https://en.wikipedia.org/wiki/Race_condition) because two asynchronous operations are "racing" with each other, and they might arrive in an unexpected order.

To fix this race condition, add a cleanup function:

<Sandpack>

```js App.js
import { useState, useEffect } from 'react';
import { fetchBio } from './api.js';

export default function Page() {
  const [person, setPerson] = useState('Alice');
  const [bio, setBio] = useState(null);
  useEffect(() => {
    let ignore = false;
    setBio(null);
    fetchBio(person).then(result => {
      if (!ignore) {
        setBio(result);
      }
    });
    return () => {
      ignore = true;
    }
  }, [person]);

  return (
    <>
      <select value={person} onChange={e => {
        setPerson(e.target.value);
      }}>
        <option value="Alice">Alice</option>
        <option value="Bob">Bob</option>
        <option value="Taylor">Taylor</option>
      </select>
      <hr />
      <p><i>{bio ?? 'Loading...'}</i></p>
    </>
  );
}
```

```js api.js hidden
export async function fetchBio(person) {
  const delay = person === 'Bob' ? 2000 : 200;
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('This is ' + person + '’s bio.');
    }, delay);
  })
}

```

</Sandpack>

Each render's Effect has its own `ignore` variable. Initially, the `ignore` variable is set to `false`. However, if an Effect gets cleaned up (such as when you select a different person), its `ignore` variable becomes `true`. So now it doesn't matter in which order the requests complete. Only the last person's Effect will have `ignore` set to `false`, so it will call `setBio(result)`. Past Effects have been cleaned up, so the `if (!ignore)` check will prevent them from calling `setBio`:

- Selecting `'Bob'` triggers `fetchBio('Bob')`
- Selecting `'Taylor'` triggers `fetchBio('Taylor')` **and cleans up the previous (Bob's) Effect**
- Fetching `'Taylor'` completes *before* fetching `'Bob'`
- The Effect from the `'Taylor'` render calls `setBio('This is Taylor’s bio')`
- Fetching `'Bob'` completes
- The Effect from the `'Bob'` render **does not do anything because its `ignore` flag was set to `true`**

In addition to ignoring the result of an outdated API call, you can also use [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) to cancel the requests that are no longer needed. However, by itself this is not enough to protect against race conditions. More asynchronous steps could be chained after the fetch, so using an explicit flag like `ignore` is the most reliable way to fix this type of problems.

</Solution>

</Challenges>

