---
title: Writing Markup with JSX(نوشتن نشانه گذاری با JSX)
---

<Intro>

*JSX* is a syntax extension for JavaScript that lets you write HTML-like markup inside a JavaScript file. Although there are other ways to write components, most React developers prefer the conciseness of JSX, and most codebases use it.
////
*JSX* یک پسوند نحوی برای جاوا اسکریپت است که به شما امکان می‌دهد نشانه‌گذاری HTML مانند درون یک فایل جاوا اسکریپت بنویسید. اگرچه راه‌های دیگری برای نوشتن کامپوننت‌ها وجود دارد، اما اکثر توسعه‌دهندگان React مختصر بودن JSX را ترجیح می‌دهند و اکثر پایگاه‌های کد از آن استفاده می‌کنند.
</Intro>

<YouWillLearn>

* Why React mixes markup with rendering logic
* How JSX is different from HTML
* How to display information with JSX

////
* چرا React نشانه گذاری را با منطق رندر ترکیب می کند
* تفاوت JSX با HTML
* نحوه نمایش اطلاعات صفحه با JSX

</YouWillLearn>

## JSX: Putting markup into JavaScript(قرار دادن(تفسیرکردن) نشانه گذاری در جاوا اسکریپت) {/*jsx-putting-markup-into-javascript*/}

The Web has been built on HTML, CSS, and JavaScript. For many years, web developers kept content in HTML, design in CSS, and logic in JavaScript—often in separate files! Content was marked up inside HTML while the page's logic lived separately in JavaScript:
////
وب بر پایه HTML، CSS و JavaScript ساخته شده است. برای سال‌ها، توسعه‌دهندگان وب محتوا را در HTML، طراحی را در CSS و منطق را در JavaScript نگه داشتند - اغلب در فایل‌های جداگانه! محتوا در داخل HTML علامت‌گذاری شده بود در حالی که منطق صفحه در JavaScript جداگانه قرار داشت1

<DiagramGroup>

<Diagram name="writing_jsx_html" height={237} width={325} alt="HTML markup with purple background and a div with two child tags: p and form. ">

HTML

</Diagram>

<Diagram name="writing_jsx_js" height={237} width={325} alt="Three JavaScript handlers with yellow background: onSubmit, onLogin, and onClick.">

JavaScript

</Diagram>

</DiagramGroup>

But as the Web became more interactive, logic increasingly determined content. JavaScript was in charge of the HTML! This is why **in React, rendering logic and markup live together in the same place—components.**
////
اما با توسعه بیشتر وب، منطق به طور فزاینده‌ای محتوا را تعیین کرد. JavaScript مسئول HTML بود! به همین دلیل در React، منطق رندرینگ و نشانه‌گذاری در یک مکان قرار دارند - کامپوننت‌ها1.

<DiagramGroup>

<Diagram name="writing_jsx_sidebar" height={330} width={325} alt="React component with HTML and JavaScript from previous examples mixed. Function name is Sidebar which calls the function isLoggedIn, highlighted in yellow. Nested inside the function highlighted in purple is the p tag from before, and a Form tag referencing the component shown in the next diagram.">

`Sidebar.js` React component

</Diagram>

<Diagram name="writing_jsx_form" height={330} width={325} alt="React component with HTML and JavaScript from previous examples mixed. Function name is Form containing two handlers onClick and onSubmit highlighted in yellow. Following the handlers is HTML highlighted in purple. The HTML contains a form element with a nested input element, each with an onClick prop.">

`Form.js` React component

</Diagram>

</DiagramGroup>

Keeping a button's rendering logic and markup together ensures that they stay in sync with each other on every edit. Conversely, details that are unrelated, such as the button's markup and a sidebar's markup, are isolated from each other, making it safer to change either of them on their own.

Each React component is a JavaScript function that may contain some markup that React renders into the browser. React components use a syntax extension called JSX to represent that markup. JSX looks a lot like HTML, but it is a bit stricter and can display dynamic information. The best way to understand this is to convert some HTML markup to JSX markup.
////
نگه داشتن منطق و نشانه‌گذاری رندرینگ دکمه در کنار هم، اطمینان می‌دهد که هر بار که ویرایشی انجام می‌شود، آن‌ها با یکدیگر هماهنگ می‌شوند. به عبارت دیگر، جزئیاتی که با یکدیگر ارتباطی ندارند، مانند نشانه‌گذاری دکمه و نشانه‌گذاری نوار کناری، از یکدیگر جدا شده‌اند و تغییر هر کدام از آن‌ها به تنهایی، ایمن‌تر است.

هر کامپوننت React یک تابع JavaScript است که ممکن است شامل برخی نشانه‌گذاری باشد که React آن‌ها را در مرورگر رندر می‌کند. کامپوننت‌های React از یک افزونه نحوی به نام JSX برای نمایش آن نشانه‌گذاری استفاده می‌کنند. JSX شبیه به HTML است، اما کمی سختگیرانه‌تر است و می‌تواند اطلاعات پویا را نمایش دهد. بهترین راه برای درک این موضوع، تبدیل برخی از نشانه‌گذاری HTML به نشانه‌گذاری JSX است.

<Note>

JSX and React are two separate things. They're often used together, but you *can* [use them independently](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html#whats-a-jsx-transform) of each other. JSX is a syntax extension, while React is a JavaScript library.
////
JSX و React دو مورد جداگانه هستند. آن‌ها اغلب با هم استفاده می‌شوند، اما می‌توانید از هر کدام به طور مستقل استفاده کنید. JSX یک افزونه و یک سینتکس است، در حالی که React یک کتابخانه JavaScript است1.
</Note>

## Converting HTML to JSX {/*converting-html-to-jsx*/}

Suppose that you have some (perfectly valid) HTML:
////
فرض کنید مقداری HTML (کاملا معتبر) دارید:

```html
<h1>Hedy Lamarr's Todos</h1>
<img 
  src="https://i.imgur.com/yXOvdOSs.jpg" 
  alt="Hedy Lamarr" 
  class="photo"
>
<ul>
    <li>Invent new traffic lights
    <li>Rehearse a movie scene
    <li>Improve the spectrum technology
</ul>
```

And you want to put it into your component:
////
و می خواهید آن را در کامپوننت خود قرار دهید:

```js
export default function TodoList() {
  return (
    // ???
  )
}
```

If you copy and paste it as is, it will not work:
////
اگر آن را همانطور که هست کپی و پیست کنید کار نمی کند:


<Sandpack>

```js
export default function TodoList() {
  return (
    // This doesn't quite work!
    <h1>Hedy Lamarr's Todos</h1>
    <img 
      src="https://i.imgur.com/yXOvdOSs.jpg" 
      alt="Hedy Lamarr" 
      class="photo"
    >
    <ul>
      <li>Invent new traffic lights
      <li>Rehearse a movie scene
      <li>Improve the spectrum technology
    </ul>
  );
}
```

```css
img { height: 90px }
```

</Sandpack>

This is because JSX is stricter and has a few more rules than HTML! If you read the error messages above, they'll guide you to fix the markup, or you can follow the guide below.
////
این به این دلیل است که JSX سختگیرانه‌تر و چند قانون بیشتری نسبت به HTML دارد! اگر پیام‌های خطا را بالا بخوانید، به شما کمک می‌کنند تا نشانه‌گذاری را تصحیح کنید، یا می‌توانید راهنمای زیر را دنبال کنید1.

<Note>

Most of the time, React's on-screen error messages will help you find where the problem is. Give them a read if you get stuck!
////
بیشتر اوقات، پیام‌های خطای React در صفحه نمایش به شما کمک می‌کنند تا مشکل را پیدا کنید. اگر گیر کردید، آن‌ها را بخوانید!

</Note>

## The Rules of JSX {/*the-rules-of-jsx*/}

### 1. Return a single root element(یک عنصر ریشه و والد رو قرار بدید) {/*1-return-a-single-root-element*/}

To return multiple elements from a component, **wrap them with a single parent tag.**
////
برای برگرداندن چندین عنصر از یک کامپوننت، آنها را با یک تگ والد بپیچید.

به عنوان مثال، می توانید از `«<div>»` استفاده کنید:

```js {1,11}
<div>
  <h1>Hedy Lamarr's Todos</h1>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
  >
  <ul>
    ...
  </ul>
</div>
```


If you don't want to add an extra `<div>` to your markup, you can write `<>` and `</>` instead:
////
اگر نمی‌خواهید `«<div>»` اضافی به نشانه‌گذاری خود اضافه کنید، می‌توانید «<>» و «</>» را به جای آن بنویسید:

```js {1,11}
<>
  <h1>Hedy Lamarr's Todos</h1>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
  >
  <ul>
    ...
  </ul>
</>
```

This empty tag is called a *[Fragment.](/reference/react/Fragment)* Fragments let you group things without leaving any trace in the browser HTML tree.
////
این تگ خالی یک *[Fragment.](/reference/react/Fragment)* نامیده می‌شود.

<DeepDive>

#### Why do multiple JSX tags need to be wrapped?(چرا چندین تگ JSX باید دورشون یک parent پیچیده شود؟) {/*why-do-multiple-jsx-tags-need-to-be-wrapped*/}

JSX looks like HTML, but under the hood it is transformed into plain JavaScript objects. You can't return two objects from a function without wrapping them into an array. This explains why you also can't return two JSX tags without wrapping them into another tag or a Fragment.
////
JSX شبیه به HTML است، اما در زیر سطح آن به شیء‌های ساده JavaScript تبدیل می‌شود. شما نمی‌توانید دو شیء را از یک تابع برگردانید مگر آنکه آن‌ها را در یک آرایه بگذارید. به همین دلیل است که نمی‌توانید دو تگ JSX را بدون قرار دادن آن‌ها در یک تگ دیگر یا Fragment برگردانید1.
</DeepDive>

### 2. Close all the tags(همه تگ ها را ببندید) {/*2-close-all-the-tags*/}

JSX requires tags to be explicitly closed: self-closing tags like `<img>` must become `<img />`, and wrapping tags like `<li>oranges` must be written as `<li>oranges</li>`.
////
JSX نیاز به بسته شدن صریح تگ‌ها دارد: تگ‌های خودبسته مانند `<img>` باید به `<img />` تبدیل شوند و تگ‌های بسته‌کننده مانند `<li>`oranges باید به صورت `<li>`oranges`</li>` نوشته شوند.

این نحوه بسته شدن تصویر و موارد لیستی Hedy Lamarr است:

This is how Hedy Lamarr's image and list items look closed:
////
این است که چگونه تصویر Hedy Lamarr و موارد لیست بسته به نظر می‌رسد123.

```js {2-6,8-10}
<>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
   />
  <ul>
    <li>Invent new traffic lights</li>
    <li>Rehearse a movie scene</li>
    <li>Improve the spectrum technology</li>
  </ul>
</>
```

### 3. camelCase <s>all</s> most of the things!(CamelCase <s>all</s> بیشتر چیز ها) {/*3-camelcase-salls-most-of-the-things*/}

JSX turns into JavaScript and attributes written in JSX become keys of JavaScript objects. In your own components, you will often want to read those attributes into variables. But JavaScript has limitations on variable names. For example, their names can't contain dashes or be reserved words like `class`.

This is why, in React, many HTML and SVG attributes are written in camelCase. For example, instead of `stroke-width` you use `strokeWidth`. Since `class` is a reserved word, in React you write `className` instead, named after the [corresponding DOM property](https://developer.mozilla.org/en-US/docs/Web/API/Element/className):
////
JSX به JavaScript تبدیل می‌شود و ویژگی‌های نوشته شده در JSX به عنوان کلیدهای شیء‌های JavaScript محسوب می‌شوند. در کامپوننت‌های خود، اغلب می‌خواهید این ویژگی‌ها را به متغیرها بخوانید. اما JavaScript محدودیت‌هایی در نام متغیرها دارد. به عنوان مثال، نام آن‌ها نمی‌تواند شامل خط تیره باشد یا کلمات رزرو شده مانند class باشد.

به همین دلیل، در React، بسیاری از ویژگی‌های HTML و SVG به صورت camelCase نوشته می‌شوند. به عنوان مثال، به جای stroke-width از strokeWidth استفاده می‌کنید. از آنجا که class یک کلمه رزرو شده است، در React به جای آن className را می‌نویسید1.

```js {4}
<img 
  src="https://i.imgur.com/yXOvdOSs.jpg" 
  alt="Hedy Lamarr" 
  className="photo"
/>
```

You can [find all these attributes in the list of DOM component props.](/reference/react-dom/components/common) If you get one wrong, don't worry—React will print a message with a possible correction to the [browser console.](https://developer.mozilla.org/docs/Tools/Browser_Console)
////
شما می‌توانید تمامی این ویژگی‌ها را در لیست خصوصیات کامپوننت DOM پیدا کنید. اگر یکی از آن‌ها را اشتباه بنویسید، نگران نباشید - React یک پیام با یک اصلاح ممکن به کنسول مرورگر چاپ می‌کند12 .

<Pitfall>

For historical reasons, [`aria-*`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA) and [`data-*`](https://developer.mozilla.org/docs/Learn/HTML/Howto/Use_data_attributes) attributes are written as in HTML with dashes.
////
به دلایل تاریخی، ویژگی‌های aria-* و data-* مانند HTML با خط تیره نوشته می‌شوند.

</Pitfall>

### Pro-tip: Use a JSX Converter(نکته ی حرفه ای: از مبدل تبدیل کننده به jsx استفاده کنید) {/*pro-tip-use-a-jsx-converter*/}

Converting all these attributes in existing markup can be tedious! We recommend using a [converter](https://transform.tools/html-to-jsx) to translate your existing HTML and SVG to JSX. Converters are very useful in practice, but it's still worth understanding what is going on so that you can comfortably write JSX on your own.
////
تبدیل تمامی این ویژگی‌ها در نشانه‌گذاری موجود می‌تواند خسته‌کننده باشد! ما پیشنهاد می‌دهیم از یک تبدیل‌کننده برای ترجمه HTML و SVG موجود شما به JSX استفاده کنید. تبدیل‌کننده‌ها در عمل بسیار مفید هستند، اما هنوز هم ارزش دانستن آنچه در حال انجام است وجود دارد تا بتوانید به راحتی JSX را به تنهایی بنویسید1.

Here is your final result:
////
در اینجا نتیجه نهایی شما است:

<Sandpack>

```js
export default function TodoList() {
  return (
    <>
      <h1>Hedy Lamarr's Todos</h1>
      <img 
        src="https://i.imgur.com/yXOvdOSs.jpg" 
        alt="Hedy Lamarr" 
        className="photo" 
      />
      <ul>
        <li>Invent new traffic lights</li>
        <li>Rehearse a movie scene</li>
        <li>Improve the spectrum technology</li>
      </ul>
    </>
  );
}
```

```css
img { height: 90px }
```

</Sandpack>

<Recap>

Now you know why JSX exists and how to use it in components:
////
حالا می‌دانید چرا JSX وجود دارد و چگونه می‌توانید از آن در کامپوننت های خود استفاده کنید

* React components group rendering logic together with markup because they are related.
* JSX is similar to HTML, with a few differences. You can use a [converter](https://transform.tools/html-to-jsx) if you need to.
* Error messages will often point you in the right direction to fixing your markup.

////
کامپوننت‌های React، منطق رندرینگ را به همراه نشانه‌گذاری گروه‌بندی می‌کنند زیرا با یکدیگر مرتبط هستند. JSX شبیه به HTML است، با تفاوت‌هایی کم. اگر نیاز داشتید، می‌توانید از یک تبدیل‌کننده استفاده کنید. پیام‌های خطای زیادی به شما کمک می‌کنند تا نشانه‌گذاری خود را تصحیح کنید1234.
</Recap>



<Challenges>

#### Convert some HTML to JSX(تبدیل مقداری html به jsx) {/*convert-some-html-to-jsx*/}

This HTML was pasted into a component, but it's not valid JSX. Fix it:
////
این HTML در یک کامپوننت جایگذاری شده است، اما JSX معتبر نیست. درستش کن:

<Sandpack>

```js
export default function Bio() {
  return (
    <div class="intro">
      <h1>Welcome to my website!</h1>
    </div>
    <p class="summary">
      You can find my thoughts here.
      <br><br>
      <b>And <i>pictures</b></i> of scientists!
    </p>
  );
}
```

```css
.intro {
  background-image: linear-gradient(to left, violet, indigo, blue, green, yellow, orange, red);
  background-clip: text;
  color: transparent;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.summary {
  padding: 20px;
  border: 10px solid gold;
}
```

</Sandpack>

Whether to do it by hand or using the converter is up to you!
////
اینکه آیا این کار را با دست انجام دهید یا با استفاده از مبدل به شما بستگی دارد!

<Solution>

<Sandpack>

```js
export default function Bio() {
  return (
    <div>
      <div className="intro">
        <h1>Welcome to my website!</h1>
      </div>
      <p className="summary">
        You can find my thoughts here.
        <br /><br />
        <b>And <i>pictures</i></b> of scientists!
      </p>
    </div>
  );
}
```

```css
.intro {
  background-image: linear-gradient(to left, violet, indigo, blue, green, yellow, orange, red);
  background-clip: text;
  color: transparent;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.summary {
  padding: 20px;
  border: 10px solid gold;
}
```

</Sandpack>

</Solution>

</Challenges>
