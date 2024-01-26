---
title: Keeping Components Pure(خالص نگه داشتن components ها )
---

<Intro>

Some JavaScript functions are *pure.* Pure functions only perform a calculation and nothing more. By strictly only writing your components as pure functions, you can avoid an entire class of baffling bugs and unpredictable behavior as your codebase grows. To get these benefits, though, there are a few rules you must follow.

/////
برخی از توابع جاوا اسکریپت *خالص هستند.* توابع خالص فقط یک محاسبه را انجام می دهند و نه بیشتر. با صرفاً نوشتن component های خود به عنوان توابع خالص، می توانید از یک کلاس کامل از اشکالات گیج کننده و رفتار غیرقابل پیش بینی با رشد پایگاه کد خود جلوگیری کنید. اما برای به دست آوردن این مزایا، چند قانون وجود دارد که باید رعایت کنید.

</Intro>

<YouWillLearn>

* What purity is and how it helps you avoid bugs
* How to keep components pure by keeping changes out of the render phase
* How to use Strict Mode to find mistakes in your components

////

* خلوص چیست و چگونه به شما کمک می کند تا از اشکالات جلوگیری کنید
* نحوه خالص نگه داشتن اجزا با دور نگه داشتن تغییرات از فاز رندر
* نحوه استفاده از حالت سختگیرانه برای یافتن اشتباهات در اجزای خود

</YouWillLearn>

## Purity: Components as formulas(خلوص: کامپوننت ها به عنوان فرمول) {/*purity-components-as-formulas*/}

>به دلیل اینکه React بر اساس این مفهوم طراحی شده است، هر کامپوننتی که شما می نویسید، یک تابع خالص فرض می شود. این بدان معناست که کامپوننت های React شما باید همیشه JSX یکسانی را با توجه به ورودی های یکسان خود بازگردانند. به عبارت دیگر، کامپوننت های شما باید همیشه همان خروجی را تولید کنند، بدون توجه به ترتیب رندرینگ موارد.    


In computer science (and especially the world of functional programming), [a pure function](https://wikipedia.org/wiki/Pure_function) is a function with the following characteristics:

* **It minds its own business.** It does not change any objects or variables that existed before it was called.
* **Same inputs, same output.** Given the same inputs, a pure function should always return the same result.

////
در علوم کامپیوتر (و به ویژه در دنیای برنامه نویسی تابعی)، تابع خالص تابعی است با ویژگی های زیر:

* **به کار خودش فکر می کند.** هیچ شی یا متغیری را که قبل از فراخوانی وجود داشته است تغییر نمی دهد.
* **ورودی های یکسان، خروجی یکسان.** با توجه به ورودی های یکسان، یک تابع خالص همیشه باید همان نتیجه را برگرداند.


Consider this math formula: <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math>.
این فرمول ریاضی را در نظر بگیرید: <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math>.

If <Math><MathI>x</MathI> = 2</Math> then <Math><MathI>y</MathI> = 4</Math>. Always. 

If <Math><MathI>x</MathI> = 3</Math> then <Math><MathI>y</MathI> = 6</Math>. Always. 

If <Math><MathI>x</MathI> = 3</Math>, <MathI>y</MathI> won't sometimes be <Math>9</Math> or <Math>–1</Math> or <Math>2.5</Math> depending on the time of day or the state of the stock market. 

If <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> and <Math><MathI>x</MathI> = 3</Math>, <MathI>y</MathI> will _always_ be <Math>6</Math>. 

If we made this into a JavaScript function, it would look like this:
اگر این را به یک تابع جاوا اسکریپت تبدیل کنیم، به شکل زیر خواهد بود:

```js
function double(number) {
  return 2 * number;
}
```

In the above example, `double` is a **pure function.** If you pass it `3`, it will return `6`. Always.

React is designed around this concept. **React assumes that every component you write is a pure function.** This means that React components you write must always return the same JSX given the same inputs:

////
در مثال بالا، double یک تابع خالص است. اگر از آن 3 عبور کنید، 6 برمی گردد. همیشه.

React حول این مفهوم طراحی شده است. **React فرض می کند که هر کامپوننت ای که می نویسید یک تابع خالص است.** این بدان معنی است که کامپوننت های React که می نویسید باید همیشه همان JSX را با توجه به ورودی های یکسان برگردانند:

<Sandpack>

```js App.js
function Recipe({ drinkers }) {
  return (
    <ol>    
      <li>Boil {drinkers} cups of water.</li>
      <li>Add {drinkers} spoons of tea and {0.5 * drinkers} spoons of spice.</li>
      <li>Add {0.5 * drinkers} cups of milk to boil and sugar to taste.</li>
    </ol>
  );
}

export default function App() {
  return (
    <section>
      <h1>Spiced Chai Recipe</h1>
      <h2>For two</h2>
      <Recipe drinkers={2} />
      <h2>For a gathering</h2>
      <Recipe drinkers={4} />
    </section>
  );
}
```

</Sandpack>

When you pass `drinkers={2}` to `Recipe`, it will return JSX containing `2 cups of water`. Always. 
با ارسال drinkers={2} به Recipe، JSX حاوی 2 فنجان آب را همیشه باز می گرداند

If you pass `drinkers={4}`, it will return JSX containing `4 cups of water`. Always.
با ارسال drinkers={4} به Recipe، JSX حاوی 4 فنجان آب را همیشه باز می گرداند

Just like a math formula. 
درست مثل یک فرمول ریاضی.

You could think of your components as recipes: if you follow them and don't introduce new ingredients during the cooking process, you will get the same dish every time. That "dish" is the JSX that the component serves to React to [render.](/learn/render-and-commit)
می توانید از کامپوننت های خود به عنوان دستور العمل ها فکر کنید: اگر آنها را دنبال کنید و در طول فرآیند پخت و پز مواد جدیدی معرفی نکنید، هر بار یک غذای یکسان دریافت خواهید کرد. این “غذا” JSX است که کامپوننت آن را به React برای رندر کردن ارائه می دهد1

<Illustration src="/images/docs/illustrations/i_puritea-recipe.png" alt="A tea recipe for x people: take x cups of water, add x spoons of tea and 0.5x spoons of spices, and 0.5x cups of milk" />

## Side Effects: (un)intended consequences(عوارض جانبی عواقب ناخواسته) {/*side-effects-unintended-consequences*/}

React's rendering process must always be pure. Components should only *return* their JSX, and not *change* any objects or variables that existed before rendering—that would make them impure!
به دلیل اینکه فرآیند رندرینگ React همیشه باید خالص باشد، کامپوننت ها فقط باید JSX خود را بازگردانند و هیچ تغییری در شی ها یا متغیرهایی که قبل از رندرینگ وجود داشتند، ایجاد نکنند - این باعث می شود که آنها ناخالص شوند1

Here is a component that breaks this rule:
در اینجا یک کاکپوننت است که این قانون را زیر پا می گذارد:

<Sandpack>

```js
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}
```

</Sandpack>

This component is reading and writing a `guest` variable declared outside of it. This means that **calling this component multiple times will produce different JSX!** And what's more, if _other_ components read `guest`, they will produce different JSX, too, depending on when they were rendered! That's not predictable.
////
این کامپوننت دارای عوارض جانبی است. به علت خواندن و نوشتن متغیر مهمانی که خارج از آن تعریف شده است، فراخوانی چندین بار این کامپوننت، JSX متفاوتی را تولید خواهد کرد! و بیشتر، اگر کامپوننت های دیگر مهمان را بخوانند، همچنین JSX متفاوتی را تولید خواهند کرد، بسته به زمانی که رندر شده اند! این قابل پیش بینی نیست1

Going back to our formula <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math>, now even if <Math><MathI>x</MathI> = 2</Math>, we cannot trust that <Math><MathI>y</MathI> = 4</Math>. Our tests could fail, our users would be baffled, planes would fall out of the sky—you can see how this would lead to confusing bugs!
////

بازگشت به فرمول ما y = 2x، حتی اگر x = 2 باشد، نمی توانیم به این باور برسیم که y = 4. آزمایش های ما ممکن است شکست بخورند، کاربران ما ممکن است گیج شوند، هواپیماها ممکن است از آسمان بیافتند - می توانید ببینید که این چگونه باعث ایجاد باگ های گیج کننده می شود! 1

You can fix this component by [passing `guest` as a prop instead](/learn/passing-props-to-a-component):
شما میتوانید کامل کنید این کامپوننت را به وسیله ی ارسال quest به عنوان پراپ

<Sandpack>

```js
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup guest={1} />
      <Cup guest={2} />
      <Cup guest={3} />
    </>
  );
}
```

</Sandpack>

Now your component is pure, as the JSX it returns only depends on the `guest` prop.
حالا کامپوننت شما خالص است، زیرا JSX آن تنها به guest prop وابسته است1

In general, you should not expect your components to be rendered in any particular order. It doesn't matter if you call <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> before or after <Math><MathI>y</MathI> = 5<MathI>x</MathI></Math>: both formulas will resolve independently of each other. In the same way, each component should only "think for itself", and not attempt to coordinate with or depend upon others during rendering. Rendering is like a school exam: each component should calculate JSX on their own!

////
به طور کلی، شما نباید انتظار داشته باشید که کامپوننت های شما به ترتیب خاصی رندر شوند. مهم نیست که آیا شما y = 2x را قبل یا بعد از y = 5x فراخوانی می کنید: هر دو فرمول به صورت مستقل از یکدیگر حل خواهند شد. به همین ترتیب، هر کامپوننت باید فقط “برای خودش فکر کند” و در طول رندرینگ تلاش نکند که با دیگران هماهنگ شود یا به آنها وابسته باشد. رندرینگ شبیه به یک آزمون مدرسه است: هر کامپوننت باید JSX خود را به تنهایی محاسبه کند! 1

<DeepDive>

#### Detecting impure calculations with StrictMode(تشخیص محاسبات ناخالص با StrictMode) {/*detecting-impure-calculations-with-strict-mode*/}

Although you might not have used them all yet, in React there are three kinds of inputs that you can read while rendering: [props](/learn/passing-props-to-a-component), [state](/learn/state-a-components-memory), and [context.](/learn/passing-data-deeply-with-context) You should always treat these 
inputs as read-only.
////
اگرچه شاید هنوز از همه آنها استفاده نکرده باشید، در React سه نوع ورودی وجود دارد که می توانید در حین رندرینگ آنها را بخوانید: props، state و context. همیشه باید این ورودی ها را به عنوان فقط خواندنی در نظر بگیرید1

When you want to *change* something in response to user input, you should [set state](/learn/state-a-components-memory) instead of writing to a variable. You should never change preexisting variables or objects while your component is rendering.

////
شما باید به جای نوشتن در یک متغیر، وضعیت را تنظیم کنید، زیرا در صورت تغییر متغیر قبلی یا شی ها، در حین رندرینگ کامپوننت، مشکلاتی ایجاد خواهد شد1

React offers a "Strict Mode" in which it calls each component's function twice during development. **By calling the component functions twice, Strict Mode helps find components that break these rules.**

////
React یک “Strict Mode” ارائه می دهد که در آن هر تابع کامپوننت را در طول توسعه دو بار فراخوانی می کند. با فراخوانی تابع کامپوننت دو بار، Strict Mode به پیدا کردن کامپوننت هایی که این قوانین را نقض می کنند، کمک می کند1

Notice how the original example displayed "Guest #2", "Guest #4", and "Guest #6" instead of "Guest #1", "Guest #2", and "Guest #3". The original function was impure, so calling it twice broke it. But the fixed pure version works even if the function is called twice every time. **Pure functions only calculate, so calling them twice won't change anything**--just like calling `double(2)` twice doesn't change what's returned, and solving <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> twice doesn't change what <MathI>y</MathI> is. Same inputs, same outputs. Always.

////
توجه کنید که مثال اصلی “Guest #2”، “Guest #4” و “Guest #6” را به جای “Guest #1”، “Guest #2” و “Guest #3” نشان داد. تابع اصلی ناخالص بود، بنابراین فراخوانی آن دو بار آن را خراب کرد. اما نسخه خالص ترمیم شده حتی اگر تابع هر بار دو بار فراخوانی شود، کار می کند. توابع خالص فقط محاسبه می کنند، بنابراین دو بار فراخوانی آنها هیچ چیز را تغییر نمی دهد - تماماً مانند دو بار فراخوانی double(2) که چیزی را تغییر نمی دهد، و حل y = 2x که دو بار حل شده است و y را تغییر نمی دهد. ورودی های یکسان، خروجی های یکسان. همیشه1

Strict Mode has no effect in production, so it won't slow down the app for your users. To opt into Strict Mode, you can wrap your root component into `<React.StrictMode>`. Some frameworks do this by default.

////
Strict Mode در محصولات تأثیری ندارد، بنابراین برنامه شما را برای کاربرانتان کند نمی کند. برای ورود به Strict Mode، می توانید کامپوننت ریشه خود را در `<React.StrictMode>` بسته بندی کنید. برخی از چارچوب ها این کار را به صورت پیش فرض انجام می دهند1

</DeepDive>

### Local mutation: Your component's little secret(عوارض جانبی(تغییر) محلی: راز کوچک کامپوننت شما1) {/*local-mutation-your-components-little-secret*/}

In the above example, the problem was that the component changed a *preexisting* variable while rendering. This is often called a **"mutation"** to make it sound a bit scarier. Pure functions don't mutate variables outside of the function's scope or objects that were created before the call—that makes them impure!

////
در مثال بالا، مشکل این بود که کامپوننت در حین رندرینگ متغیر قبلی را تغییر داد. این اغلب به عنوان “mutation” نامیده می شود تا کمی ترسناک تر به نظر بیاید. توابع خالص متغیرها را خارج از دامنه تابع یا شی هایی که قبل از فراخوانی ایجاد شده اند، تغییر نمی دهند - این باعث می شود که آنها ناخالص شوند! 

However, **it's completely fine to change variables and objects that you've *just* created while rendering.** In this example, you create an `[]` array, assign it to a `cups` variable, and then `push` a dozen cups into it:

////

به طور کلی، شما نباید انتظار داشته باشید که کامپوننت های شما به ترتیب خاصی رندر شوند. مهم نیست که آیا شما y = 2x را قبل یا بعد از y = 5x فراخوانی می کنید: هر دو فرمول به صورت مستقل از یکدیگر حل خواهند شد. به همین ترتیب، هر کامپوننت باید فقط “برای خودش فکر کند”، و در طول رندرینگ تلاش نکند که با دیگران هماهنگ شود یا به آنها وابسته باشد. رندرینگ شبیه به یک آزمون مدرسه است: هر کامپوننت باید JSX خود را به تنهایی محاسبه کند! 1

<Sandpack>

```js
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  let cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
```

</Sandpack>

If the `cups` variable or the `[]` array were created outside the `TeaGathering` function, this would be a huge problem! You would be changing a *preexisting* object by pushing items into that array.

However, it's fine because you've created them *during the same render*, inside `TeaGathering`. No code outside of `TeaGathering` will ever know that this happened. This is called **"local mutation"**—it's like your component's little secret.

////
اگر متغیر cups یا آرایه [] خارج از تابع TeaGathering ایجاد شده بود، این یک مشکل بزرگ خواهد بود! شما با وارد کردن موارد در آن آرایه، یک شی قبلی را تغییر می دهید.

اما این مشکلی نیست زیرا آنها را در همان رندر، در داخل TeaGathering ایجاد کرده اید. هیچ کدی خارج از TeaGathering هرگز نخواهد دانست که این اتفاق افتاده است. این “تغییرات محلی” نامیده می شود - مثل راز کوچک کامپوننت شما1

## Where you _can_ cause side effects(جایی که می توانید عوارض جانبی ایجاد کنید) {/*where-you-_can_-cause-side-effects*/}

While functional programming relies heavily on purity, at some point, somewhere, _something_ has to change. That's kind of the point of programming! These changes—updating the screen, starting an animation, changing the data—are called **side effects.** They're things that happen _"on the side"_, not during rendering.

////
در حالی که برنامه نویسی عملکردی به شدت به خلوص متکی است، در یک نقطه، جایی، چیزی باید تغییر کند. این یک نوع نکته برنامه نویسی است! این تغییرات - به روز رسانی صفحه، شروع انیمیشن، تغییر داده ها - عوارض جانبی نامیده می شود. آنها چیزهایی هستند که "در کنار" اتفاق می‌افتند، نه در حین رندر.
////
اگرچه برنامه نویسی تقریباً برای تغییر چیزی طراحی شده است، اما در نهایت، در هر جایی، چیزی باید تغییر کند. این نوع تغییرات - به روز رسانی صفحه، شروع یک انیمیشن، تغییر داده ها - به عنوان عوارض جانبی شناخته می شوند. آنها چیزهایی هستند که “در کنار” اتفاق می افتند، نه در طول رندرینگ1

In React, **side effects usually belong inside [event handlers.](/learn/responding-to-events)** Event handlers are functions that React runs when you perform some action—for example, when you click a button. Even though event handlers are defined *inside* your component, they don't run *during* rendering! **So event handlers don't need to be pure.**

////
در React، اثرات جانبی معمولاً در داخل کنترل کننده های رویداد قرار دارند. کنترل کننده های رویداد، توابعی هستند که React آنها را هنگام انجام عملیاتی مانند کلیک کردن دکمه اجرا می کند. با این حال، هر چند که کنترل کننده های رویداد در داخل کامپوننت شما تعریف شده اند، در طول رندرینگ اجرا نمی شوند! بنابراین کنترل کننده های رویداد نیازی به خالص بودن ندارند.

If you've exhausted all other options and can't find the right event handler for your side effect, you can still attach it to your returned JSX with a [`useEffect`](/reference/react/useEffect) call in your component. This tells React to execute it later, after rendering, when side effects are allowed. **However, this approach should be your last resort.**

////
اگر تمام گزینه های دیگر را به پایان رسانده و نتوانستید کنترل کننده رویداد مناسب برای اثر جانبی خود پیدا کنید، همچنان می توانید آن را با یک تماس useEffect در کامپوننت خود به JSX بازگشتی خود اضافه کنید. این به React می گوید که آن را بعد از رندرینگ، زمانی که اثرات جانبی مجاز هستند، اجرا کند. با این حال، این رویکرد باید آخرین راه حل شما باشد.

When possible, try to express your logic with rendering alone. You'll be surprised how far this can take you!

////
وقتی امکان پذیر است، سعی کنید منطق خود را فقط با رندرینگ بیان کنید. شما از اینکه چقدر این کار می تواند به شما کمک کند، شگفت زده خواهید شد!

<DeepDive>

#### Why does React care about purity?(چرا React به خلوص اهمیت می دهد؟) {/*why-does-react-care-about-purity*/}

Writing pure functions takes some habit and discipline. But it also unlocks marvelous opportunities:

* Your components could run in a different environment—for example, on the server! Since they return the same result for the same inputs, one component can serve many user requests.
* You can improve performance by [skipping rendering](/reference/react/memo) components whose inputs have not changed. This is safe because pure functions always return the same results, so they are safe to cache.
* If some data changes in the middle of rendering a deep component tree, React can restart rendering without wasting time to finish the outdated render. Purity makes it safe to stop calculating at any time.

Every new React feature we're building takes advantage of purity. From data fetching to animations to performance, keeping components pure unlocks the power of the React paradigm.

/////
نوشتن توابع خالص مقداری عادت و نظم می خواهد. اما همچنین فرصت های شگفت انگیزی را باز می کند:

کامپوننت های شما می توانند در محیط متفاوتی اجرا شوند—مثلاً روی سرور! از آنجایی که آنها همان نتیجه را برای ورودی های یکسان برمی گردانند، یک مؤلفه می تواند بسیاری از درخواست های کاربر را ارائه دهد.
می‌توانید با رد کردن اجزای رندر که ورودی‌های آن‌ها تغییر نکرده‌اند، عملکرد را بهبود ببخشید. این ایمن است زیرا توابع خالص همیشه نتایج یکسانی را برمی‌گردانند، بنابراین در حافظه پنهان امن هستند.
اگر برخی از داده ها در میانه رندر درخت کامپوننت عمیق تغییر کند، React می تواند بدون اتلاف وقت برای اتمام رندر قدیمی، رندر را مجدداً راه اندازی کند. خلوص باعث می‌شود که هر زمان که بخواهید محاسبات را متوقف کنید.
هر ویژگی React جدیدی که می سازیم از خلوص بهره می برد. از واکشی داده‌ها گرفته تا انیمیشن‌ها و عملکرد، خالص نگه‌داشتن اجزا، قدرت پارادایم React را باز می‌کند.

</DeepDive>

<Recap>

* A component must be pure, meaning:
  * **It minds its own business.** It should not change any objects or variables that existed before rendering.
  * **Same inputs, same output.** Given the same inputs, a component should always return the same JSX. 
* Rendering can happen at any time, so components should not depend on each others' rendering sequence.
* You should not mutate any of the inputs that your components use for rendering. That includes props, state, and context. To update the screen, ["set" state](/learn/state-a-components-memory) instead of mutating preexisting objects.
* Strive to express your component's logic in the JSX you return. When you need to "change things", you'll usually want to do it in an event handler. As a last resort, you can `useEffect`.
* Writing pure functions takes a bit of practice, but it unlocks the power of React's paradigm.

/////
خلاصه
یک جزء باید خالص باشد، به این معنی:
به کار خودش فکر می کند. نباید هیچ شی یا متغیری را که قبل از رندر وجود داشت تغییر دهد.
همان ورودی ها، همان خروجی ها. با توجه به ورودی های یکسان، یک جزء باید همیشه همان JSX را برگرداند.
رندر می تواند در هر زمانی اتفاق بیفتد، بنابراین کامپوننت ها نباید به دنباله رندر یکدیگر بستگی داشته باشند.
شما نباید هیچ یک از ورودی هایی را که اجزای شما برای رندر استفاده می کنند جهش دهید. که شامل موارد، حالت و زمینه است. برای به روز رسانی صفحه، به جای جهش اشیاء موجود، حالت "set" را انجام دهید.
سعی کنید منطق مؤلفه خود را در JSX که برمی‌گردانید بیان کنید. وقتی نیاز به "تغییر چیزها" دارید، معمولاً می خواهید این کار را در یک کنترل کننده رویداد انجام دهید. به عنوان آخرین راه حل، می توانید از Effect استفاده کنید.
نوشتن توابع خالص کمی تمرین می خواهد، اما قدرت پارادایم React را باز می کند.

</Recap>


  
<Challenges>

#### Fix a broken clock {/*fix-a-broken-clock*/}

This component tries to set the `<h1>`'s CSS class to `"night"` during the time from midnight to six hours in the morning, and `"day"` at all other times. However, it doesn't work. Can you fix this component?

You can verify whether your solution works by temporarily changing the computer's timezone. When the current time is between midnight and six in the morning, the clock should have inverted colors!

<Hint>

Rendering is a *calculation*, it shouldn't try to "do" things. Can you express the same idea differently?

</Hint>

<Sandpack>

```js Clock.js active
export default function Clock({ time }) {
  let hours = time.getHours();
  if (hours >= 0 && hours <= 6) {
    document.getElementById('time').className = 'night';
  } else {
    document.getElementById('time').className = 'day';
  }
  return (
    <h1 id="time">
      {time.toLocaleTimeString()}
    </h1>
  );
}
```

```js App.js hidden
import { useState, useEffect } from 'react';
import Clock from './Clock.js';

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}

export default function App() {
  const time = useTime();
  return (
    <Clock time={time} />
  );
}
```

```css
body > * {
  width: 100%;
  height: 100%;
}
.day {
  background: #fff;
  color: #222;
}
.night {
  background: #222;
  color: #fff;
}
```

</Sandpack>

<Solution>

You can fix this component by calculating the `className` and including it in the render output:

<Sandpack>

```js Clock.js active
export default function Clock({ time }) {
  let hours = time.getHours();
  let className;
  if (hours >= 0 && hours <= 6) {
    className = 'night';
  } else {
    className = 'day';
  }
  return (
    <h1 className={className}>
      {time.toLocaleTimeString()}
    </h1>
  );
}
```

```js App.js hidden
import { useState, useEffect } from 'react';
import Clock from './Clock.js';

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}

export default function App() {
  const time = useTime();
  return (
    <Clock time={time} />
  );
}
```

```css
body > * {
  width: 100%;
  height: 100%;
}
.day {
  background: #fff;
  color: #222;
}
.night {
  background: #222;
  color: #fff;
}
```

</Sandpack>

In this example, the side effect (modifying the DOM) was not necessary at all. You only needed to return JSX.

</Solution>

#### Fix a broken profile {/*fix-a-broken-profile*/}

Two `Profile` components are rendered side by side with different data. Press "Collapse" on the first profile, and then "Expand" it. You'll notice that both profiles now show the same person. This is a bug.

Find the cause of the bug and fix it.

<Hint>

The buggy code is in `Profile.js`. Make sure you read it all from top to bottom!

</Hint>

<Sandpack>

```js Profile.js
import Panel from './Panel.js';
import { getImageUrl } from './utils.js';

let currentPerson;

export default function Profile({ person }) {
  currentPerson = person;
  return (
    <Panel>
      <Header />
      <Avatar />
    </Panel>
  )
}

function Header() {
  return <h1>{currentPerson.name}</h1>;
}

function Avatar() {
  return (
    <img
      className="avatar"
      src={getImageUrl(currentPerson)}
      alt={currentPerson.name}
      width={50}
      height={50}
    />
  );
}
```

```js Panel.js hidden
import { useState } from 'react';

export default function Panel({ children }) {
  const [open, setOpen] = useState(true);
  return (
    <section className="panel">
      <button onClick={() => setOpen(!open)}>
        {open ? 'Collapse' : 'Expand'}
      </button>
      {open && children}
    </section>
  );
}
```

```js App.js
import Profile from './Profile.js';

export default function App() {
  return (
    <>
      <Profile person={{
        imageId: 'lrWQx8l',
        name: 'Subrahmanyan Chandrasekhar',
      }} />
      <Profile person={{
        imageId: 'MK3eW3A',
        name: 'Creola Katherine Johnson',
      }} />
    </>
  )
}
```

```js utils.js hidden
export function getImageUrl(person, size = 's') {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; }
.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
  width: 200px;
}
h1 { margin: 5px; font-size: 18px; }
```

</Sandpack>

<Solution>

The problem is that the `Profile` component writes to a preexisting variable called `currentPerson`, and the `Header` and `Avatar` components read from it. This makes *all three of them* impure and difficult to predict.

To fix the bug, remove the `currentPerson` variable. Instead, pass all information from `Profile` to `Header` and `Avatar` via props. You'll need to add a `person` prop to both components and pass it all the way down.

<Sandpack>

```js Profile.js active
import Panel from './Panel.js';
import { getImageUrl } from './utils.js';

export default function Profile({ person }) {
  return (
    <Panel>
      <Header person={person} />
      <Avatar person={person} />
    </Panel>
  )
}

function Header({ person }) {
  return <h1>{person.name}</h1>;
}

function Avatar({ person }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={50}
      height={50}
    />
  );
}
```

```js Panel.js hidden
import { useState } from 'react';

export default function Panel({ children }) {
  const [open, setOpen] = useState(true);
  return (
    <section className="panel">
      <button onClick={() => setOpen(!open)}>
        {open ? 'Collapse' : 'Expand'}
      </button>
      {open && children}
    </section>
  );
}
```

```js App.js
import Profile from './Profile.js';

export default function App() {
  return (
    <>
      <Profile person={{
        imageId: 'lrWQx8l',
        name: 'Subrahmanyan Chandrasekhar',
      }} />
      <Profile person={{
        imageId: 'MK3eW3A',
        name: 'Creola Katherine Johnson',
      }} />
    </>
  );
}
```

```js utils.js hidden
export function getImageUrl(person, size = 's') {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; }
.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
  width: 200px;
}
h1 { margin: 5px; font-size: 18px; }
```

</Sandpack>

Remember that React does not guarantee that component functions will execute in any particular order, so you can't communicate between them by setting variables. All communication must happen through props.

</Solution>

#### Fix a broken story tray {/*fix-a-broken-story-tray*/}

The CEO of your company is asking you to add "stories" to your online clock app, and you can't say no. You've written a `StoryTray` component that accepts a list of `stories`, followed by a "Create Story" placeholder.

You implemented the "Create Story" placeholder by pushing one more fake story at the end of the `stories` array that you receive as a prop. But for some reason, "Create Story" appears more than once. Fix the issue.

<Sandpack>

```js StoryTray.js active
export default function StoryTray({ stories }) {
  stories.push({
    id: 'create',
    label: 'Create Story'
  });

  return (
    <ul>
      {stories.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```js App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState([...initialStories])
  let time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

</Sandpack>

<Solution>

Notice how whenever the clock updates, "Create Story" is added *twice*. This serves as a hint that we have a mutation during rendering--Strict Mode calls components twice to make these issues more noticeable.

`StoryTray` function is not pure. By calling `push` on the received `stories` array (a prop!), it is mutating an object that was created *before* `StoryTray` started rendering. This makes it buggy and very difficult to predict.

The simplest fix is to not touch the array at all, and render "Create Story" separately:

<Sandpack>

```js StoryTray.js active
export default function StoryTray({ stories }) {
  return (
    <ul>
      {stories.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
      <li>Create Story</li>
    </ul>
  );
}
```

```js App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState([...initialStories])
  let time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Alternatively, you could create a _new_ array (by copying the existing one) before you push an item into it:

<Sandpack>

```js StoryTray.js active
export default function StoryTray({ stories }) {
  // Copy the array!
  let storiesToDisplay = stories.slice();

  // Does not affect the original array:
  storiesToDisplay.push({
    id: 'create',
    label: 'Create Story'
  });

  return (
    <ul>
      {storiesToDisplay.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```js App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState([...initialStories])
  let time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

This keeps your mutation local and your rendering function pure. However, you still need to be careful: for example, if you tried to change any of the array's existing items, you'd have to clone those items too.

It is useful to remember which operations on arrays mutate them, and which don't. For example, `push`, `pop`, `reverse`, and `sort` will mutate the original array, but `slice`, `filter`, and `map` will create a new one.

</Solution>

</Challenges>
