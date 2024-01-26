---
title: Rendering Lists
---

<Intro>

You will often want to display multiple similar components from a collection of data. You can use the [JavaScript array methods](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array#) to manipulate an array of data. On this page, you'll use [`filter()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) and [`map()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/map) with React to filter and transform your array of data into an array of components.
////
اغلب می خواهید چندین مؤلفه مشابه را از مجموعه ای از داده ها نمایش دهید. می توانید از روش های آرایه جاوا اسکریپت برای دستکاری آرایه ای از داده ها استفاده کنید. در این صفحه، از filter() و map() با React برای فیلتر کردن و تبدیل آرایه داده های خود به آرایه ای از کامپوننت ها استفاده می کنید.
////
شما اغلب می‌خواهید چندین اجزای مشابه را از مجموعه‌ای از داده‌ها نمایش دهید. شما می‌توانید از روش‌های آرایه جاوااسکریپت برای تغییر یک آرایه داده استفاده کنید. در این صفحه، شما با استفاده از filter() و map() با React، آرایه داده خود را فیلتر و تبدیل به آرایه اجزا تغییر می‌دهید.
</Intro>

<YouWillLearn>

* How to render components from an array using JavaScript's `map()`
* How to render only specific components using JavaScript's `filter()`
* When and why to use React keys
////
* نحوه رندر کردن اجزاء از یک آرایه با استفاده از "map()" جاوا اسکریپت
* چگونه با استفاده از "filter()" جاوا اسکریپت فقط اجزای خاصی را رندر کنیم
* زمان و چرایی استفاده از کلیدهای React
////
* چگونه اجزا را از یک آرایه با استفاده از `map()` جاوااسکریپت رندر کنیم
* چگونه فقط اجزای خاصی را با استفاده از `filter()` جاوااسکریپت رندر کنیم
* زمان و دلیل استفاده از کلیدهای React

</YouWillLearn>

## Rendering data from arrays {/*rendering-data-from-arrays*/}

Say that you have a list of content.
////
می‌گویند که شما یک لیست از محتوا دارید.

```js
<ul>
  <li>Creola Katherine Johnson: mathematician</li>
  <li>Mario José Molina-Pasquel Henríquez: chemist</li>
  <li>Mohammad Abdus Salam: physicist</li>
  <li>Percy Lavon Julian: chemist</li>
  <li>Subrahmanyan Chandrasekhar: astrophysicist</li>
</ul>
```

The only difference among those list items is their contents, their data. You will often need to show several instances of the same component using different data when building interfaces: from lists of comments to galleries of profile images. In these situations, you can store that data in JavaScript objects and arrays and use methods like [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) and [`filter()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) to render lists of components from them.

Here’s a short example of how to generate a list of items from an array:
////
تنها تفاوت بین آن آیتم های فهرست، محتویات، داده هایشان است. هنگام ساخت رابط‌ها، اغلب باید چندین نمونه از یک مؤلفه را با استفاده از داده‌های مختلف نشان دهید: از فهرست نظرات گرفته تا گالری تصاویر نمایه. در این شرایط، می‌توانید آن داده‌ها را در اشیاء و آرایه‌های جاوا اسکریپت ذخیره کنید و از روش‌هایی مانند map() و filter() برای رندر کردن لیست اجزا از آنها استفاده کنید.

در اینجا یک مثال کوتاه از نحوه ایجاد لیستی از آیتم ها از یک آرایه آورده شده است:
////
تنها تفاوت بین آن موارد فهرست، محتوا و داده‌های آنهاست. شما اغلب نیاز دارید چندین نمونه از همان اجزا را با استفاده از داده‌های مختلف در هنگام ساخت رابط‌ها نشان دهید: از فهرست‌های نظرات تا گالری تصاویر پروفایل. در این موارد، می‌توانید این داده‌ها را در شیء‌ها و آرایه‌های جاوااسکریپت ذخیره کرده و از روش‌هایی مانند map() و filter() برای رندر کردن لیست‌هایی از اجزا استفاده کنید.

در اینجا یک مثال کوتاه از نحوه تولید یک لیست از موارد از یک آرایه است.


1. **Move** the data into an array:
1. **انتقال** داده ها به یک آرایه:

```js
const people = [
  'Creola Katherine Johnson: mathematician',
  'Mario José Molina-Pasquel Henríquez: chemist',
  'Mohammad Abdus Salam: physicist',
  'Percy Lavon Julian: chemist',
  'Subrahmanyan Chandrasekhar: astrophysicist'
];
```

2. **Map** the `people` members into a new array of JSX nodes, `listItems`:
2. **نقشه** اعضای `people` را در آرایه جدیدی از گره های JSX، `listItems` قرار دهید:

```js
const listItems = people.map(person => <li>{person}</li>);
```

3. **Return** `listItems` from your component wrapped in a `<ul>`:
3. **برگردان ** `listItems` از component شما که در `<ul>` پیچیده شده است:

```js
return <ul>{listItems}</ul>;
```

Here is the result:

<Sandpack>

```js
const people = [
  'Creola Katherine Johnson: mathematician',
  'Mario José Molina-Pasquel Henríquez: chemist',
  'Mohammad Abdus Salam: physicist',
  'Percy Lavon Julian: chemist',
  'Subrahmanyan Chandrasekhar: astrophysicist'
];

export default function List() {
  const listItems = people.map(person =>
    <li>{person}</li>
  );
  return <ul>{listItems}</ul>;
}
```

```css
li { margin-bottom: 10px; }
```

</Sandpack>

Notice the sandbox above displays a console error:
توجه داشته باشید که جعبه ایمنی بالا یک خطای کنسول را نشان می دهد:

<ConsoleBlock level="error">

Warning: Each child in a list should have a unique "key" prop.
اخطار: هر child در یک لیست باید یک prop "کلید" منحصر به فرد داشته باشد.

</ConsoleBlock>

You'll learn how to fix this error later on this page. Before we get to that, let's add some structure to your data.
////
نحوه رفع این خطا را بعداً در این صفحه خواهید آموخت. قبل از رسیدن به آن، اجازه دهید ساختاری به داده های شما اضافه کنیم.
////
شما در ادامه این صفحه یاد خواهید گرفت که چگونه این خطا را برطرف کنید. قبل از آن، بیایید ساختاری را به داده‌های خود اضافه کنیم.

## Filtering arrays of items(فیلتر کردن آیتم های آرایه ها ) {/*filtering-arrays-of-items*/}

This data can be structured even more.
این داده ها را می توان حتی بیشتر ساختار داد.

```js
const people = [{
  id: 0,
  name: 'Creola Katherine Johnson',
  profession: 'mathematician',
}, {
  id: 1,
  name: 'Mario José Molina-Pasquel Henríquez',
  profession: 'chemist',
}, {
  id: 2,
  name: 'Mohammad Abdus Salam',
  profession: 'physicist',
}, {
  name: 'Percy Lavon Julian',
  profession: 'chemist',  
}, {
  name: 'Subrahmanyan Chandrasekhar',
  profession: 'astrophysicist',
}];
```

Let's say you want a way to only show people whose profession is `'chemist'`. You can use JavaScript's `filter()` method to return just those people. This method takes an array of items, passes them through a “test” (a function that returns `true` or `false`), and returns a new array of only those items that passed the test (returned `true`).
////
فرض کنید راهی می‌خواهید که فقط به افرادی که حرفه‌شان «شیمی‌دان» است نشان دهید. می‌توانید از روش «filter()» جاوا اسکریپت برای بازگرداندن آن افراد استفاده کنید. این متد آرایه‌ای از آیتم‌ها را می‌گیرد، آن‌ها را از طریق یک «تست» (عملکردی که «درست» یا «نادرست» را برمی‌گرداند) عبور می‌دهد و یک آرایه جدید فقط از آیتم‌هایی که آزمون را گذرانده‌اند (به «درست» برمی‌گرداند) برمی‌گرداند.
////
بیایید فرض کنیم که می‌خواهید راهی برای نمایش فقط افرادی که حرفه آنها “شیمی‌دان” است، داشته باشید. شما می‌توانید از روش filter() جاوااسکریپت برای بازگرداندن فقط این افراد استفاده کنید. این روش یک آرایه از موارد را می‌گیرد، آنها را از طریق یک “آزمون” (یک تابع که مقدار true یا false برمی‌گرداند) می‌گذراند و یک آرایه جدید از تنها آن مواردی که آزمون را گذرانده‌اند (مقدار true را برگردانده‌اند) را برمی‌گرداند.

You only want the items where `profession` is `'chemist'`. The "test" function for this looks like `(person) => person.profession === 'chemist'`. Here's how to put it together:
////
شما فقط مواردی را می خواهید که در آن حرفه `"شیمیدان"` است. تابع `"تست"` برای این به نظر می رسد (فرد) => person.profession === 'شیمیدان'. در اینجا نحوه کنار هم قرار دادن آن آمده است:
////
شما فقط می‌خواهید مواردی را که حرفه آنها `“شیمی‌دان”` است، نمایش دهید. تابع `“آزمون”` برای این کار به شکل `(person) => person.profession === ‘chemist’` به نظر می‌رسد. در ادامه، نحوه کار آن را مشاهده می‌کنید:

1. **Create** a new array of just “chemist” people, `chemists`, by calling `filter()` on the `people` filtering by `person.profession === 'chemist'`:

////
1. **با فراخوانی "filter()" در فیلتر "People" توسط "person.profession === “chemist”، آرایه جدیدی از افراد فقط“chemist”، “chemist” ایجاد کنید:

```js
const chemists = people.filter(person =>
  person.profession === 'chemist'
);
```

2. Now **map** over `chemists`:

////
2. اکنون **map** را روی `chemists` قرار دهید:

```js {1,13}
const listItems = chemists.map(person =>
  <li>
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
```

3. Lastly, **return** the `listItems` from your component:

////
3. در نهایت، `listItems` را از component خود **برگردانید**:

```js
return <ul>{listItems}</ul>;
```

<Sandpack>

```js App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const listItems = chemists.map(person =>
    <li>
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
  return <ul>{listItems}</ul>;
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

<Pitfall>

Arrow functions implicitly return the expression right after `=>`, so you didn't need a `return` statement:
////
توابع arrow به طور ضمنی عبارت را دقیقاً بعد از `=>` برمی گردانند، بنابراین شما نیازی به عبارت 'return' ندارید:

```js
const listItems = chemists.map(person =>
  <li>...</li> // Implicit return!
);
```

However, **you must write `return` explicitly if your `=>` is followed by a `{` curly brace!**
////
با این حال، **اگر پس از `=>` شما، یک `{` پرانتز وجود دارد، باید «بازگشت» را به صراحت بنویسید!**

```js
const listItems = chemists.map(person => { // Curly brace
  return <li>...</li>;
});
```

Arrow functions containing `=> {` are said to have a ["block body".](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#function_body) They let you write more than a single line of code, but you *have to* write a `return` statement yourself. If you forget it, nothing gets returned!
////
توابع پیکان حاوی `=> {` گفته می شود که دارای "بدنه بلوکی" هستند. آنها به شما اجازه می دهند بیش از یک خط کد بنویسید، اما باید خودتان یک عبارت بازگشتی بنویسید. اگر آن را فراموش کنید، هیچ چیز برگردانده نمی شود!

</Pitfall>

## Keeping list items in order with `key`(به ترتیب نگه داشتن آیتم های لیست توسط کلید) {/*keeping-list-items-in-order-with-key*/}

Notice that all the sandboxes above show an error in the console:
////
توجه داشته باشید که تمام جعبه های ماسه ای(sandboxes) بالا یک خطا را در کنسول نشان می دهند:

<ConsoleBlock level="error">

Warning: Each child in a list should have a unique "key" prop.

</ConsoleBlock>

You need to give each array item a `key` -- a string or a number that uniquely identifies it among other items in that array:
////
شما باید به هر مورد آرایه یک key بدهید - یک رشته یا یک عدد که بین موارد دیگر در آن آرایه به صورت منحصر به فرد شناسایی شود:

```js
<li key={person.id}>...</li>
```

<Note>

JSX elements directly inside a `map()` call always need keys!
////
عناصر JSX مستقیماً در فراخوانی map() همیشه به کلید نیاز دارند!

</Note>

Keys tell React which array item each component corresponds to, so that it can match them up later. This becomes important if your array items can move (e.g. due to sorting), get inserted, or get deleted. A well-chosen `key` helps React infer what exactly has happened, and make the correct updates to the DOM tree.

Rather than generating keys on the fly, you should include them in your data:
////
کلیدها به React می‌گویند که هر جزء با کدام آیتم آرایه مطابقت دارد، تا بعداً بتواند آنها را مطابقت دهد. اگر آیتم های آرایه شما می توانند جابجا شوند (مثلاً به دلیل مرتب سازی)، درج شوند یا حذف شوند، این مهم می شود. یک «کلید» به خوبی انتخاب شده به React کمک می‌کند تا بفهمد دقیقاً چه اتفاقی افتاده است و به‌روزرسانی‌های صحیح درخت DOM را انجام دهد.

به جای تولید کلیدها، باید آنها را در داده های خود بگنجانید:

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
        <b>{person.name}</b>
          {' ' + person.profession + ' '}
          known for {person.accomplishment}
      </p>
    </li>
  );
  return <ul>{listItems}</ul>;
}
```

```js data.js active
export const people = [{
  id: 0, // Used in JSX as a key
  name: 'Creola Katherine Johnson',
  profession: 'mathematician',
  accomplishment: 'spaceflight calculations',
  imageId: 'MK3eW3A'
}, {
  id: 1, // Used in JSX as a key
  name: 'Mario José Molina-Pasquel Henríquez',
  profession: 'chemist',
  accomplishment: 'discovery of Arctic ozone hole',
  imageId: 'mynHUSa'
}, {
  id: 2, // Used in JSX as a key
  name: 'Mohammad Abdus Salam',
  profession: 'physicist',
  accomplishment: 'electromagnetism theory',
  imageId: 'bE7W1ji'
}, {
  id: 3, // Used in JSX as a key
  name: 'Percy Lavon Julian',
  profession: 'chemist',
  accomplishment: 'pioneering cortisone drugs, steroids and birth control pills',
  imageId: 'IOjWm71'
}, {
  id: 4, // Used in JSX as a key
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

<DeepDive>

#### Displaying several DOM nodes for each list item(نمایش چندین گره DOM برای هر آیتم لیست) {/*displaying-several-dom-nodes-for-each-list-item*/}

What do you do when each item needs to render not one, but several DOM nodes?
////
وقتی هر آیتم باید نه یک، بلکه چندین گره DOM را ارائه کند، چه کار می کنید؟

The short [`<>...</>` Fragment](/reference/react/Fragment) syntax won't let you pass a key, so you need to either group them into a single `<div>`, or use the slightly longer and [more explicit `<Fragment>` syntax:](/reference/react/Fragment#rendering-a-list-of-fragments)
////
متن زیر را به فارسی ترجمه کنید: “سینتکس کوتاه <></> اجازه نمی دهد که یک کلید را انتقال دهید، بنابراین شما باید آنها را در یک `<div>` تجمیع کنید یا از سینتکس `<Fragment>` که کمی بلندتر و صریح تر است، استفاده کنید.”

به دلیل عدم پشتیبانی از کلید در سینتکس کوتاه <></>، شما باید آنها را در یک `<div>` تجمیع کنید یا از سینتکس `<Fragment>` که کمی بلندتر و صریح تر است، استفاده کنید1

```js
import { Fragment } from 'react';

// ...

const listItems = people.map(person =>
  <Fragment key={person.id}>
    <h1>{person.name}</h1>
    <p>{person.bio}</p>
  </Fragment>
);
```

Fragments disappear from the DOM, so this will produce a flat list of `<h1>`, `<p>`, `<h1>`, `<p>`, and so on.
////
 Fragments از DOM ناپدید می شوند، بنابراین این باعث تولید یک لیست صاف از `<h1>`, `<p>`, `<h1>`, `<p>` و غیره می شود.”

به دلیل اینکه سینتکس کوتاه <></> اجازه نمی دهد که یک کلید را انتقال دهید، شما باید آنها را در یک `<div>` تجمیع کنید یا از سینتکس `<Fragment>` که کمی بلندتر و صریح تر است، استفاده کنید1

</DeepDive>

### Where to get your `key` {/*where-to-get-your-key*/}

Different sources of data provide different sources of keys:
////
منابع مختلف داده منابع مختلفی از کلیدها را ارائه می دهند:

* **Data from a database:** If your data is coming from a database, you can use the database keys/IDs, which are unique by nature.
* **Locally generated data:** If your data is generated and persisted locally (e.g. notes in a note-taking app), use an incrementing counter, [`crypto.randomUUID()`](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/randomUUID) or a package like [`uuid`](https://www.npmjs.com/package/uuid) when creating items.

////
داده‌های پایگاه داده: اگر داده‌های شما از پایگاه داده می‌آیند، می‌توانید از کلیدها/شناسه‌های پایگاه داده استفاده کنید که طبیعتاً منحصربه‌فرد هستند.
داده‌های محلی تولید شده: اگر داده‌های شما به صورت محلی تولید شده و باقی می‌ماند (مثلاً یادداشت‌ها در یک برنامه یادداشت‌برداری)، هنگام ایجاد موارد از یک شمارنده افزایشی، crypto.randomUUID() یا بسته‌ای مانند uuid استفاده کنید.

### Rules of keys(قوانین کلیدها) {/*rules-of-keys*/}

* **Keys must be unique among siblings.** However, it’s okay to use the same keys for JSX nodes in _different_ arrays.
* **Keys must not change** or that defeats their purpose! Don't generate them while rendering.

////
* **کلیدها باید در بین خواهر و برادرها منحصر به فرد باشند.** با این حال، استفاده از کلیدهای مشابه برای گره‌های JSX در آرایه‌های _مختلف، اشکالی ندارد.
* **کلیدها نباید تغییر کنند** یا این هدف آنها را شکست می دهد! آنها را هنگام رندر تولید نکنید.

////
متن زیر را به فارسی ترجمه کنید: "* کلیدها باید بین همان خواهران منحصر به فرد باشند. با این حال، استفاده از همان کلیدها برای گره های JSX در آرایه های مختلف مجاز است.

کلیدها نباید تغییر کنند وگرنه هدف آنها از بین می رود! آنها را در هنگام رندرینگ تولید نکنید."
به دلیل اینکه سینتکس کوتاه <></> اجازه نمی دهد که یک کلید را انتقال دهید، شما باید آنها را در یک `<div>` تجمیع کنید یا از سینتکس `<Fragment>` که کمی بلندتر و صریح تر است، استفاده کنید1

### Why does React need keys?(چرا React به کلید نیاز دارد؟) {/*why-does-react-need-keys*/}

Imagine that files on your desktop didn't have names. Instead, you'd refer to them by their order -- the first file, the second file, and so on. You could get used to it, but once you delete a file, it would get confusing. The second file would become the first file, the third file would be the second file, and so on.

File names in a folder and JSX keys in an array serve a similar purpose. They let us uniquely identify an item between its siblings. A well-chosen key provides more information than the position within the array. Even if the _position_ changes due to reordering, the `key` lets React identify the item throughout its lifetime.
////
تصور کنید که فایل های روی دسکتاپ شما نامی ندارند. در عوض، شما به ترتیب آنها به آنها مراجعه می کنید - فایل اول، فایل دوم و غیره. شما می توانید به آن عادت کنید، اما هنگامی که یک فایل را حذف می کنید، گیج کننده می شود. فایل دوم تبدیل به اولین فایل، فایل سوم فایل دوم و غیره می شود.

نام فایل ها در یک پوشه و کلیدهای JSX در یک آرایه هدف مشابهی دارند. آنها به ما این امکان را می دهند که یک مورد را بین خواهر و برادرش به طور منحصر به فرد شناسایی کنیم. یک کلید خوب انتخاب شده اطلاعات بیشتری نسبت به موقعیت درون آرایه ارائه می دهد. حتی اگر _position_ به دلیل مرتب‌سازی مجدد تغییر کند، «کلید» به React اجازه می‌دهد تا مورد را در طول عمر خود شناسایی کند.
////
به دلیل اینکه فایل های روی دسکتاپ شما نام ندارند، به جای آنها به ترتیب آنها ارجاع می دهید - فایل اول، فایل دوم و غیره. شما ممکن است به آن عادت کنید، اما یکبار که یک فایل را حذف کنید، گیج کننده خواهد شد. فایل دوم به فایل اول تبدیل می شود، فایل سوم به فایل دوم و غیره.

نام فایل ها در یک پوشه و کلید JSX در یک آرایه هدف مشابهی دارند. آنها به ما اجازه می دهند یک مورد را بین خواهران خود به صورت منحصر به فرد شناسایی کنیم. یک کلید مناسب اطلاعات بیشتری را نسبت به موقعیت در آرایه فراهم می کند. حتی اگر موقعیت به دلیل تغییر ترتیب تغییر کند، key به React اجازه می دهد که مورد را در طول عمر خود شناسایی کند1
<Pitfall>

You might be tempted to use an item's index in the array as its key. In fact, that's what React will use if you don't specify a `key` at all. But the order in which you render items will change over time if an item is inserted, deleted, or if the array gets reordered. Index as a key often leads to subtle and confusing bugs.

Similarly, do not generate keys on the fly, e.g. with `key={Math.random()}`. This will cause keys to never match up between renders, leading to all your components and DOM being recreated every time. Not only is this slow, but it will also lose any user input inside the list items. Instead, use a stable ID based on the data.

Note that your components won't receive `key` as a prop. It's only used as a hint by React itself. If your component needs an ID, you have to pass it as a separate prop: `<Profile key={id} userId={id} />`.
////
ممکن است وسوسه شوید که از شاخص یک آیتم در آرایه به عنوان کلید آن استفاده کنید. در واقع، این همان چیزی است که React از آن استفاده خواهد کرد اگر شما اصلاً کلیدی را مشخص نکنید. اما اگر یک آیتم درج شود، حذف شود، یا اگر آرایه مرتب شود، ترتیبی که آیتم‌ها را بر اساس آن رندر می‌کنید در طول زمان تغییر می‌کند. ایندکس به عنوان یک کلید اغلب منجر به اشکالات ظریف و گیج کننده می شود.

به طور مشابه، کلیدها را در لحظه تولید نکنید، به عنوان مثال. با `key={Math.random()}.` این باعث می شود که کلیدها هرگز بین رندرها مطابقت نداشته باشند و هر بار همه اجزا و DOM شما دوباره ایجاد شوند. این نه تنها کند است، بلکه هرگونه ورودی کاربر را در فهرست موارد از دست خواهد داد. در عوض، از یک شناسه پایدار بر اساس داده ها استفاده کنید.

توجه داشته باشید که اجزای شما کلید را به عنوان یک پایه دریافت نمی کنند. این فقط به عنوان یک اشاره توسط خود React استفاده می شود. اگر شما
////
به دلیل اینکه شما ممکن است تمایل داشته باشید از شاخص یک مورد در آرایه به عنوان کلید استفاده کنید. در واقع، اگر کلیدی را مشخص نکنید، React از شاخص آن استفاده خواهد کرد. اما ترتیبی که شما موارد را رندر می کنید، در صورتی که یک مورد درج، حذف شود یا آرایه مرتب شود، با گذر زمان تغییر خواهد کرد. استفاده از شاخص به عنوان کلید، معمولاً منجر به باگ های پیچیده و گیج کننده می شود.

به طور مشابه، کلیدها را در حالت رو به رو تولید نکنید، به عنوان مثال با `key={Math.random()}`. این باعث می شود کلیدها هرگز با هم مطابقت نداشته باشند، منجر به بازسازی همه مؤلفه ها و DOM شما در هر بار رندرینگ می شود. این کند نه تنها است، بلکه هرگز ورودی کاربر در داخل موارد لیست را نیز از دست خواهد داد. به جای آن، از یک شناسه پایدار بر اساس داده ها استفاده کنید.

توجه کنید که مؤلفه های شما `key` را به عنوان یک prop دریافت نمی کنند. فقط به عنوان یک راهنمایی توسط React استفاده می شود. اگر مؤلفه شما به یک شناسه نیاز دارد، باید آن را به عنوان یک prop جداگانه منتقل کنید: `<Profile key={id} userId={id} />`.

</Pitfall>

<Recap> 

On this page you learned:

* How to move data out of components and into data structures like arrays and objects.
* How to generate sets of similar components with JavaScript's `map()`.
* How to create arrays of filtered items with JavaScript's `filter()`.
* Why and how to set `key` on each component in a collection so React can keep track of each of them even if their position or data changes.

////
در این صفحه یاد گرفتید:

* نحوه انتقال داده ها از components و به ساختارهای داده مانند آرایه ها و اشیاء.
* نحوه تولید مجموعه ای از components های مشابه با 'map()' جاوا اسکریپت.
* نحوه ایجاد آرایه هایی از آیتم های فیلتر شده با «filter()» جاوا اسکریپت.
* چرا و چگونه «کلید» را روی هر components در مجموعه تنظیم کنیم تا React بتواند هر یک از آنها را ردیابی کند، حتی اگر موقعیت یا داده های آنها تغییر کند.
</Recap>



<Challenges>

#### Splitting a list in two {/*splitting-a-list-in-two*/}

This example shows a list of all people.

Change it to show two separate lists one after another: **Chemists** and **Everyone Else.** Like previously, you can determine whether a person is a chemist by checking if `person.profession === 'chemist'`.

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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

<Solution>

You could use `filter()` twice, creating two separate arrays, and then `map` over both of them:

<Sandpack>

```js App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const everyoneElse = people.filter(person =>
    person.profession !== 'chemist'
  );
  return (
    <article>
      <h1>Scientists</h1>
      <h2>Chemists</h2>
      <ul>
        {chemists.map(person =>
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
        )}
      </ul>
      <h2>Everyone Else</h2>
      <ul>
        {everyoneElse.map(person =>
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
        )}
      </ul>
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

In this solution, the `map` calls are placed directly inline into the parent `<ul>` elements, but you could introduce variables for them if you find that more readable.

There is still a bit duplication between the rendered lists. You can go further and extract the repetitive parts into a `<ListSection>` component:

<Sandpack>

```js App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

function ListSection({ title, people }) {
  return (
    <>
      <h2>{title}</h2>
      <ul>
        {people.map(person =>
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
        )}
      </ul>
    </>
  );
}

export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const everyoneElse = people.filter(person =>
    person.profession !== 'chemist'
  );
  return (
    <article>
      <h1>Scientists</h1>
      <ListSection
        title="Chemists"
        people={chemists}
      />
      <ListSection
        title="Everyone Else"
        people={everyoneElse}
      />
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

A very attentive reader might notice that with two `filter` calls, we check each person's profession twice. Checking a property is very fast, so in this example it's fine. If your logic was more expensive than that, you could replace the `filter` calls with a loop that manually constructs the arrays and checks each person once.

In fact, if `people` never change, you could move this code out of your component. From React's perspective, all that matters is that you give it an array of JSX nodes in the end. It doesn't care how you produce that array:

<Sandpack>

```js App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

let chemists = [];
let everyoneElse = [];
people.forEach(person => {
  if (person.profession === 'chemist') {
    chemists.push(person);
  } else {
    everyoneElse.push(person);
  }
});

function ListSection({ title, people }) {
  return (
    <>
      <h2>{title}</h2>
      <ul>
        {people.map(person =>
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
        )}
      </ul>
    </>
  );
}

export default function List() {
  return (
    <article>
      <h1>Scientists</h1>
      <ListSection
        title="Chemists"
        people={chemists}
      />
      <ListSection
        title="Everyone Else"
        people={everyoneElse}
      />
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

</Solution>

#### Nested lists in one component {/*nested-lists-in-one-component*/}

Make a list of recipes from this array! For each recipe in the array, display its name as an `<h2>` and list its ingredients in a `<ul>`.

<Hint>

This will require nesting two different `map` calls.

</Hint>

<Sandpack>

```js App.js
import { recipes } from './data.js';

export default function RecipeList() {
  return (
    <div>
      <h1>Recipes</h1>
    </div>
  );
}
```

```js data.js
export const recipes = [{
  id: 'greek-salad',
  name: 'Greek Salad',
  ingredients: ['tomatoes', 'cucumber', 'onion', 'olives', 'feta']
}, {
  id: 'hawaiian-pizza',
  name: 'Hawaiian Pizza',
  ingredients: ['pizza crust', 'pizza sauce', 'mozzarella', 'ham', 'pineapple']
}, {
  id: 'hummus',
  name: 'Hummus',
  ingredients: ['chickpeas', 'olive oil', 'garlic cloves', 'lemon', 'tahini']
}];
```

</Sandpack>

<Solution>

Here is one way you could go about it:

<Sandpack>

```js App.js
import { recipes } from './data.js';

export default function RecipeList() {
  return (
    <div>
      <h1>Recipes</h1>
      {recipes.map(recipe =>
        <div key={recipe.id}>
          <h2>{recipe.name}</h2>
          <ul>
            {recipe.ingredients.map(ingredient =>
              <li key={ingredient}>
                {ingredient}
              </li>
            )}
          </ul>
        </div>
      )}
    </div>
  );
}
```

```js data.js
export const recipes = [{
  id: 'greek-salad',
  name: 'Greek Salad',
  ingredients: ['tomatoes', 'cucumber', 'onion', 'olives', 'feta']
}, {
  id: 'hawaiian-pizza',
  name: 'Hawaiian Pizza',
  ingredients: ['pizza crust', 'pizza sauce', 'mozzarella', 'ham', 'pineapple']
}, {
  id: 'hummus',
  name: 'Hummus',
  ingredients: ['chickpeas', 'olive oil', 'garlic cloves', 'lemon', 'tahini']
}];
```

</Sandpack>

Each of the `recipes` already includes an `id` field, so that's what the outer loop uses for its `key`. There is no ID you could use to loop over ingredients. However, it's reasonable to assume that the same ingredient won't be listed twice within the same recipe, so its name can serve as a `key`. Alternatively, you could change the data structure to add IDs, or use index as a `key` (with the caveat that you can't safely reorder ingredients).

</Solution>

#### Extracting a list item component {/*extracting-a-list-item-component*/}

This `RecipeList` component contains two nested `map` calls. To simplify it, extract a `Recipe` component from it which will accept `id`, `name`, and `ingredients` props. Where do you place the outer `key` and why?

<Sandpack>

```js App.js
import { recipes } from './data.js';

export default function RecipeList() {
  return (
    <div>
      <h1>Recipes</h1>
      {recipes.map(recipe =>
        <div key={recipe.id}>
          <h2>{recipe.name}</h2>
          <ul>
            {recipe.ingredients.map(ingredient =>
              <li key={ingredient}>
                {ingredient}
              </li>
            )}
          </ul>
        </div>
      )}
    </div>
  );
}
```

```js data.js
export const recipes = [{
  id: 'greek-salad',
  name: 'Greek Salad',
  ingredients: ['tomatoes', 'cucumber', 'onion', 'olives', 'feta']
}, {
  id: 'hawaiian-pizza',
  name: 'Hawaiian Pizza',
  ingredients: ['pizza crust', 'pizza sauce', 'mozzarella', 'ham', 'pineapple']
}, {
  id: 'hummus',
  name: 'Hummus',
  ingredients: ['chickpeas', 'olive oil', 'garlic cloves', 'lemon', 'tahini']
}];
```

</Sandpack>

<Solution>

You can copy-paste the JSX from the outer `map` into a new `Recipe` component and return that JSX. Then you can change `recipe.name` to `name`, `recipe.id` to `id`, and so on, and pass them as props to the `Recipe`:

<Sandpack>

```js
import { recipes } from './data.js';

function Recipe({ id, name, ingredients }) {
  return (
    <div>
      <h2>{name}</h2>
      <ul>
        {ingredients.map(ingredient =>
          <li key={ingredient}>
            {ingredient}
          </li>
        )}
      </ul>
    </div>
  );
}

export default function RecipeList() {
  return (
    <div>
      <h1>Recipes</h1>
      {recipes.map(recipe =>
        <Recipe {...recipe} key={recipe.id} />
      )}
    </div>
  );
}
```

```js data.js
export const recipes = [{
  id: 'greek-salad',
  name: 'Greek Salad',
  ingredients: ['tomatoes', 'cucumber', 'onion', 'olives', 'feta']
}, {
  id: 'hawaiian-pizza',
  name: 'Hawaiian Pizza',
  ingredients: ['pizza crust', 'pizza sauce', 'mozzarella', 'ham', 'pineapple']
}, {
  id: 'hummus',
  name: 'Hummus',
  ingredients: ['chickpeas', 'olive oil', 'garlic cloves', 'lemon', 'tahini']
}];
```

</Sandpack>

Here, `<Recipe {...recipe} key={recipe.id} />` is a syntax shortcut saying "pass all properties of the `recipe` object as props to the `Recipe` component". You could also write each prop explicitly: `<Recipe id={recipe.id} name={recipe.name} ingredients={recipe.ingredients} key={recipe.id} />`.

**Note that the `key` is specified on the `<Recipe>` itself rather than on the root `<div>` returned from `Recipe`.** This is because this `key` is needed directly within the context of the surrounding array. Previously, you had an array of `<div>`s so each of them needed a `key`, but now you have an array of `<Recipe>`s. In other words, when you extract a component, don't forget to leave the `key` outside the JSX you copy and paste.

</Solution>

#### List with a separator {/*list-with-a-separator*/}

This example renders a famous haiku by Tachibana Hokushi, with each line wrapped in a `<p>` tag. Your job is to insert an `<hr />` separator between each paragraph. Your resulting structure should look like this:

```js
<article>
  <p>I write, erase, rewrite</p>
  <hr />
  <p>Erase again, and then</p>
  <hr />
  <p>A poppy blooms.</p>
</article>
```

A haiku only contains three lines, but your solution should work with any number of lines. Note that `<hr />` elements only appear *between* the `<p>` elements, not in the beginning or the end!

<Sandpack>

```js
const poem = {
  lines: [
    'I write, erase, rewrite',
    'Erase again, and then',
    'A poppy blooms.'
  ]
};

export default function Poem() {
  return (
    <article>
      {poem.lines.map((line, index) =>
        <p key={index}>
          {line}
        </p>
      )}
    </article>
  );
}
```

```css
body {
  text-align: center;
}
p {
  font-family: Georgia, serif;
  font-size: 20px;
  font-style: italic;
}
hr {
  margin: 0 120px 0 120px;
  border: 1px dashed #45c3d8;
}
```

</Sandpack>

(This is a rare case where index as a key is acceptable because a poem's lines will never reorder.)

<Hint>

You'll either need to convert `map` to a manual loop, or use a fragment.

</Hint>

<Solution>

You can write a manual loop, inserting `<hr />` and `<p>...</p>` into the output array as you go:

<Sandpack>

```js
const poem = {
  lines: [
    'I write, erase, rewrite',
    'Erase again, and then',
    'A poppy blooms.'
  ]
};

export default function Poem() {
  let output = [];

  // Fill the output array
  poem.lines.forEach((line, i) => {
    output.push(
      <hr key={i + '-separator'} />
    );
    output.push(
      <p key={i + '-text'}>
        {line}
      </p>
    );
  });
  // Remove the first <hr />
  output.shift();

  return (
    <article>
      {output}
    </article>
  );
}
```

```css
body {
  text-align: center;
}
p {
  font-family: Georgia, serif;
  font-size: 20px;
  font-style: italic;
}
hr {
  margin: 0 120px 0 120px;
  border: 1px dashed #45c3d8;
}
```

</Sandpack>

Using the original line index as a `key` doesn't work anymore because each separator and paragraph are now in the same array. However, you can give each of them a distinct key using a suffix, e.g. `key={i + '-text'}`.

Alternatively, you could render a collection of fragments which contain `<hr />` and `<p>...</p>`. However, the `<>...</>` shorthand syntax doesn't support passing keys, so you'd have to write `<Fragment>` explicitly:

<Sandpack>

```js
import { Fragment } from 'react';

const poem = {
  lines: [
    'I write, erase, rewrite',
    'Erase again, and then',
    'A poppy blooms.'
  ]
};

export default function Poem() {
  return (
    <article>
      {poem.lines.map((line, i) =>
        <Fragment key={i}>
          {i > 0 && <hr />}
          <p>{line}</p>
        </Fragment>
      )}
    </article>
  );
}
```

```css
body {
  text-align: center;
}
p {
  font-family: Georgia, serif;
  font-size: 20px;
  font-style: italic;
}
hr {
  margin: 0 120px 0 120px;
  border: 1px dashed #45c3d8;
}
```

</Sandpack>

Remember, fragments (often written as `<> </>`) let you group JSX nodes without adding extra `<div>`s!

</Solution>

</Challenges>
