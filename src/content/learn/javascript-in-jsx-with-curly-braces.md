---
title: JavaScript in JSX with Curly Braces(جاوا اسکریپت در JSX با Curly Braces  )
---

<Intro>

JSX lets you write HTML-like markup inside a JavaScript file, keeping rendering logic and content in the same place. Sometimes you will want to add a little JavaScript logic or reference a dynamic property inside that markup. In this situation, you can use curly braces in your JSX to open a window to JavaScript.
////
JSX به شما اجازه می‌دهد که نشانه‌گذاری شبیه به HTML را درون یک فایل JavaScript بنویسید و منطق رندرینگ و محتوا را در یک مکان نگه دارید. گاهی اوقات ممکن است بخواهید یک منطق کمی JavaScript را اضافه کنید یا به یک ویژگی پویا درون آن نشانه‌گذاری ارجاع دهید. در این موقعیت، می‌توانید از پرانتز آکولادی در JSX خود استفاده کنید تا یک پنجره به جاوا اسکریپت باز کنید1234

</Intro>

<YouWillLearn>

* How to pass strings with quotes
* How to reference a JavaScript variable inside JSX with curly braces
* How to call a JavaScript function inside JSX with curly braces
* How to use a JavaScript object inside JSX with curly braces

////
* چگونه رشته‌هایی با نقل قول را انتقال دهیم
* چگونه به یک متغیر جاوا اسکریپت درون JSX با پرانتز آکولادی ارجاع دهیم
* چگونه یک تابع جاوا اسکریپت را درون JSX با پرانتز آکولادی فراخوانی کنیم
* چگونه از یک شیء جاوا اسکریپت درون JSX با پرانتز آکولادی استفاده کنیم12

</YouWillLearn>

## Passing strings with quotes(ارسال رشته با نقل قول) {/*passing-strings-with-quotes*/}

When you want to pass a string attribute to JSX, you put it in single or double quotes:
////
وقتی می‌خواهید یک ویژگی رشته‌ای را به JSX منتقل کنید، آن را در داخل نقل قول تکی یا دوتایی قرار می‌دهید1234.

<Sandpack>

```js
export default function Avatar() {
  return (
    <img
      className="avatar"
      src="https://i.imgur.com/7vQD0fPs.jpg"
      alt="Gregorio Y. Zara"
    />
  );
}
```

```css
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

Here, `"https://i.imgur.com/7vQD0fPs.jpg"` and `"Gregorio Y. Zara"` are being passed as strings.

But what if you want to dynamically specify the `src` or `alt` text? You could **use a value from JavaScript by replacing `"` and `"` with `{` and `}`**:
////
اینجا، `“https://i.imgur.com/7vQD0fPs.jpg”` و `“Gregorio Y. Zara”` به عنوان رشته‌ها منتقل می‌شوند.

اما اگر می‌خواهید متن alt یا src را به صورت پویا مشخص کنید چه؟ می‌توانید از یک مقدار از جاوا اسکریپت با جایگزینی `" و "` با `{ و }` استفاده کنید1234.
<Sandpack>

```js
export default function Avatar() {
  const avatar = 'https://i.imgur.com/7vQD0fPs.jpg';
  const description = 'Gregorio Y. Zara';
  return (
    <img
      className="avatar"
      src={avatar}
      alt={description}
    />
  );
}
```

```css
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

Notice the difference between `className="avatar"`, which specifies an `"avatar"` CSS class name that makes the image round, and `src={avatar}` that reads the value of the JavaScript variable called `avatar`. That's because curly braces let you work with JavaScript right there in your markup!
////
توجه کنید که تفاوت بین `className=“avatar”` که نام کلاس CSS `“avatar”` را مشخص می‌کند و تصویر `src={avatar}` که مقدار متغیر جاوا اسکریپت به نام avatar را خوانده است، چیست. این به این دلیل است که پرانتز آکولادی به شما اجازه می‌دهد که با جاوا اسکریپت درون نشانه‌گذاری خود کار کنید!

## Using curly braces: A window into the JavaScript world(استفاده از curly braces: پنجره ای به دنیای جاوا اسکریپت) {/*using-curly-braces-a-window-into-the-javascript-world*/}

JSX is a special way of writing JavaScript. That means it’s possible to use JavaScript inside it—with curly braces `{ }`. The example below first declares a name for the scientist, `name`, then embeds it with curly braces inside the `<h1>`:
////
استفاده از پرانتز آکولادی: یک پنجره به دنیای جاوا اسکریپت JSX یک روش ویژه برای نوشتن جاوا اسکریپت است. این بدان معناست که ممکن است درون آن از جاوا اسکریپت استفاده کنید - با پرانتز آکولادی { }. در مثال زیر، ابتدا یک نام برای دانشمند، name، اعلام می‌شود، سپس با پرانتز آکولادی درون `<h1>` جاسازی می‌شود:

<Sandpack>

```js
export default function TodoList() {
  const name = 'Gregorio Y. Zara';
  return (
    <h1>{name}'s To Do List</h1>
  );
}
```

</Sandpack>

Try changing the `name`'s value from `'Gregorio Y. Zara'` to `'Hedy Lamarr'`. See how the list title changes?

Any JavaScript expression will work between curly braces, including function calls like `formatDate()`:
////
سعی کنید مقدار name را از 'Gregorio Y. Zara' به 'Hedy Lamarr' تغییر دهید. مشاهده کنید که عنوان لیست چگونه تغییر می‌کند؟

هر عبارت جاوا اسکریپتی با پرانتز آکولادی، از جمله فراخوانی تابع مانند formatDate()، در میان آن کار می‌کند123 .
<Sandpack>

```js
const today = new Date();

function formatDate(date) {
  return new Intl.DateTimeFormat(
    'en-US',
    { weekday: 'long' }
  ).format(date);
}

export default function TodoList() {
  return (
    <h1>To Do List for {formatDate(today)}</h1>
  );
}
```

</Sandpack>

### Where to use curly braces(کجا میتونیم از پرانتز آکولادی استفاده کنیم) {/*where-to-use-curly-braces*/}

You can only use curly braces in two ways inside JSX:
////
در داخل JSX فقط می توانید از بریس های curly به دو صورت استفاده کنید:

1. **As text** directly inside a JSX tag: `<h1>{name}'s To Do List</h1>` works, but `<{tag}>Gregorio Y. Zara's To Do List</{tag}>`  will not.
2. **As attributes** immediately following the `=` sign: `src={avatar}` will read the `avatar` variable, but `src="{avatar}"` will pass the string `"{avatar}"`.

////
۱.به عنوان متن مستقیم درون یک تگ JSX: `<h1>{name}'s To Do List</h1>` کار می‌کند، اما `<{tag}>Gregorio Y. Zara’s To Do List</{tag}>` کار نخواهد کرد.

۲.به عنوان ویژگی‌هایی که بلافاصله پس از علامت = قرار می‌گیرند: `src={avatar}` مقدار متغیر avatar را خوانده، اما `src=“{avatar}”` رشته `“{avatar}”` را منتقل می‌کند.123 .

## Using "double curlies": CSS and other objects in JSX(استفاده از "دبل کرلی": CSS و سایر اشیاء در JSX) {/*using-double-curlies-css-and-other-objects-in-jsx*/}

In addition to strings, numbers, and other JavaScript expressions, you can even pass objects in JSX. Objects are also denoted with curly braces, like `{ name: "Hedy Lamarr", inventions: 5 }`. Therefore, to pass a JS object in JSX, you must wrap the object in another pair of curly braces: `person={{ name: "Hedy Lamarr", inventions: 5 }}`.

You may see this with inline CSS styles in JSX. React does not require you to use inline styles (CSS classes work great for most cases). But when you need an inline style, you pass an object to the `style` attribute:
////
به علاوه رشته‌ها، اعداد و سایر عبارات جاوا اسکریپت، شما می‌توانید حتی اشیاء را در JSX منتقل کنید. اشیاء همچنین با پرانتز آکولادی مشخص می‌شوند، مانند `{ name: “Hedy Lamarr”, inventions: 5 }.` بنابراین، برای انتقال یک شیء JS در JSX، باید آن را در یک جفت دیگر از پرانتز آکولادی بگذارید: `person={{ name: “Hedy Lamarr”, inventions: 5 }}.`

شما ممکن است این را با استیل‌های CSS درون خطی در JSX ببینید. React نیازی به استفاده از استیل‌های درون خطی ندارد (کلاس‌های CSS برای بیشتر موارد عالی عمل می‌کنند). اما هنگامی که نیاز به یک استیل درون خطی دارید، یک شیء را به ویژگی style منتقل می‌کنید:

App.js

<Sandpack>

```js
export default function TodoList() {
  return (
    <ul style={{
      backgroundColor: 'black',
      color: 'pink'
    }}>
      <li>Improve the videophone</li>
      <li>Prepare aeronautics lectures</li>
      <li>Work on the alcohol-fuelled engine</li>
    </ul>
  );
}
```

```css
body { padding: 0; margin: 0 }
ul { padding: 20px 20px 20px 40px; margin: 0; }
```

</Sandpack>

Try changing the values of `backgroundColor` and `color`.

You can really see the JavaScript object inside the curly braces when you write it like this:
////
سعی کنید مقادیر رنگ پس زمینه و رنگ را تغییر دهد.

واقعاً می‌توانید شیء جاوا اسکریپت را درون پرانتز آکولادی ببینید وقتی آن را به این شکل می‌نویسید:

```js {2-5}
<ul style={
  {
    backgroundColor: 'black',
    color: 'pink'
  }
}>
```

The next time you see `{{` and `}}` in JSX, know that it's nothing more than an object inside the JSX curlies!
////
دفعه بعد که `«{{» و «}}»` را در JSX مشاهده کردید، بدانید که چیزی بیش از یک شی در داخل بریس های JSX نیست!

<Pitfall>

Inline `style` properties are written in camelCase. For example, HTML `<ul style="background-color: black">` would be written as `<ul style={{ backgroundColor: 'black' }}>`  in your component.
////
ویژگی‌های استایل درون خطی با camelCase نوشته می‌شوند. به عنوان مثال، HTML `<ul style=“background-color: black”> به صورت <ul style={{ backgroundColor: ‘black’ }}>` در کامپوننت شما نوشته می‌شود.123.
</Pitfall>

## More fun with JavaScript objects and curly braces(بیشتر سرگرم کننده با اشیاء جاوا اسکریپت و پرانتز مجعد.) {/*more-fun-with-javascript-objects-and-curly-braces*/}

You can move several expressions into one object, and reference them in your JSX inside curly braces:
////
شما می‌توانید چندین عبارت را در یک شیء جاوا اسکریپت جمع کنید و آن‌ها را درون پرانتز آکولادی در JSX خود ارجاع دهید:

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

In this example, the `person` JavaScript object contains a `name` string and a `theme` object:
////
در این مثال، شی جاوا اسکریپت `"person"` حاوی یک رشته `"name"` و یک شی `"theme"` است:

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};
```

The component can use these values from `person` like so:
////
کامپوننت می‌تواند از این مقادیر از «person» مانند این استفاده کند:

```js
<div style={person.theme}>
  <h1>{person.name}'s Todos</h1>
```

JSX is very minimal as a templating language because it lets you organize data and logic using JavaScript.
////
JSX به عنوان یک زبان قالب‌بندی بسیار گم حجم است، زیرا به شما اجازه می‌دهد تا با استفاده از جاوا اسکریپت، داده‌ها و منطق را سازماندهی کنید123.

<Recap>

حالا شما تقریباً همه چیز در مورد JSX را می‌دانید:

ویژگی‌های JSX درون نقل قول‌ها به عنوان رشته‌ها منتقل می‌شوند. پرانتز آکولادی به شما اجازه می‌دهد منطق و متغیرهای جاوا اسکریپت را درون نشانه‌گذاری خود بیاورید. آن‌ها درون محتوای تگ JSX یا فوراً پس از = در ویژگی‌ها کار می‌کنند. `{{ و }}` نحوی خاص نیستند: یک شیء جاوا اسکریپت درون پرانتز آکولادی JSX قرار دارد.1234.


////


</Recap>

<Challenges>

#### Fix the mistake {/*fix-the-mistake*/}

This code crashes with an error saying `Objects are not valid as a React child`:
این کد خطای `Objects are not valid as a React child` رو برمیگردونه

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

Can you find the problem?
////
آیا می توانید مشکل را پیدا کنید؟

<Hint>
Look for what's inside the curly braces. Are we putting the right thing there?
////
دنبال چیزی بگرد که داخل اون پرانتزهای مجعده هست داریم چیز درست رو اونجا میذاریم1.
</Hint>

<Solution>

This is happening because this example renders *an object itself* into the markup rather than a string: `<h1>{person}'s Todos</h1>` is trying to render the entire `person` object! Including raw objects as text content throws an error because React doesn't know how you want to display them.

To fix it, replace `<h1>{person}'s Todos</h1>` with `<h1>{person.name}'s Todos</h1>`:
////
این اتفاق به دلیل این است که این مثال یک شیء را به جای یک رشته در نشانگرها رندر می کند: `<h1>{person}'s Todos</h1>` سعی می کند که کل شیء person را رندر کند! شامل شیء های خام به عنوان محتوای متنی باعث پرتاب خطا می شود زیرا React نمی داند که شما می خواهید آنها را چگونه نمایش دهید.

برای رفع مشکل، `<h1>{person.name}'s Todos</h1>` را با `<h1>{person}'s Todos</h1>` جایگزین کنید1.

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

</Solution>

#### Extract information into an object(استخراج اطلاعات در یک شی) {/*extract-information-into-an-object*/}

Extract the image URL into the `person` object.
////
URL تصویر را در شیء `«person»` استخراج کنید.

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

<Solution>

Move the image URL into a property called `person.imageUrl` and read it from the `<img>` tag using the curlies:
////
URL تصویر را به یک ویژگی به نام `"person.imageUrl"` منتقل کنید و آن را از برچسب `"<img>" `با استفاده از curlies بخوانید:
////
Move the image URL into a property called `'person.imageUrl'` and read it from the `'<img>'` tag using the curlies: به فارسی می‌شود: آدرس تصویر را در یک خاصیت به نام `'person.imageUrl'` قرار دهید و از داخل براکت‌های زمینه‌ای در تگ `'<img>'` آن را بخوانید.1.
<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  imageUrl: "https://i.imgur.com/7vQD0fPs.jpg",
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src={person.imageUrl}
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

</Solution>

#### Write an expression inside JSX curly braces(یک عبارت در داخل پرانتزهای JSX بنویسید) {/*write-an-expression-inside-jsx-curly-braces*/}

In the object below, the full image URL is split into four parts: base URL, `imageId`, `imageSize`, and file extension.
////
در شی زیر، URL کامل تصویر به چهار بخش تقسیم می‌شود: URL پایه، "imageId"، "imageSize" و پسوند فایل.

We want the image URL to combine these attributes together: base URL (always `'https://i.imgur.com/'`), `imageId` (`'7vQD0fP'`), `imageSize` (`'s'`), and file extension (always `'.jpg'`). However, something is wrong with how the `<img>` tag specifies its `src`.

Can you fix it?
////
ما می‌خواهیم URL تصویر این ویژگی‌ها را با هم ترکیب کند: URL پایه (همیشه «https://i.imgur.com/»)، `imageId` («7vQD0fP»»)، `imageSize` (`'s'`)، و پسوند فایل (همیشه ``.jpg``). با این حال، در نحوه تعیین تگ `<img>` `src` آن مشکلی وجود دارد.

می تونی درستش کنی؟
<Sandpack>

```js

const baseUrl = 'https://i.imgur.com/';
const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="{baseUrl}{person.imageId}{person.imageSize}.jpg"
        alt={person.name}
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; }
```

</Sandpack>

To check that your fix worked, try changing the value of `imageSize` to `'b'`. The image should resize after your edit.
////
برای بررسی اینکه آیا رفع مشکل شما جواب داده است، مقدار `"imageSize"` را به `"b"` تغییر دهید. اندازه تصویر پس از ویرایش شما باید تغییر کند.
<Solution>

You can write it as `src={baseUrl + person.imageId + person.imageSize + '.jpg'}`.
////
می توانید آن را به صورت `src={baseUrl + person.imageId + person.imageSize + '.jpg'}` بنویسید.

1. `{` opens the JavaScript expression
2. `baseUrl + person.imageId + person.imageSize + '.jpg'` produces the correct URL string
3. `}` closes the JavaScript expression
////
1. `{` عبارت جاوا اسکریپت را باز می کند
2. `«baseUrl + person.imageId + person.imageSize + '.jpg'»` رشته URL صحیح را تولید می کند
3. `}` عبارت جاوا اسکریپت را می بندد
<Sandpack>

```js
const baseUrl = 'https://i.imgur.com/';
const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src={baseUrl + person.imageId + person.imageSize + '.jpg'}
        alt={person.name}
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; }
```

</Sandpack>

You can also move this expression into a separate function like `getImageUrl` below:
////
همچنین می توانید این عبارت را به یک تابع جداگانه مانند 'getImageUrl' در زیر منتقل کنید:

<Sandpack>

```js App.js
import { getImageUrl } from './utils.js'

const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src={getImageUrl(person)}
        alt={person.name}
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```js utils.js
export function getImageUrl(person) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    person.imageSize +
    '.jpg'
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; }
```

</Sandpack>

Variables and functions can help you keep the markup simple!
////
همچنین می توانید این عبارت را به یک تابع جداگانه مانند 'getImageUrl' در زیر منتقل کنید:

</Solution>

</Challenges>
