---
title: Conditional Rendering
---

<Intro>

Your components will often need to display different things depending on different conditions. In React, you can conditionally render JSX using JavaScript syntax like `if` statements, `&&`, and `? :` operators.
////
اجزای شما معمولاً بسته به شرایط مختلف نیاز به نمایش چیزهای مختلفی دارند. در React، می‌توانید JSX را با استفاده از نحو جاوا اسکریپت مانند دستورات «if»، «&&» و «به صورت مشروط رندر کنید؟ :` اپراتورها
////
کامپوننت‌های شما بسیار احتمالاً باید چیزهای مختلفی را بر اساس شرایط مختلف نمایش دهند. در React، می‌توانید با استفاده از نحو JavaScript مانند if statements، && و ? : operators، JSX را به صورت شرطی رندر کنید1

</Intro>

<YouWillLearn>

* How to return different JSX depending on a condition
* How to conditionally include or exclude a piece of JSX
* Common conditional syntax shortcuts you’ll encounter in React codebases

////
* نحوه برگرداندن JSX های مختلف بسته به شرایط
* نحوه گنجاندن یا حذف مشروط یک قطعه از JSX
* میانبرهای نحوی مشروط معمولی که در پایگاه های کد React با آنها مواجه خواهید شد
////
لطفاً متن انگلیسی زیر را به فارسی ترجمه کنید:
* چگونه JSX متفاوتی را بر اساس یک شرط بازگردانی کنیم
* چگونه یک قطعه از JSX را به صورت شرطی شامل یا حذف کنیم
* میانبرهای نحو شرطی مشترکی که در کدهای React می‌بینید ¹²³

</YouWillLearn>

## Conditionally returning JSX {/*conditionally-returning-jsx*/}

Let’s say you have a `PackingList` component rendering several `Item`s, which can be marked as packed or not:
////
فرض کنید یک مؤلفه «PackingList» دارید که چندین «آیتم» را ارائه می‌کند، که می‌توان آن‌ها را به‌عنوان بسته‌شده یا غیر بسته علامت‌گذاری کرد:
////
بیایید بگوییم که شما یک کامپوننت PackingList دارید که چندین Item را رندر می کند، که می توانند به عنوان بسته شده یا نشده علامت گذاری شوند.

<Sandpack>

```js
function Item({ name, isPacked }) {
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Notice that some of the `Item` components have their `isPacked` prop set to `true` instead of `false`. You want to add a checkmark (✔) to packed items if `isPacked={true}`.

You can write this as an [`if`/`else` statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) like so:
////
توجه داشته باشید که برخی از مؤلفه‌های «Item» به جای «نادرست» روی `«isPacked»` روی `«true»` تنظیم شده‌اند. اگر `«isPacked={true}»` باشد، می‌خواهید یک علامت (✔) به موارد بسته‌بندی شده اضافه کنید.

می توانید این را به عنوان یک عبارت [`if`/`else`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) بنویسید:
////
توجه کنید که برخی از کامپوننت های Item دارای خاصیت isPacked با مقدار true هستند به جای false. شما می خواهید به موارد بسته شده علامت تیک (✔) اضافه کنید اگر isPacked={true}.

شما می توانید این را به صورت یک if/else statement مانند زیر بنویسید:

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

If the `isPacked` prop is `true`, this code **returns a different JSX tree.** With this change, some of the items get a checkmark at the end:
////
اگر prop isPacked درست باشد، این کد درخت JSX متفاوتی را برمی گرداند. با این تغییر، برخی از موارد در انتها علامت چک می گیرند:
////
اگر خاصیت isPacked برابر با true باشد، این کد یک درخت JSX متفاوت را برمی گرداند. با این تغییر، برخی از موارد در انتها با یک علامت تیک مشخص می شوند.

<Sandpack>

```js
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="item">{name} ✔</li>;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Try editing what gets returned in either case, and see how the result changes!

Notice how you're creating branching logic with JavaScript's `if` and `return` statements. In React, control flow (like conditions) is handled by JavaScript.
////
سعی کنید آنچه را که در هر صورت بازگردانده می شود ویرایش کنید و ببینید نتیجه چگونه تغییر می کند!

به نحوه ایجاد منطق انشعاب با دستورات `«if»` و `«return» `جاوا اسکریپت توجه کنید. در React، جریان کنترل (مانند شرایط) توسط جاوا اسکریپت مدیریت می شود.
////
این متن به فارسی ترجمه شده است: سعی کنید در هر دو حالت، آنچه برگشت داده می شود را ویرایش کنید و ببینید چگونه نتیجه تغییر می کند!

توجه کنید که با استفاده از if و return در جاوااسکریپت، منطق شاخه دار را ایجاد می کنید. در React، جریان کنترل (مانند شرایط) توسط جاوااسکریپت کنترل می شود.

### Conditionally returning nothing with `null`(Conditionally returning هیچ چیزی را با "null" بر نمی گرداند) {/*conditionally-returning-nothing-with-null*/}

In some situations, you won't want to render anything at all. For example, say you don't want to show packed items at all. A component must return something. In this case, you can return `null`:
////
در برخی شرایط، اصلاً نمی خواهید چیزی را رندر کنید. به عنوان مثال، بگویید که اصلاً نمی خواهید موارد بسته بندی شده را نشان دهید. یک جزء باید چیزی را برگرداند. در این صورت می توانید null را برگردانید:
////
در برخی موارد، شما نمی خواهید هیچ چیزی را رندر کنید. به عنوان مثال، بگویید که به هیچ وجه نمی خواهید موارد بسته شده را نشان دهید. یک کامپوننت باید چیزی را برگرداند. در این مورد، می توانید null را برگردانید:

```js
if (isPacked) {
  return null;
}
return <li className="item">{name}</li>;
```

If `isPacked` is true, the component will return nothing, `null`. Otherwise, it will return JSX to render.
////
اگر «isPacked» درست باشد، کامپوننت چیزی «null» را بر نمی‌گرداند. در غیر این صورت، JSX را برای رندر برمی گرداند.
////
اگر خاصیت isPacked برابر با true باشد، کامپوننت هیچ چیز، null، برنمی گرداند. در غیر این صورت، JSX را برای رندر کردن برمی گرداند.

<Sandpack>

```js
function Item({ name, isPacked }) {
  if (isPacked) {
    return null;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

In practice, returning `null` from a component isn't common because it might surprise a developer trying to render it. More often, you would conditionally include or exclude the component in the parent component's JSX. Here's how to do that!
////
در عمل، برگرداندن «null» از یک مؤلفه معمول نیست، زیرا ممکن است توسعه‌دهنده‌ای را که تلاش می‌کند آن را ارائه کند، شگفت‌زده کند. بیشتر اوقات، شما به صورت مشروط کامپوننت را در JSX جزء والد قرار می دهید یا حذف می کنید. در اینجا نحوه انجام این کار آمده است!
////
در عمل، برگرداندن null از یک کامپوننت رایج نیست زیرا ممکن است برای یک توسعه دهنده که سعی در رندر کردن آن دارد، شگفت انگیز باشد. بیشتر، شما می توانید کامپوننت را به صورت شرطی در JSX کامپوننت والد شامل یا مستثنی کنید. روش انجام این کار به شرح زیر است:

## Conditionally including JSX {/*conditionally-including-jsx*/}

In the previous example, you controlled which (if any!) JSX tree would be returned by the component. You may already have noticed some duplication in the render output:
////
در مثال قبلی، شما کنترل کردید که کدام درخت JSX (در صورت وجود!) توسط کامپوننت بازگردانده شود. ممکن است قبلاً متوجه مقداری تکراری در خروجی رندر شده باشید:
////
در مثال قبل، شما کنترل کردید که درخت JSX کدام (اگر هیچکدام!) برگشت داده شود. شاید شما در خروجی رندر تکراری بودن برخی از موارد را متوجه شده باشید:

```js
<li className="item">{name} ✔</li>
```

is very similar to

```js
<li className="item">{name}</li>
```

Both of the conditional branches return `<li className="item">...</li>`:
////
هر دو شاخه شرطی `«<li className="item">...</li>` را برمی‌گردانند:
////
هر دو شاخه شرطی بازگشت `<li className="item">...</li>` را برمی گردانند.

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

While this duplication isn't harmful, it could make your code harder to maintain. What if you want to change the `className`? You'd have to do it in two places in your code! In such a situation, you could conditionally include a little JSX to make your code more [DRY.](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)
////
اگرچه این تکرار مضر نیست، اما می‌تواند حفظ کد شما را سخت‌تر کند. اگر بخواهید className را تغییر دهید چه؟ شما باید این کار را در دو مکان از کد خود انجام دهید! در چنین شرایطی، می‌توانید به صورت مشروط کمی JSX اضافه کنید تا کد شما خشک‌تر شود.
////
این متن به فارسی ترجمه شده است: این تکرار، ضرری ندارد، اما ممکن است کد شما را سخت تر برای نگهداری کنید. اگر می خواهید className را تغییر دهید، باید آن را در دو مکان در کد خود انجام دهید! در چنین موقعیتی، می توانید به صورت شرطی یک کم JSX را اضافه کنید تا کد خود را بیشتر DRY کنید.

### Conditional (ternary) operator (`? :`) {/*conditional-ternary-operator--*/}

JavaScript has a compact syntax for writing a conditional expression -- the [conditional operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) or "ternary operator".

Instead of this:
////
جاوا اسکریپت یک دستور فشرده برای نوشتن یک عبارت شرطی دارد - عملگر شرطی یا "اپراتور سه تایی".

به جای این:
////
“جاوااسکریپت” یک دستور زبان برای نوشتن عبارت شرطی کوچک است - اپراتور شرطی یا “اپراتور سه گانه”.

به جای این:

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

You can write this:
////
می توانید این را بنویسید:

```js
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
);
```

You can read it as *"if `isPacked` is true, then (`?`) render `name + ' ✔'`, otherwise (`:`) render `name`"*.
////
شما می توانید آن را به صورت "اگر isPacked درست است، پس (؟) نام + '✔' را رندر کنید، در غیر این صورت (:) نام را رندر کنید.
////
این متن به فارسی ترجمه شده است: شما می توانید آن را به عنوان “اگر isPacked برابر با true باشد، آنگاه (?) نام + ’ ✔’ را رندر کنید، در غیر این صورت (:) نام را رندر کنید” بخوانید.

<DeepDive>

#### Are these two examples fully equivalent?(آیا این دو مثال کاملاً برابر هستند؟) {/*are-these-two-examples-fully-equivalent*/}

If you're coming from an object-oriented programming background, you might assume that the two examples above are subtly different because one of them may create two different "instances" of `<li>`. But JSX elements aren't "instances" because they don't hold any internal state and aren't real DOM nodes. They're lightweight descriptions, like blueprints. So these two examples, in fact, *are* completely equivalent. [Preserving and Resetting State](/learn/preserving-and-resetting-state) goes into detail about how this works.
////
اگر از یک پس‌زمینه برنامه‌نویسی شی‌گرا می‌آیید، ممکن است فرض کنید که دو مثال بالا به طور ماهرانه‌ای متفاوت هستند، زیرا یکی از آنها ممکن است دو «نمونه» متفاوت از `<li>` ایجاد کند. اما عناصر JSX "نمونه" نیستند زیرا هیچ حالت داخلی ندارند و گره های DOM واقعی نیستند. آنها توصیف های سبک وزن هستند، مانند نقشه ها. پس این دو مثال در واقع کاملاً معادل هستند. حفظ و تنظیم مجدد وضعیت به جزئیات در مورد نحوه کار این می‌پردازد.
////
اگر از پس زمینه برنامه نویسی شی گرا می‌آیید، ممکن است فرض کنید که دو نمونه فوق به دلیل اینکه یکی از آن‌ها ممکن است دو “نمونه” متفاوت از `<li>` ایجاد کند، به طور پنهانی متفاوت هستند. اما عناصر JSX “نمونه” نیستند زیرا هیچ حالت داخلی را نگه نمی‌دارند و گره‌های DOM واقعی نیستند. آن‌ها شرح‌های سبک هستند، مانند طرح‌های معماری. بنابراین، این دو نمونه در واقع کاملاً معادل هستند. حفظ و بازنشانی حالت به جزئیات اینکه چگونه این کار می‌کند، می‌پردازد.

</DeepDive>

Now let's say you want to wrap the completed item's text into another HTML tag, like `<del>` to strike it out. You can add even more newlines and parentheses so that it's easier to nest more JSX in each of the cases:
////
حال فرض کنید می‌خواهید متن مورد تکمیل‌شده را در یک تگ HTML دیگری مانند `<del>` بپیچید تا آن را حذف کنید. می‌توانید حتی خطوط جدید و پرانتز بیشتری اضافه کنید تا در هر یک از موارد، تودرتو کردن JSX بیشتری آسان‌تر شود:
////
حال فرض کنید می‌خواهید متن آیتم تکمیل شده را در برچسب HTML دیگری مانند `<del>` برای خط خوردن آن قرار دهید. می‌توانید حتی خطوط جدید و پرانتزهای بیشتری اضافه کنید تا در هر یک از موارد، جایگاه‌های JSX بیشتری را به راحتی تو در تو کنید.

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {isPacked ? (
        <del>
          {name + ' ✔'}
        </del>
      ) : (
        name
      )}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

This style works well for simple conditions, but use it in moderation. If your components get messy with too much nested conditional markup, consider extracting child components to clean things up. In React, markup is a part of your code, so you can use tools like variables and functions to tidy up complex expressions.
////
این سبک برای شرایط ساده به خوبی کار می کند، اما از آن در حد اعتدال استفاده کنید. اگر کامپوننت‌های شما با نشانه‌گذاری‌های شرطی بیش از حد تودرتو آشفته می‌شوند، برای تمیز کردن موارد، اجزای فرزند را استخراج کنید. در React، نشانه گذاری بخشی از کد شما است، بنابراین می توانید از ابزارهایی مانند متغیرها و توابع برای مرتب کردن عبارات پیچیده استفاده کنید.
////
این سبک برای شرایط ساده به خوبی کار می‌کند، اما در استفاده از آن معتدل باشید. اگر کامپوننت‌های شما با نشانه‌گذاری شرطی تو در تو زیادی پیچیده شده‌اند، در نظر داشته باشید که اجزای فرزند را برای پاک کردن کارها استخراج کنید. در React، نشانه‌گذاری بخشی از کد شماست، بنابراین می‌توانید از ابزارهایی مانند متغیرها و توابع برای تمیز کردن عبارات پیچیده استفاده کنید.

### Logical AND operator (`&&`) {/*logical-and-operator-*/}

Another common shortcut you'll encounter is the [JavaScript logical AND (`&&`) operator.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#:~:text=The%20logical%20AND%20(%20%26%26%20)%20operator,it%20returns%20a%20Boolean%20value.) Inside React components, it often comes up when you want to render some JSX when the condition is true, **or render nothing otherwise.** With `&&`, you could conditionally render the checkmark only if `isPacked` is `true`:
////
میانبر رایج دیگری که با آن مواجه خواهید شد، عملگر AND (&&) منطقی جاوا اسکریپت است. در داخل کامپوننت‌های React، اغلب زمانی که می‌خواهید برخی از JSX را در زمانی که شرط درست است، رندر کنید یا هیچ چیز دیگری را رندر کنید، ظاهر می‌شود. با &&، فقط در صورتی که isPacked درست باشد، می توانید علامت را به صورت مشروط ارائه دهید:
////
یکی از میانبرهای معمول دیگری که با آن مواجه می‌شوید، اپراتور AND (&&) منطقی JavaScript است. درون کامپوننت‌های React، آن زمانی که می‌خواهید برخی از JSX را در صورت برقرار بودن شرط رندر کنید یا در غیر این صورت هیچ چیزی را رندر نکنید، اغلب پیش می‌آید. با &&، می‌توانید فقط در صورت برقرار بودن isPacked، علامت تیک را به صورت شرطی رندر کنید.

```js
return (
  <li className="item">
    {name} {isPacked && '✔'}
  </li>
);
```

You can read this as *"if `isPacked`, then (`&&`) render the checkmark, otherwise, render nothing"*.

Here it is in action:
////
شما می توانید این را به عنوان "اگر isPacked است، سپس (&&) علامت چک را ارائه دهید، در غیر این صورت، هیچ چیز را رندر کنید" بخوانید.

اینجا آن در حال عمل هست:
////
می‌توانید این را به این صورت بخوانید: “اگر isPacked برقرار باشد، آنگاه (&&) علامت تیک را رندر کنید، در غیر این صورت، هیچ چیزی را رندر نکنید”.

اینجا آن در حال عمل هست:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✔'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

A [JavaScript && expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) returns the value of its right side (in our case, the checkmark) if the left side (our condition) is `true`. But if the condition is `false`, the whole expression becomes `false`. React considers `false` as a "hole" in the JSX tree, just like `null` or `undefined`, and doesn't render anything in its place.
////
یک عبارت && جاوا اسکریپت مقدار سمت راست خود (در مورد ما علامت تیک) را در صورتی که سمت چپ (شرط ما) درست باشد، برمی‌گرداند. اما اگر شرط نادرست باشد، کل عبارت نادرست می شود. React false را به عنوان یک "سوراخ" در درخت JSX، درست مانند null یا undefined در نظر می گیرد و هیچ چیزی را در جای خود رندر نمی کند.
////
یک عبارت && جاوا اسکریپت، اگر طرف راست آن (در مورد ما، علامت تیک) اگر طرف چپ (شرط ما) درست باشد، مقدار آن را برمی‌گرداند. اما اگر شرط نادرست باشد، کل عبارت نادرست می‌شود. React false را به عنوان یک “حفره” در درخت JSX در نظر می‌گیرد، همانند null یا undefined، و در محل آن چیزی را رندر نمی‌کند.

<Pitfall>

**Don't put numbers on the left side of `&&`.**
اعداد را در سمت چپ && قرار ندهید.

To test the condition, JavaScript converts the left side to a boolean automatically. However, if the left side is `0`, then the whole expression gets that value (`0`), and React will happily render `0` rather than nothing.
////
برای آزمایش شرایط، جاوا اسکریپت سمت چپ را به صورت خودکار به یک بولی تبدیل می کند. با این حال، اگر سمت چپ `"0"` باشد، کل عبارت آن مقدار `("0")` را دریافت می کند، و React با خوشحالی به جای هیچ چیز، `"0"` را ارائه می دهد.
////
برای آزمایش شرط، جاوااسکریپت طرف چپ را به صورت خودکار به یک بولین تبدیل می‌کند. با این حال، اگر طرف چپ `0` باشد، کل عبارت آن مقدار `(0)` را دریافت می‌کند و React با خوشحالی `0` را به جای هیچ چیزی رندر می‌کند.

For example, a common mistake is to write code like `messageCount && <p>New messages</p>`. It's easy to assume that it renders nothing when `messageCount` is `0`, but it really renders the `0` itself!
////
به عنوان مثال، یک اشتباه رایج نوشتن کدی مانند `«messageCount &&<p>New messages</p>»` است. به راحتی می توان فرض کرد که وقتی `«messageCount»` `«0»` است، چیزی ارائه نمی دهد، اما واقعاً خود `«0»` را رندر می کند!
////
To fix it, make the left side a boolean: `messageCount > 0 && <p>New messages</p>`.
برای رفع آن، سمت چپ را به صورت منطقی درآورید: `messageCount > 0 && <p>پیام‌های جدید</p>`.

</Pitfall>

### Conditionally assigning JSX to a variable {/*conditionally-assigning-jsx-to-a-variable*/}

When the shortcuts get in the way of writing plain code, try using an `if` statement and a variable. You can reassign variables defined with [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), so start by providing the default content you want to display, the name:
////
هنگامی که میانبرها مانع نوشتن کد ساده می شوند، سعی کنید از یک دستور if و یک متغیر استفاده کنید. می توانید متغیرهای تعریف شده با let را مجدداً اختصاص دهید، بنابراین با ارائه محتوای پیش فرضی که می خواهید نمایش دهید شروع کنید، نام:
////
وقتی که می‌خواهید کد ساده‌ای بنویسید، اگر می‌خواهید از میانبرها استفاده کنید، با استفاده از یک عبارت if و یک متغیر امتحان کنید. می‌توانید متغیرهایی که با let تعریف شده‌اند را دوباره اختصاص دهید، بنابراین با ارائه محتوای پیش‌فرضی که می‌خواهید نمایش دهید، نام را شروع کنید:

```js
let itemContent = name;
```

Use an `if` statement to reassign a JSX expression to `itemContent` if `isPacked` is `true`:
////
اگر «isPacked» «true» باشد، از عبارت «if» برای تخصیص مجدد عبارت JSX به «itemContent» استفاده کنید:
////
اگر isPacked برابر با true باشد، از یک عبارت if برای اختصاص دوباره یک عبارت JSX به itemContent استفاده کنید.

```js
if (isPacked) {
  itemContent = name + " ✔";
}
```

[Curly braces open the "window into JavaScript".](/learn/javascript-in-jsx-with-curly-braces#using-curly-braces-a-window-into-the-javascript-world) Embed the variable with curly braces in the returned JSX tree, nesting the previously calculated expression inside of JSX:
////
بریس های فرفری "پنجره به جاوا اسکریپت" را باز می کنند. متغیر را با پرانتزهای فرفری در درخت JSX برگشتی جاسازی کنید و عبارت محاسبه شده قبلی را در داخل JSX قرار دهید:
////
کروی آکولادها “پنجره‌ای به جاوااسکریپت” را باز می‌کنند. متغیر را با کروی آکولادها در درخت JSX برگردانده، عبارت قبلی محاسبه شده را در JSX جاسازی کنید.

```js
<li className="item">
  {itemContent}
</li>
```

This style is the most verbose, but it's also the most flexible. Here it is in action:
////
این سبک پرحرف ترین است، اما در عین حال انعطاف پذیرترین است. اینجا در عمل است:
////
این سبک، بیشترین حجم را دارد، اما همچنین بیشترین انعطاف‌پذیری را دارد. در ادامه، نحوه کار آن را مشاهده می‌کنید:

<Sandpack>

```js
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = name + " ✔";
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Like before, this works not only for text, but for arbitrary JSX too:
////
مانند قبل، این نه تنها برای متن، بلکه برای JSX دلخواه نیز کار می کند:
////
مانند قبل، این کار نه تنها برای متن، بلکه برای JSX خودسرانه نیز کاربرد دارد.

<Sandpack>

```js
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = (
      <del>
        {name + " ✔"}
      </del>
    );
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

If you're not familiar with JavaScript, this variety of styles might seem overwhelming at first. However, learning them will help you read and write any JavaScript code -- and not just React components! Pick the one you prefer for a start, and then consult this reference again if you forget how the other ones work.
////
اگر با جاوا اسکریپت آشنایی ندارید، این تنوع سبک در ابتدا ممکن است بسیار زیاد به نظر برسد. با این حال، یادگیری آنها به شما کمک می کند تا هر کد جاوا اسکریپت را بخوانید و بنویسید - و نه فقط اجزای React! یکی را که برای شروع ترجیح می دهید انتخاب کنید و اگر فراموش کردید که بقیه چگونه کار می کنند، دوباره با این مرجع مشورت کنید.
////
اگر با جاوااسکریپت آشنایی ندارید، این نوع از سبک‌ها در ابتدا ممکن است شما را به شدت گیج کند. با این حال، یادگیری آنها به شما کمک می‌کند تا هر کد جاوااسکریپتی را بخوانید و بنویسید - و نه فقط اجزای React! برای شروع، یکی را که ترجیح می‌دهید انتخاب کنید، و در صورت فراموشی نحوه کار دیگران، دوباره به این مرجع مراجعه کنید.

<Recap>

* In React, you control branching logic with JavaScript.
* You can return a JSX expression conditionally with an `if` statement.
* You can conditionally save some JSX to a variable and then include it inside other JSX by using the curly braces.
* In JSX, `{cond ? <A /> : <B />}` means *"if `cond`, render `<A />`, otherwise `<B />`"*.
* In JSX, `{cond && <A />}` means *"if `cond`, render `<A />`, otherwise nothing"*.
* The shortcuts are common, but you don't have to use them if you prefer plain `if`.
////
* در React، منطق شاخه‌بندی را با جاوا اسکریپت کنترل می‌کنید.
* می توانید یک عبارت JSX را به صورت شرطی با عبارت «if» برگردانید.
* می توانید به صورت مشروط مقداری JSX را در یک متغیر ذخیره کنید و سپس با استفاده از بریس های فرفری آن را در داخل JSX دیگر قرار دهید.
* در JSX، `{cond ? <A /> : <B />}` به معنای *"اگر "cond" است، `"<A />"` را تبدیل کنید، در غیر این صورت `"<B />"`"*.
* در JSX، `{cond && <A />}` به معنی *"اگر "cond" است، `"<A />"` را رندر کنید، در غیر این صورت هیچی"*.
* میانبرها رایج هستند، اما اگر «اگر» ساده را ترجیح می دهید، نیازی به استفاده از آنها ندارید.
////
* در React، شما با جاوااسکریپت منطق شاخه‌بندی را کنترل می‌کنید.
* شما می‌توانید با یک عبارت `if` یک عبارت JSX را به صورت شرطی برگردانید.
* شما می‌توانید برخی از JSX را به صورت شرطی ذخیره کرده و سپس آن را با استفاده از کروی آکولادها در داخل JSX دیگر قرار دهید.
* در JSX، `{cond ? <A /> : <B />}` به معنای *"اگر `cond`، `<A />` را نمایش دهید، در غیر این صورت `<B />`"* است.
* در JSX، `{cond && <A />}` به معنای *"اگر `cond`، `<A />` را نمایش دهید، در غیر این صورت هیچ چیزی نمایش داده نمی‌شود"* است.
* این میانبرها رایج هستند، اما اگر ترجیح می‌دهید از `if` ساده استفاده کنید، نیازی به استفاده از آنها نیست.

</Recap>



<Challenges>

#### Show an icon for incomplete items with `? :` {/*show-an-icon-for-incomplete-items-with--*/}

Use the conditional operator (`cond ? a : b`) to render a ❌ if `isPacked` isn’t `true`.

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✔'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked ? '✔' : '❌'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

</Solution>

#### Show the item importance with `&&` {/*show-the-item-importance-with-*/}

In this example, each `Item` receives a numerical `importance` prop. Use the `&&` operator to render "_(Importance: X)_" in italics, but only for items that have non-zero importance. Your item list should end up looking like this:

* Space suit _(Importance: 9)_
* Helmet with a golden leaf
* Photo of Tam _(Importance: 6)_

Don't forget to add a space between the two labels!

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          importance={9} 
          name="Space suit" 
        />
        <Item 
          importance={0} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          importance={6} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

This should do the trick:

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
      {importance > 0 && ' '}
      {importance > 0 &&
        <i>(Importance: {importance})</i>
      }
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          importance={9} 
          name="Space suit" 
        />
        <Item 
          importance={0} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          importance={6} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Note that you must write `importance > 0 && ...` rather than `importance && ...` so that if the `importance` is `0`, `0` isn't rendered as the result!

In this solution, two separate conditions are used to insert a space between the name and the importance label. Alternatively, you could use a fragment with a leading space: `importance > 0 && <> <i>...</i></>` or add a space immediately inside the `<i>`:  `importance > 0 && <i> ...</i>`.

</Solution>

#### Refactor a series of `? :` to `if` and variables {/*refactor-a-series-of---to-if-and-variables*/}

This `Drink` component uses a series of `? :` conditions to show different information depending on whether the `name` prop is `"tea"` or `"coffee"`. The problem is that the information about each drink is spread across multiple conditions. Refactor this code to use a single `if` statement instead of three `? :` conditions.

<Sandpack>

```js
function Drink({ name }) {
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{name === 'tea' ? 'leaf' : 'bean'}</dd>
        <dt>Caffeine content</dt>
        <dd>{name === 'tea' ? '15–70 mg/cup' : '80–185 mg/cup'}</dd>
        <dt>Age</dt>
        <dd>{name === 'tea' ? '4,000+ years' : '1,000+ years'}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

Once you've refactored the code to use `if`, do you have further ideas on how to simplify it?

<Solution>

There are multiple ways you could go about this, but here is one starting point:

<Sandpack>

```js
function Drink({ name }) {
  let part, caffeine, age;
  if (name === 'tea') {
    part = 'leaf';
    caffeine = '15–70 mg/cup';
    age = '4,000+ years';
  } else if (name === 'coffee') {
    part = 'bean';
    caffeine = '80–185 mg/cup';
    age = '1,000+ years';
  }
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{part}</dd>
        <dt>Caffeine content</dt>
        <dd>{caffeine}</dd>
        <dt>Age</dt>
        <dd>{age}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

Here the information about each drink is grouped together instead of being spread across multiple conditions. This makes it easier to add more drinks in the future.

Another solution would be to remove the condition altogether by moving the information into objects:

<Sandpack>

```js
const drinks = {
  tea: {
    part: 'leaf',
    caffeine: '15–70 mg/cup',
    age: '4,000+ years'
  },
  coffee: {
    part: 'bean',
    caffeine: '80–185 mg/cup',
    age: '1,000+ years'
  }
};

function Drink({ name }) {
  const info = drinks[name];
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{info.part}</dd>
        <dt>Caffeine content</dt>
        <dd>{info.caffeine}</dd>
        <dt>Age</dt>
        <dd>{info.age}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
