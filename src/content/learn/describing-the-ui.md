---
title: توصیف رابط کاربری
---

<Intro>

React is a JavaScript library for rendering user interfaces (UI). UI is built from small units like buttons, text, and images. React lets you combine them into reusable, nestable *components.* From web sites to phone apps, everything on the screen can be broken down into components. In this chapter, you'll learn to create, customize, and conditionally display React components.
////////////
React یک کتابخانه‌ی جاوااسکریپتی برای رندر کردن رابط کاربری (UI) است. رابط کاربری از واحدهای کوچکی مانند دکمه‌ها، متن‌ها و تصاویر ساخته می‌شود. React به شما اجازه می‌دهد تا آنها را به صورت کامپوننت‌های قابل استفاده و قابل تو در تو ترکیب کنید. از وب سایت‌ها تا برنامه‌های تلفن همراه، همه چیز روی صفحه می‌تواند به کامپوننت‌ها تقسیم شود. در این فصل، شما یاد خواهید گرفت که چگونه کامپوننت‌های React را ایجاد، سفارشی کنید و به صورت شرطی نمایش دهید.

</Intro>

<YouWillLearn isChapter={true}>

* [How to write your first React component](/learn/your-first-component)
* [When and how to create multi-component files](/learn/importing-and-exporting-components)
* [How to add markup to JavaScript with JSX](/learn/writing-markup-with-jsx)
* [How to use curly braces with JSX to access JavaScript functionality from your components](/learn/javascript-in-jsx-with-curly-braces)
* [How to configure components with props](/learn/passing-props-to-a-component)
* [How to conditionally render components](/learn/conditional-rendering)
* [How to render multiple components at a time](/learn/rendering-lists)
* [How to avoid confusing bugs by keeping components pure](/learn/keeping-components-pure)
* [Why understanding your UI as trees is useful](/learn/understanding-your-ui-as-a-tree)

</YouWillLearn>

## Your first component(ساخت اولین کامپوننت شما) {/*your-first-component*/}

React applications are built from isolated pieces of UI called *components*. A React component is a JavaScript function that you can sprinkle with markup. Components can be as small as a button, or as large as an entire page. Here is a `Gallery` component rendering three `Profile` components:

///////////////
برنامه‌های React از قطعات مجزای رابط کاربری به نام *کامپوننت* ساخته می‌شوند. کامپوننت React یک تابع جاوا اسکریپت است که می‌توانید آن را با نشانه‌گذاری بسازید. کامپوننت ها می توانند به کوچکی یک دکمه یا به بزرگی کل صفحه باشند. در اینجا یک کامپوننت «گالری» است که سه کامپوننت «نمایه» را ارائه می‌کند:
<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

<LearnMore path="/learn/your-first-component">

Read **[Your First Component](/learn/your-first-component)** to learn how to declare and use React components.
////
برای یادگیری نحوه اعلان و استفاده از کامپوننت‌های React، First Component خود را بخوانید.

</LearnMore>

## Importing and exporting components {/*importing-and-exporting-components*/}

You can declare many components in one file, but large files can get difficult to navigate. To solve this, you can *export* a component into its own file, and then *import* that component from another file:
////
شما می توانید بسیاری از کامپوننت ها را در یک فایل اعلام کنید، اما پیمایش فایل های بزرگ ممکن است دشوار باشد. برای حل این مشکل، می توانید یک کامپوننت را به فایل خودش صادر کنید و سپس آن کامپوننت را از فایل دیگری وارد کنید:

<Sandpack>

```js App.js hidden
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```

```js Gallery.js active
import Profile from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export default function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; }
```

</Sandpack>

<LearnMore path="/learn/importing-and-exporting-components">

Read **[Importing and Exporting Components](/learn/importing-and-exporting-components)** to learn how to split components into their own files.
////
Importing and Exporting Components را بخوانید تا یاد بگیرید چگونه اجزا را به فایل های خود تقسیم کنید.
</LearnMore>

## Writing markup with JSX(نوشتن نشانه گذاری با JSX) {/*writing-markup-with-jsx*/}

Each React component is a JavaScript function that may contain some markup that React renders into the browser. React components use a syntax extension called JSX to represent that markup. JSX looks a lot like HTML, but it is a bit stricter and can display dynamic information.

If we paste existing HTML markup into a React component, it won't always work:
////
هر جزء React یک تابع جاوا اسکریپت است که ممکن است حاوی مقداری نشانه گذاری باشد که React در مرورگر ارائه می کند. اجزای React از یک پسوند نحوی به نام JSX برای نشان دادن آن نشانه گذاری استفاده می کنند. JSX شباهت زیادی به HTML دارد، اما کمی سخت‌تر است و می‌تواند اطلاعات پویا را نمایش دهد.

اگر نشانه‌گذاری HTML موجود را در یک جزء React بچسبانیم، همیشه کار نخواهد کرد:
////
هر کامپوننت React یک تابع جاوااسکریپتی است که ممکن است شامل برخی نشانه‌گذاری باشد که React آن را در مرورگر رندر می‌کند. کامپوننت‌های React از یک افزونه‌ی نحوی به نام JSX برای نمایش آن نشانه‌گذاری استفاده می‌کنند. JSX شبیه به HTML است، اما کمی سختگیرانه‌تر است و می‌تواند اطلاعات پویا را نمایش دهد.

اگر ما نشانه‌گذاری HTML موجود را در یک کامپوننت React قرار دهیم، همیشه کار نمی‌کند.
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
      <li>Improve spectrum technology
    </ul>
  );
}
```

```css
img { height: 90px; }
```

</Sandpack>

If you have existing HTML like this, you can fix it using a [converter](https://transform.tools/html-to-jsx):
////
اگر HTML مانند این دارید، می‌توانید با استفاده از [مبدل] (https://transform.tools/html-to-jsx) آن را برطرف کنید:

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
        <li>Improve spectrum technology</li>
      </ul>
    </>
  );
}
```

```css
img { height: 90px; }
```

</Sandpack>

<LearnMore path="/learn/writing-markup-with-jsx">

Read **[Writing Markup with JSX](/learn/writing-markup-with-jsx)** to learn how to write valid JSX.
////
**[Writing Markup with JSX](/learn/writing-markup-with-jsx)** را بخوانید تا یاد بگیرید چگونه JSX معتبر بنویسید.

</LearnMore>

## JavaScript in JSX with curly braces(جاوا اسکریپت در JSX با کرلی بریس) {/*javascript-in-jsx-with-curly-braces*/}

JSX lets you write HTML-like markup inside a JavaScript file, keeping rendering logic and content in the same place. Sometimes you will want to add a little JavaScript logic or reference a dynamic property inside that markup. In this situation, you can use curly braces in your JSX to "open a window" to JavaScript:
////
JSX به شما امکان می دهد نشانه گذاری HTML مانند را در یک فایل جاوا اسکریپت بنویسید و منطق و محتوا را در همان مکان نگه دارید. گاهی اوقات می خواهید کمی منطق جاوا اسکریپت اضافه کنید یا به یک ویژگی پویا در داخل آن نشانه گذاری ارجاع دهید. در این شرایط، می‌توانید از بریس‌های فرفری در JSX خود برای «باز کردن پنجره» به جاوا اسکریپت استفاده کنید:
////
JSX به شما اجازه می‌دهد تا نشانه‌گذاری شبیه به HTML را درون یک فایل جاوااسکریپتی بنویسید و منطق رندرینگ و محتوا را در یک مکان نگه دارید. گاهی اوقات ممکن است بخواهید یک کم منطق جاوااسکریپت اضافه کنید یا به یک خاصیت پویا درون آن نشانه‌گذاری ارجاع دهید. در این موقعیت، می‌توانید از پرانتز آکولادی در JSX خود استفاده کنید تا “یک پنجره” به جاوااسکریپت باز کنید.
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

<LearnMore path="/learn/javascript-in-jsx-with-curly-braces">

Read **[JavaScript in JSX with Curly Braces](/learn/javascript-in-jsx-with-curly-braces)** to learn how to access JavaScript data from JSX.
////
جاوا اسکریپت را در JSX با بریس های فرفری بخوانید تا نحوه دسترسی به داده های جاوا اسکریپت از JSX را بیاموزید.
////
برای یادگیری دسترسی به داده‌های جاوااسکریپت از JSX، می‌توانید به مطلب JavaScript in JSX with Curly Braces مراجعه کنید.

</LearnMore>

## Passing props to a component(انتقال پراپ به یک کامپوننت) {/*passing-props-to-a-component*/}

React components use *props* to communicate with each other. Every parent component can pass some information to its child components by giving them props. Props might remind you of HTML attributes, but you can pass any JavaScript value through them, including objects, arrays, functions, and even JSX!
////
اجزای React برای برقراری ارتباط با یکدیگر از props استفاده می کنند. هر مؤلفه والد می تواند با دادن ابزارهایی به مؤلفه های فرزند خود، اطلاعاتی را به آنها منتقل کند. Props ممکن است به شما ویژگی های HTML را یادآوری کند، اما می توانید هر مقدار جاوا اسکریپت را از طریق آنها ارسال کنید، از جمله اشیاء، آرایه ها، توابع و حتی JSX!
////
کامپوننت‌های React از props برای ارتباط با یکدیگر استفاده می‌کنند. هر کامپوننت والد می‌تواند با دادن props، برخی اطلاعات را به کامپوننت‌های فرزند خود منتقل کند. Props ممکن است به شما یادآور ویژگی‌های HTML باشد، اما شما می‌توانید هر مقدار جاوااسکریپتی را از طریق آنها منتقل کنید، از جمله اشیاء، آرایه‌ها، توابع و حتی JSX!
<Sandpack>

```js
import { getImageUrl } from './utils.js'

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

```

```js utils.js
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
.card {
  width: fit-content;
  margin: 5px;
  padding: 5px;
  font-size: 20px;
  text-align: center;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.avatar {
  margin: 20px;
  border-radius: 50%;
}
```

</Sandpack>

<LearnMore path="/learn/passing-props-to-a-component">

Read **[Passing Props to a Component](/learn/passing-props-to-a-component)** to learn how to pass and read props.
////
برای یادگرفتن نحوه پاس دادن و خواندن پروپوزال ها را بخوانید.


</LearnMore>

## Conditional rendering(رندر شرطی) {/*conditional-rendering*/}

Your components will often need to display different things depending on different conditions. In React, you can conditionally render JSX using JavaScript syntax like `if` statements, `&&`, and `? :` operators.

In this example, the JavaScript `&&` operator is used to conditionally render a checkmark:
////
اجزای شما معمولاً بسته به شرایط مختلف نیاز به نمایش چیزهای مختلفی دارند. در React، می‌توانید JSX را با استفاده از نحو جاوا اسکریپت مانند دستورات «if»، «&&» و «به صورت مشروط رندر کنید؟ :` اپراتورها

در این مثال، عملگر «&&» جاوا اسکریپت برای ارائه یک علامت چک به صورت مشروط استفاده می‌شود:
////
کامپوننت‌های شما اغلب باید چیزهای مختلفی را بر اساس شرایط مختلف نمایش دهند. در React، می‌توانید با استفاده از نحو جاوااسکریپتی مانند if statements، && و ? : operators به صورت شرطی JSX را رندر کنید.

در این مثال، اپراتور && جاوااسکریپت برای رندر شرطی یک تیک استفاده شده است:

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

<LearnMore path="/learn/conditional-rendering">

Read **[Conditional Rendering](/learn/conditional-rendering)** to learn the different ways to render content conditionally.
////
رندر شرطی را بخوانید تا روش‌های مختلف ارائه محتوا را به صورت مشروط بیاموزید.

</LearnMore>

## Rendering lists {/*rendering-lists*/}

You will often want to display multiple similar components from a collection of data. You can use JavaScript's `filter()` and `map()` with React to filter and transform your array of data into an array of components.

For each array item, you will need to specify a `key`. Usually, you will want to use an ID from the database as a `key`. Keys let React keep track of each item's place in the list even if the list changes.
////
اغلب می خواهید چندین مؤلفه مشابه را از مجموعه ای از داده ها نمایش دهید. می توانید از filter() و map() جاوا اسکریپت با React برای فیلتر کردن و تبدیل آرایه داده های خود به آرایه ای از اجزاء استفاده کنید.

برای هر آیتم آرایه باید یک کلید مشخص کنید. معمولاً می خواهید از یک شناسه از پایگاه داده به عنوان کلید استفاده کنید. کلیدها به React اجازه می‌دهند تا مکان هر مورد را در لیست پیگیری کند، حتی اگر لیست تغییر کند.
////
شما اغلب باید چندین کامپوننت مشابه را از یک مجموعه داده نمایش دهید. شما می‌توانید از filter() و map() جاوااسکریپت با React برای فیلتر کردن و تبدیل آرایه داده خود به آرایه‌ای از کامپوننت‌ها استفاده کنید.

برای هر مورد آرایه، شما باید یک کلید مشخص کنید. معمولاً، شما می‌خواهید از یک ID از پایگاه داده به عنوان یک کلید استفاده کنید. کلیدها به React اجازه می‌دهند که محل هر مورد در لیست را حتی اگر لیست تغییر کند، پیگیری کند.
<Sandpack>

```js App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const listItems = people.map(person =>
    <li key={person.id}>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}:</b>
        {' ' + person.profession + ' '}
        known for {person.accomplishment}
      </p>
    </li>
  );
  return (
    <article>
      <h1>Scientists</h1>
      <ul>{listItems}</ul>
    </article>
  );
}
```

```js data.js
export const people = [{
  id: 0,
  name: 'Creola Katherine Johnson',
  profession: 'mathematician',
  accomplishment: 'spaceflight calculations',
  imageId: 'MK3eW3A'
}, {
  id: 1,
  name: 'Mario José Molina-Pasquel Henríquez',
  profession: 'chemist',
  accomplishment: 'discovery of Arctic ozone hole',
  imageId: 'mynHUSa'
}, {
  id: 2,
  name: 'Mohammad Abdus Salam',
  profession: 'physicist',
  accomplishment: 'electromagnetism theory',
  imageId: 'bE7W1ji'
}, {
  id: 3,
  name: 'Percy Lavon Julian',
  profession: 'chemist',
  accomplishment: 'pioneering cortisone drugs, steroids and birth control pills',
  imageId: 'IOjWm71'
}, {
  id: 4,
  name: 'Subrahmanyan Chandrasekhar',
  profession: 'astrophysicist',
  accomplishment: 'white dwarf star mass calculations',
  imageId: 'lrWQx8l'
}];
```

```js utils.js
export function getImageUrl(person) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    's.jpg'
  );
}
```

```css
ul { list-style-type: none; padding: 0px 10px; }
li {
  margin-bottom: 10px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
h1 { font-size: 22px; }
h2 { font-size: 20px; }
```

</Sandpack>

<LearnMore path="/learn/rendering-lists">

Read **[Rendering Lists](/learn/rendering-lists)** to learn how to render a list of components, and how to choose a key.
////
**[Rendering Lists](/learn/rendering-lists)** را بخوانید تا یاد بگیرید چگونه فهرستی از کامپوننت ها را رندر کنید و چگونه یک کلید را انتخاب کنید.

</LearnMore>

## Keeping components pure(پیور نگه داشتن کامپوننت) {/*keeping-components-pure*/}

Some JavaScript functions are *pure.* A pure function:

* **Minds its own business.** It does not change any objects or variables that existed before it was called.
* **Same inputs, same output.** Given the same inputs, a pure function should always return the same result.

By strictly only writing your components as pure functions, you can avoid an entire class of baffling bugs and unpredictable behavior as your codebase grows. Here is an example of an impure component:
////
برخی از توابع جاوا اسکریپت *خالص هستند.* یک تابع خالص:

* **به کار خودش فکر می کند.** هیچ شی یا متغیری را که قبل از فراخوانی وجود داشته است تغییر نمی دهد.
* **ورودی های یکسان، خروجی یکسان.** با توجه به ورودی های یکسان، یک تابع خالص همیشه باید همان نتیجه را برگرداند.

با صرفاً نوشتن اجزای خود به عنوان توابع خالص، می توانید از یک کلاس کامل از اشکالات گیج کننده و رفتار غیرقابل پیش بینی با رشد پایگاه کد خود جلوگیری کنید. در اینجا مثالی از یک جزء ناخالص آورده شده است:
////
تابع‌های جاوااسکریپت پاک هستند. یک تابع پاک:

کار خودش را انجام می‌دهد. هیچ شیء یا متغیری که قبل از فراخوانی آن وجود داشته باشد، تغییر نمی‌دهد.
ورودی‌های یکسان، خروجی یکسان. با دادن ورودی‌های یکسان، یک تابع پاک همیشه باید نتیجه‌ی یکسانی را برگرداند.
با صرفاً نوشتن کامپوننت‌های خود به عنوان تابع‌های پاک، می‌توانید از یک کلاس کامل از باگ‌های گیج‌کننده و رفتارهای پیش‌بینی‌ناپذیر در هنگام رشد کد پرهیز کنید. در اینجا مثالی از یک کامپوننت ناپاک آمده است:

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

You can make this component pure by passing a prop instead of modifying a preexisting variable:
////
شما می توانید این کامپوننت را با ارسال یک prop به جای تغییر یک متغیر از قبل موجود خالص کنید:
////
شما می‌توانید این کامپوننت را پاک کنید با این کار که به جای تغییر متغیر قبلی، یک prop را ارسال کنید. برای یادگیری ارسال و خواندن props، می‌توانید به مطلب Passing Props to a Component مراجعه کنید. در این مطلب، شما با مفاهیم پایه‌ی کامپوننت‌ها، props، rendering و انتقال props به کامپوننت‌های دیگر آشنا خواهید شد.
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

<LearnMore path="/learn/keeping-components-pure">

Read **[Keeping Components Pure](/learn/keeping-components-pure)** to learn how to write components as pure, predictable functions.
////
**[Keeping Components Pure](/learn/keeping-Components-pure)** را بخوانید تا یاد بگیرید چگونه کامپوننت را به عنوان توابع خالص و قابل پیش بینی بنویسید.

</LearnMore>

## Your UI as a tree(رابط کاربری شما به عنوان یک درخت) {/*your-ui-as-a-tree*/}

React uses trees to model the relationships between components and modules. 

A React render tree is a representation of the parent and child relationship between components. 
////
React از درخت ها برای مدل سازی روابط بین اجزا و ماژول ها استفاده می کند. درخت رندر React نمایشی از رابطه والد و فرزند بین اجزا است.
////
React از یک مدل درختی برای مدل کردن روابط بین کامپوننت‌ها و ماژول‌ها استفاده می‌کند.

درخت رندر React نمایشی از رابطه والد و فرزند بین کامپوننت‌ها است.


<Diagram name="generic_render_tree" height={250} width={500} alt="A tree graph with five nodes, with each node representing a component. The root node is located at the top the tree graph and is labelled 'Root Component'. It has two arrows extending down to two nodes labelled 'Component A' and 'Component C'. Each of the arrows is labelled with 'renders'. 'Component A' has a single 'renders' arrow to a node labelled 'Component B'. 'Component C' has a single 'renders' arrow to a node labelled 'Component D'.">

An example React render tree.

</Diagram>

Components near the top of the tree, near the root component, are considered top-level components. Components with no child components are leaf components. This categorization of components is useful for understanding data flow and rendering performance.

Modelling the relationship between JavaScript modules is another useful way to understand your app. We refer to it as a module dependency tree. 
////
اجزای نزدیک به بالای درخت، نزدیک جزء ریشه، اجزای سطح بالایی در نظر گرفته می شوند. اجزای بدون اجزای فرزند، اجزای برگ هستند. این دسته بندی مولفه ها برای درک جریان داده و عملکرد رندر مفید است.

مدل سازی رابطه بین ماژول های جاوا اسکریپت یکی دیگر از راه های مفید برای درک برنامه شما است. ما از آن به عنوان درخت وابستگی ماژول یاد می کنیم.
////
کامپوننت‌های نزدیک به بالای درخت، نزدیک به کامپوننت ریشه، به عنوان کامپوننت‌های سطح بالا در نظر گرفته می‌شوند. کامپوننت‌هایی که هیچ کامپوننت فرزندی ندارند، کامپوننت‌های برگ هستند. این طبقه‌بندی از کامپوننت‌ها برای درک جریان داده و عملکرد رندرینگ مفید است.

مدل‌سازی رابطه بین ماژول‌های جاوااسکریپتی یک روش دیگر مفید برای درک برنامه شما است. به آن درخت وابستگی ماژول می‌گوییم.


<Diagram name="generic_dependency_tree" height={250} width={500} alt="A tree graph with five nodes. Each node represents a JavaScript module. The top-most node is labelled 'RootModule.js'. It has three arrows extending to the nodes: 'ModuleA.js', 'ModuleB.js', and 'ModuleC.js'. Each arrow is labelled as 'imports'. 'ModuleC.js' node has a single 'imports' arrow that points to a node labelled 'ModuleD.js'.">

An example module dependency tree.
////
نمونه درخت وابستگی ماژول.
////
درخت وابستگی ماژول یک روش مفید برای درک برنامه شما است. این درخت به ابزارهای ساخت برای بسته‌بندی کل کد جاوااسکریپت مربوط به مشتری برای دانلود و رندر کردن کمک می‌کند. درخت وابستگی ماژول به شما کمک می‌کند تا بتوانید به راحتی از مشکلاتی مانند اندازه بسته بزرگ و عملکرد رندرینگ پیش‌بینی‌ناپذیر جلوگیری کنید.
</Diagram>

A dependency tree is often used by build tools to bundle all the relevant JavaScript code for the client to download and render. A large bundle size regresses user experience for React apps. Understanding the module dependency tree is helpful to debug such issues. 
////
درخت وابستگی اغلب توسط ابزارهای بیلد استفاده می شود تا تمام کدهای جاوا اسکریپت مربوطه را برای دانلود و رندر کلاینت جمع آوری کند. اندازه بسته بزرگ تجربه کاربر را برای برنامه‌های React پس می‌گیرد. درک درخت وابستگی ماژول برای رفع اشکال چنین مسائلی مفید است.
////
درخت وابستگی اغلب توسط ابزارهای ساخت برای بسته‌بندی کل کد جاوااسکریپت مربوط به مشتری برای دانلود و رندر کردن استفاده می‌شود. اندازه بسته بزرگ، تجربه کاربر را برای برنامه‌های React بهبود نمی‌بخشد. درک درخت وابستگی ماژول برای رفع اینگونه مشکلات مفید است.


>leaf component مامپوننت هایی هستن که هیچ فرزندی ندارن و به طور مستقیم یا یک کامپوننت دیگرو رندر میکنن یا یک المنت رو
```js
return <ComponentB />;

return <h1> This is componentB </h1>
```
<LearnMore path="/learn/understanding-your-ui-as-a-tree">

Read **[Your UI as a Tree](/learn/understanding-your-ui-as-a-tree)** to learn how to create a render and module dependency trees for a React app and how they're useful mental models for improving user experience and performance.
////
برای یادگیری نحوه ایجاد درخت‌های وابستگی رندر و ماژول برای برنامه React و اینکه چگونه آنها مدل‌های ذهنی مفیدی برای بهبود تجربه و عملکرد کاربر هستند، رابط کاربری خود را به عنوان درخت بخوانید.
////
</LearnMore>


## What's next? {/*whats-next*/}

Head over to [Your First Component](/learn/your-first-component) to start reading this chapter page by page!

Or, if you're already familiar with these topics, why not read about [Adding Interactivity](/learn/adding-interactivity)?
////
برای شروع خواندن صفحه به صفحه این فصل به [Your First Component] (/learn/your-first-component) بروید!

یا، اگر قبلاً با این موضوعات آشنا هستید، چرا درباره [افزودن تعامل](/learn/adding-interactivity) مطالعه نکنید؟
