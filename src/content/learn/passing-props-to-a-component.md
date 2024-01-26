---
title: Passing Props to a Component(ارسال پراپ به یک کامپوننت)
---

<Intro>

React components use *props* to communicate with each other. Every parent component can pass some information to its child components by giving them props. Props might remind you of HTML attributes, but you can pass any JavaScript value through them, including objects, arrays, and functions.
////
React از props برای ارتباط بین کامپوننت‌ها استفاده می‌کند. هر کامپوننت والد می‌تواند با دادن props، برخی اطلاعات را به کامپوننت‌های فرزند خود منتقل کند. Props ممکن است به شما یادآوری ویژگی‌های HTML شوند، اما شما می‌توانید هر مقدار JavaScript را از طریق آنها منتقل کنید، از جمله شیء، آرایه و تابع1.
</Intro>

<YouWillLearn>

* How to pass props to a component
* How to read props from a component
* How to specify default values for props
* How to pass some JSX to a component
* How props change over time
////
این متن به فارسی می‌شود:

چگونه props را به یک کامپوننت منتقل کنیم
چگونه از props در یک کامپوننت بخوانیم
چگونه مقدار پیش‌فرض برای props تعیین کنیم
چگونه برخی از JSX را به یک کامپوننت منتقل کنیم
چگونه props در طول زمان تغییر می‌کنند1.

</YouWillLearn>

## Familiar props {/*familiar-props*/}

Props are the information that you pass to a JSX tag. For example, `className`, `src`, `alt`, `width`, and `height` are some of the props you can pass to an `<img>`:
////
Props به عنوان اطلاعاتی که به یک تگ JSX منتقل می‌شوند، شناخته می‌شوند. به عنوان مثال، className، src، alt، width و height از میان props هایی هستند که می‌توانید به `<img>` منتقل کنید1.
<Sandpack>

```js
function Avatar() {
  return (
    <img
      className="avatar"
      src="https://i.imgur.com/1bX5QH6.jpg"
      alt="Lin Lanying"
      width={100}
      height={100}
    />
  );
}

export default function Profile() {
  return (
    <Avatar />
  );
}
```

```css
body { min-height: 120px; }
.avatar { margin: 20px; border-radius: 50%; }
```

</Sandpack>

The props you can pass to an `<img>` tag are predefined (ReactDOM conforms to [the HTML standard](https://www.w3.org/TR/html52/semantics-embedded-content.html#the-img-element)). But you can pass any props to *your own* components, such as `<Avatar>`, to customize them. Here's how!
////
Props به عنوان اطلاعاتی که به یک تگ JSX منتقل می‌شوند، شناخته می‌شوند. به عنوان مثال، className، src، alt، width و height از میان props هایی هستند که می‌توانید به `<img>` منتقل کنید1. با این حال، شما می‌توانید هر props دیگری را به کامپوننت‌های خود، مانند `<Avatar>`، منتقل کنید تا آنها را سفارشی کنید. روش زیر نحوه انجام این کار را نشان می‌دهد1.

## Passing props to a component(ارسال پراپ به یک کامپوننت) {/*passing-props-to-a-component*/}

In this code, the `Profile` component isn't passing any props to its child component, `Avatar`:
////
در این کد، کامپوننت Profile هیچ props را به کامپوننت فرزند خود، Avatar، منتقل نمی‌کند1.

```js
export default function Profile() {
  return (
    <Avatar />
  );
}
```

You can give `Avatar` some props in two steps.
////
You can give 'Avatar' some props in two steps به فارسی می‌شود: شما می‌توانید در دو مرحله برخی از 'props' را به 'Avatar' بدهید.1.

### Step 1: Pass props to the child component(ارسال پراپ به کامپوننت فرزند) {/*step-1-pass-props-to-the-child-component*/}

First, pass some props to `Avatar`. For example, let's pass two props: `person` (an object), and `size` (a number):
////
ابتدا، برخی از لوازم را به «آواتار» منتقل کنید. به عنوان مثال، اجازه دهید دو پروپوزال را ارسال کنیم: "person" (یک شی) و "size" (یک عدد):
////
First, pass some props to 'Avatar'. For example, let's pass two props: 'person' (an object), and 'size' (a number):' به فارسی می‌شود: ابتدا، برخی از 'props' را به 'Avatar' منتقل کنید. به عنوان مثال، بیایید دو 'props' را منتقل کنیم: 'person' (یک شیء) و 'size' (یک عدد):1.

```js
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}
```

<Note>

If double curly braces after `person=` confuse you, recall [they're merely an object](/learn/javascript-in-jsx-with-curly-braces#using-double-curlies-css-and-other-objects-in-jsx) inside the JSX curlies.
////
اگر پرانتزهای دوتایی پس از `«person=»` شما را گیج می‌کند، به یاد بیاورید [آنها صرفاً یک شی هستند](/learn/javascript-in-jsx-with-curly-braces#using-double-curlies-css-and-other-objects -in-jsx) در داخل پیچ های JSX.
////
If double curly braces after `'person='` confuse you, recall they're merely an object inside the JSX curlies. به فارسی می‌شود: اگر برای شما دو براکت زمینه‌ای پس از `'person='` گیج‌کننده است، به یاد داشته باشید که آنها فقط یک شیء هستند داخل براکت‌های زمینه‌ای JSX.1.

</Note>

Now you can read these props inside the `Avatar` component.
////
اکنون می‌توانید این ویژگی‌ها را در مولفه «آواتار» بخوانید.
////
حالا شما می توانید این props در داخل جزء Avatar به عنوان خوانده شده1 .

### Step 2: Read props inside the child component(خواندن پراپ داخل کامپوننت فرزند) {/*step-2-read-props-inside-the-child-component*/}

You can read these props by listing their names `person, size` separated by the commas inside `({` and `})` directly after `function Avatar`. This lets you use them inside the `Avatar` code, like you would with a variable.
////
می‌توانید با فهرست کردن نام‌های «شخص، اندازه» که با کاما در داخل `«({» و «})»` مستقیماً بعد از «عملکرد آواتار» از هم جدا شده‌اند، بخوانید. این به شما امکان می دهد از آنها در داخل کد `«آواتار»` استفاده کنید، مانند یک متغیر.
////
شما می‌توانید با لیست کردن نام‌های `'person'` و `'size'` که با کاما از هم جدا شده‌اند، داخل `({و})`بعد ازfunction Avatar، این propsرا بخوانید. این کار به شما اجازه می‌دهد که آنها را در داخل کدAvatar` استفاده کنید، مانند یک متغیر1.

```js
function Avatar({ person, size }) {
  // person and size are available here
}
```

Add some logic to `Avatar` that uses the `person` and `size` props for rendering, and you're done.

Now you can configure `Avatar` to render in many different ways with different props. Try tweaking the values!
////
مقداری منطق به `«آواتار»` اضافه کنید که از ویژگی‌های `«person»` و `«size»` برای رندر استفاده می‌کند، و کارتان تمام شد.

اکنون می‌توانید `«آواتار»` را برای رندر کردن به روش‌های مختلف با ابزارهای مختلف پیکربندی کنید. سعی کنید مقادیر را تغییر دهید!
////
منطق را اضافه کنید که از `‘props’` `‘person’` و `‘size’` برای رندر کردن استفاده می‌کند، و تمام شده‌اید.

حالا می‌توانید `‘Avatar’` را به چندین روش مختلف با `‘props’` های متفاوت پیکربندی کنید. سعی کنید مقادیر را تغییر دهید!`1.

<Sandpack>

```js App.js
import { getImageUrl } from './utils.js';

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

export default function Profile() {
  return (
    <div>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi', 
          imageId: 'YfeOqp2'
        }}
      />
      <Avatar
        size={80}
        person={{
          name: 'Aklilu Lemma', 
          imageId: 'OKS67lh'
        }}
      />
      <Avatar
        size={50}
        person={{ 
          name: 'Lin Lanying',
          imageId: '1bX5QH6'
        }}
      />
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
body { min-height: 120px; }
.avatar { margin: 10px; border-radius: 50%; }
```

</Sandpack>

Props let you think about parent and child components independently. For example, you can change the `person` or the `size` props inside `Profile` without having to think about how `Avatar` uses them. Similarly, you can change how the `Avatar` uses these props, without looking at the `Profile`.

You can think of props like "knobs" that you can adjust. They serve the same role as arguments serve for functions—in fact, props _are_ the only argument to your component! React component functions accept a single argument, a `props` object:
////
لوازم جانبی به شما امکان می دهد به طور مستقل در مورد اجزای والدین و فرزند فکر کنید. برای مثال، می‌توانید `«شخص»` یا `«اندازه»` را در داخل `«پروفایل»` تغییر دهید بدون اینکه نیازی به استفاده از `«آواتار»` از آنها باشید. به طور مشابه، می‌توانید نحوه استفاده `«آواتار»` از این لوازم را بدون نگاه کردن به `«نمایه»` تغییر دهید.

می توانید به وسایلی مانند `"شستی"` فکر کنید که بتوانید آنها را تنظیم کنید. آنها همان نقشی را ایفا می کنند که آرگومان ها برای توابع خدمت می کنند - در واقع، props _ هستند_ تنها آرگومان برای جزء شما! توابع کامپوننت React یک آرگومان منفرد، یک شی `«props»` را می پذیرند:
////
Props به عنوان اطلاعاتی که به یک تگ JSX منتقل می‌شوند، شناخته می‌شوند. به عنوان مثال، className، src، alt، width و height از میان props هایی هستند که می‌توانید به `<img>` منتقل کنید1. با استفاده از props، می‌توانید در مورد کامپوننت‌های والد و فرزند به صورت مستقل فکر کنید. به عنوان مثال، می‌توانید props person یا size را در داخل Profile تغییر دهید بدون اینکه نیاز به فکر کردن در مورد این باشد که Avatar از آنها استفاده می‌کند. به طور مشابه، می‌توانید نحوه استفاده Avatar از این props را تغییر دهید، بدون نگاه کردن به Profile.

می‌توانید props را مانند “دسته‌هایی” که می‌توانید تنظیم کنید، در نظر بگیرید. آنها همان نقشی را که آرگومان‌ها برای توابع ایفا می‌کنند، ایفا می‌کنند - در واقع، props تنها آرگومان کامپوننت شما هستند! توابع کامپوننت React یک آرگومان واحد، یک شیء props را می‌پذیرند1.

```js
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ...
}
```

Usually you don't need the whole `props` object itself, so you destructure it into individual props.
////
معمولاً شما به کل شیء `«props»` نیاز ندارید، بنابراین آن را به تک تک تک‌لوحه‌ها تخریب می‌کنید.
////
معمولاً شما نیازی به کل شیء propsندارید، بنابراین آن را بهprops های فردی تجزیه می کنید.1.

<Pitfall>

**Don't miss the pair of `{` and `}` curlies** inside of `(` and `)` when declaring props:
////
**هنگام اعلان برجستگی، جفت فرفری `«{» و «}»` را در داخل `«(» و «)»` از دست ندهید:
////
در زمان اعلام props، جفت `{و}`را در داخل`(و)` از دست ندهید.1.

```js
function Avatar({ person, size }) {
  // ...
}
```

This syntax is called ["destructuring"](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Unpacking_fields_from_objects_passed_as_a_function_parameter) and is equivalent to reading properties from a function parameter:
////
این نحو «تخریب‌سازی» نامیده می‌شود و معادل خواندن خواص از یک پارامتر تابع است:
////
این نحو نوشتاری به نام “تجزیه” شناخته می‌شود و معادل خواندن ویژگی‌ها از پارامتر تابع است[1][1].

```js
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ...
}
```

</Pitfall>

## Specifying a default value for a prop(تعیین یک مقدار پیش فرض برای یک prop) {/*specifying-a-default-value-for-a-prop*/}

If you want to give a prop a default value to fall back on when no value is specified, you can do it with the destructuring by putting `=` and the default value right after the parameter:
////
اگر می‌خواهید به یک prop یک مقدار پیش‌فرض بدهید تا زمانی که مقداری مشخص نشده است دوباره به آن بیفتد، می‌توانید با قرار دادن = و مقدار پیش‌فرض درست بعد از پارامتر، این کار را با destructuring انجام دهید:
////
اگر می‌خواهید به یک 'prop' مقدار پیش‌فرضی بدهید که در صورت عدم تعیین مقدار به آن بازگردید، می‌توانید با تجزیه آن با قرار دادن = و مقدار پیش‌فرضی درست بعد از پارامتر، این کار را انجام دهید:1.

```js
function Avatar({ person, size = 100 }) {
  // ...
}
```

Now, if `<Avatar person={...} />` is rendered with no `size` prop, the `size` will be set to `100`.

The default value is only used if the `size` prop is missing or if you pass `size={undefined}`. But if you pass `size={null}` or `size={0}`, the default value will **not** be used.
////
حال، اگر `<Avatar person={...} />` بدون سایز پایه رندر شود، اندازه روی 100 تنظیم می شود.

مقدار پیش‌فرض فقط در صورتی استفاده می‌شود که پایه اندازه وجود نداشته باشد یا اگر `size={undefined}` را ارسال کنید. اما اگر `size={null}` یا `size={0}` را پاس کنید، از مقدار پیش‌فرض استفاده نمی‌شود.
////
حالا، اگر `<Avatar person={...} />` بدون size prop` رندر شود، اندازه به 100 تنظیم می‌شود.

مقدار پیش‌فرض فقط در صورت عدم وجود size prop یا اگر شما `size={undefined}` را منتقل کنید، استفاده می‌شود. اما اگر شما `size={null}` یا `size={0}` را منتقل کنید، مقدار پیش‌فرض استفاده نخواهد شد.`1.

## Forwarding props with the JSX spread syntax(فوروارد کردن props با jsx spread syntax  JSX) {/*forwarding-props-with-the-jsx-spread-syntax*/}

Sometimes, passing props gets very repetitive:
////
گاهی اوقات، پاس کردن props بسیار تکراری می شود:
////
گاهی اوقات عبور props می شود بسیار تکراری1.

```js
function Profile({ person, size, isSepia, thickBorder }) {
  return (
    <div className="card">
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
    </div>
  );
}
```

There's nothing wrong with repetitive code—it can be more legible. But at times you may value conciseness. Some components forward all of their props to their children, like how this `Profile` does with `Avatar`. Because they don't use any of their props directly, it can make sense to use a more concise "spread" syntax:
////
هیچ مشکلی با کدهای تکراری وجود ندارد - می تواند خواناتر باشد. اما گاهی اوقات ممکن است برای مختصر بودن ارزش قائل شوید. برخی از مؤلفه‌ها همه لوازم خود را برای فرزندانشان ارسال می‌کنند، مانند نحوه عملکرد این نمایه با آواتار. از آنجایی که آنها مستقیماً از هیچ یک از ابزارهای خود استفاده نمی کنند، می تواند منطقی باشد که از یک نحو "گسترش" مختصرتر استفاده کنید:
////
هیچ مشکلی با کد تکراری وجود ندارد - ممکن است خوانایی بیشتری داشته باشد. اما در برخی موارد، ممکن است به کوتاهی ارزش بدهید. برخی از کامپوننت‌ها تمام props خود را به کامپوننت‌های فرزند خود منتقل می‌کنند، مانند این که این پروفایل با آواتار انجام می‌دهد. به دلیل اینکه هیچ یک از props خود را مستقیماً استفاده نمی‌کنند، استفاده از نحوه نوشتاری “spread” می‌تواند منطقی باشد1.

```js
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```

This forwards all of `Profile`'s props to the `Avatar` without listing each of their names.

**Use spread syntax with restraint.** If you're using it in every other component, something is wrong. Often, it indicates that you should split your components and pass children as JSX. More on that next!
////
با این کار تمام ابزارهای نمایه بدون فهرست کردن نام هر یک از آنها به آواتار ارسال می شود.

از نحو گسترش با محدودیت استفاده کنید. اگر از آن در هر جزء دیگر استفاده می کنید، مشکلی وجود دارد. اغلب، نشان می دهد که شما باید اجزای خود را تقسیم کنید و فرزندان را به عنوان JSX منتقل کنید. بیشتر در مورد آن بعدی!
////
`این متن به فارسی می‌شود: "این، تمام ‘props’ پروفایل را به آواتار منتقل می‌کند بدون لیست کردن نام هر یک از آنها.

از نحوه نوشتاری ‘spread’ با احتیاط استفاده کنید. اگر در هر کامپوننت دیگری از آن استفاده می‌کنید، چیزی اشتباه است. اغلب، این نشان می‌دهد که باید کامپوننت‌های خود را تقسیم کرده و فرزندان را به عنوان JSX منتقل کنید. در مورد این موضوع بیشتر صحبت خواهیم کرد!"`1.

## Passing JSX as children(ارسال jsx از فرزند) {/*passing-jsx-as-children*/}

It is common to nest built-in browser tags:
////
معمول است که تگ های مرورگر داخلی را در خود جای دهید:
////
معمول است که برچسب‌های داخلی مرورگر را لانه‌ای کنید1.

```js
<div>
  <img />
</div>
```

Sometimes you'll want to nest your own components the same way:
////
گاهی اوقات شما می خواهید اجزای خود را به همین ترتیب تودرتو کنید:
////
گاهی اوقات شما می خواهید اجزای خود را به همان شیوه لانه کنید1.

```js
<Card>
  <Avatar />
</Card>
```

When you nest content inside a JSX tag, the parent component will receive that content in a prop called `children`. For example, the `Card` component below will receive a `children` prop set to `<Avatar />` and render it in a wrapper div:
////
وقتی محتوایی را درون یک تگ JSX قرار می‌دهید، مؤلفه والد آن محتوا را در یک پایه به نام فرزند دریافت می‌کند. به عنوان مثال، مؤلفه Card در زیر یک پایه کودک را روی ‍ دریافت می‌کند و آن را در یک wrapper div ارائه می‌کند:
////
وقتی شما محتوا را درون یک تگ JSX لانه می‌کنید، کامپوننت والد آن محتوا را در یک prop به نام children دریافت می‌کند. به عنوان مثال، کامپوننت Card زیر، یک prop با نام children که به `<Avatar />` تنظیم شده است دریافت می‌کند و آن را در یک div پوشش دهنده رندر می‌کند: 1.

<Sandpack>

```js App.js
import Avatar from './Avatar.js';

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

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
```

```js Avatar.js
import { getImageUrl } from './utils.js';

export default function Avatar({ person, size }) {
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

Try replacing the `<Avatar>` inside `<Card>` with some text to see how the `Card` component can wrap any nested content. It doesn't need to "know" what's being rendered inside of it. You will see this flexible pattern in many places.

You can think of a component with a `children` prop as having a "hole" that can be "filled in" by its parent components with arbitrary JSX. You will often use the `children` prop for visual wrappers: panels, grids, etc.
////
سعی کنید `<Avatar>` داخل `<Card>` را با مقداری متن جایگزین کنید تا ببینید که چگونه مؤلفه Card می‌تواند محتوای تودرتو را بپیچد. نیازی نیست که بداند چه چیزی در داخل آن ارائه می شود. این الگوی انعطاف پذیر را در بسیاری از جاها خواهید دید.

می‌توانید یک کامپوننت با تکیه‌گاه کودکان را به‌عنوان یک «سوراخ» در نظر بگیرید که می‌تواند توسط مؤلفه‌های والد آن با JSX دلخواه «پر» شود. شما اغلب از تکیه گاه کودکان برای لفاف های بصری استفاده می کنید: پانل ها، شبکه ها و غیره.
////
به عنوان مثال، برای دیدن اینکه کامپوننت Card چگونه محتوای تو در تو را پوشش می‌دهد، `<Avatar>` را درون `<Card>` با یک متن جایگزین کنید. این کامپوننت نیازی به “دانستن” اینکه چه چیزی درون آن رندر می‌شود ندارد. این الگوی انعطاف پذیر را در بسیاری از جاها خواهید دید.

می‌توانید یک کامپوننت با children prop را به عنوان یک `“سوراخ”` با در نظر گرفتن JSX خود به وسیله کامپوننت‌های والد `“پر کنید”`. شما اغلب از children prop برای پوشش‌های بصری مانند پنل‌ها، شبکه‌ها و غیره استفاده می‌کنید.`1.

<Illustration src="/images/docs/illustrations/i_children-prop.png" alt='A puzzle-like Card tile with a slot for "children" pieces like text and Avatar' />

## How props change over time(چگونه props در طول زمان تغییر می کنند) {/*how-props-change-over-time*/}

The `Clock` component below receives two props from its parent component: `color` and `time`. (The parent component's code is omitted because it uses [state](/learn/state-a-components-memory), which we won't dive into just yet.)

Try changing the color in the select box below:
////
کامپوننت Clock زیر دو ویژگی را از مؤلفه اصلی خود دریافت می کند: رنگ و زمان. (کد مؤلفه والد حذف شده است زیرا از حالت استفاده می کند، که ما هنوز به آن نمی پردازیم.)

سعی کنید رنگ را در کادر انتخاب زیر تغییر دهید:


Clock.js
////
کامپوننت Clock زیر دو prop با نام‌های color و time را از کامپوننت والد خود دریافت می‌کند. (کد کامپوننت والد به دلیل استفاده از state که هنوز به آن نپرداخته‌ایم، حذف شده است.)

برای تغییر رنگ، رنگ مورد نظر خود را در جعبه انتخاب زیر تغییر دهید:

Clock.js

<Sandpack>

```js Clock.js active
export default function Clock({ color, time }) {
  return (
    <h1 style={{ color: color }}>
      {time}
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
  const [color, setColor] = useState('lightcoral');
  return (
    <div>
      <p>
        Pick a color:{' '}
        <select value={color} onChange={e => setColor(e.target.value)}>
          <option value="lightcoral">lightcoral</option>
          <option value="midnightblue">midnightblue</option>
          <option value="rebeccapurple">rebeccapurple</option>
        </select>
      </p>
      <Clock color={color} time={time.toLocaleTimeString()} />
    </div>
  );
}
```

</Sandpack>

This example illustrates that **a component may receive different props over time.** Props are not always static! Here, the `time` prop changes every second, and the `color` prop changes when you select another color. Props reflect a component's data at any point in time, rather than only in the beginning.

However, props are [immutable](https://en.wikipedia.org/wiki/Immutable_object)—a term from computer science meaning "unchangeable". When a component needs to change its props (for example, in response to a user interaction or new data), it will have to "ask" its parent component to pass it _different props_—a new object! Its old props will then be cast aside, and eventually the JavaScript engine will reclaim the memory taken by them.

**Don't try to "change props".** When you need to respond to the user input (like changing the selected color), you will need to "set state", which you can learn about in [State: A Component's Memory.](/learn/state-a-components-memory)
////
این مثال نشان می‌دهد که یک کامپوننت ممکن است در طول زمان پروپوزال‌های مختلفی دریافت کند. وسایل همیشه ثابت نیستند! در اینجا، زمان بندی هر ثانیه تغییر می کند، و هنگامی که رنگ دیگری را انتخاب می کنید، پایه رنگ تغییر می کند. Props داده های یک جزء را در هر نقطه از زمان منعکس می کند نه فقط در ابتدا.

با این حال، props تغییر ناپذیر هستند - اصطلاحی از علوم کامپیوتر به معنای "غیر قابل تغییر". هنگامی که یک مؤلفه نیاز به تغییر اجزای خود دارد (مثلاً در پاسخ به تعامل کاربر یا داده‌های جدید)، باید از مؤلفه اصلی خود بخواهد که لوازم مختلف را به آن منتقل کند - یک شی جدید! سپس قطعات قدیمی آن کنار گذاشته می شوند و در نهایت موتور جاوا اسکریپت حافظه گرفته شده توسط آنها را بازیابی می کند.

سعی نکنید "تغییر وسایل" را انجام دهید. هنگامی که باید به ورودی کاربر پاسخ دهید (مانند تغییر رنگ انتخاب شده)، باید "Set state" را تنظیم کنید، که می توانید در مورد آن در State: A Component's Memory یاد بگیرید.
////
این مثال نشان می‌دهد که یک کامپوننت ممکن است در طول زمان props های مختلفی را دریافت کند. Props همیشه ثابت نیستند! در اینجا، time prop هر ثانیه تغییر می‌کند و color prop هنگام انتخاب رنگ دیگر تغییر می‌کند. Props در هر لحظه از زمان، داده‌های یک کامپوننت را نشان می‌دهند، نه فقط در ابتدا.

با این حال، props غیر قابل تغییر هستند - یک عبارت از علوم کامپیوتر به معنی “غیر قابل تغییر”. وقتی که یک کامپوننت نیاز به تغییر props خود دارد (به عنوان مثال، در پاسخ به تعامل کاربر یا داده جدید)، باید از کامپوننت والد خود بخواهد که props متفاوتی را به آن منتقل کند - یک شیء جدید! props قدیمی آن سپس کنار گذاشته می‌شود و در نهایت موتور JavaScript حافظه را بازیابی خواهد کرد.

سعی نکنید props را “تغییر دهید”. وقتی که نیاز به پاسخ به ورودی کاربر (مانند تغییر رنگ انتخاب شده) دارید، باید “state” را تنظیم کنید، که در مورد آن در “State: A Component’s Memory” می‌توانید بیشتر بدانید.



<Recap>

* To pass props, add them to the JSX, just like you would with HTML attributes.
* To read props, use the `function Avatar({ person, size })` destructuring syntax.
* You can specify a default value like `size = 100`, which is used for missing and `undefined` props.
* You can forward all props with `<Avatar {...props} />` JSX spread syntax, but don't overuse it!
* Nested JSX like `<Card><Avatar /></Card>` will appear as `Card` component's `children` prop.
* Props are read-only snapshots in time: every render receives a new version of props.
* You can't change props. When you need interactivity, you'll need to set state.
////
* برای ارسال props، آنها را به JSX اضافه کنید، درست مانند ویژگی های HTML.
* برای خواندن پروپوزال‌ها، از نحو ساختارشکن `«function Avatar({ person, size })»` استفاده کنید.
* می‌توانید یک مقدار پیش‌فرض مانند «اندازه = 100» را تعیین کنید، که برای موارد گمشده و «تعریف نشده» استفاده می‌شود.
* می‌توانید با `«<Avatar {...props} />»` دستور زبان JSX را ارسال کنید، اما از آن بیش از حد استفاده نکنید!
* JSX تودرتو مانند `«<Card><Avatar /></Card>»` به‌عنوان پایه «فرزندان» مؤلفه «کارت» ظاهر می‌شود.
* Props عکس های فوری فقط خواندنی در زمان هستند: هر رندر نسخه جدیدی از غرفه ها را دریافت می کند.
* شما نمی توانید وسایل را تغییر دهید. هنگامی که به تعامل نیاز دارید، باید حالت را تنظیم کنید.
////
* برای انتقال props، آنها را به JSX اضافه کنید، تماماً مانند ویژگی‌های HTML.
* برای خواندن props، از دستگیری دسته‌ای `function Avatar({ person, size })` استفاده کنید.
* می‌توانید مقدار پیش‌فرضی مانند size = 100 را مشخص کنید که برای props از دست رفته و undefined استفاده می‌شود.
* می‌توانید تمام props را با استفاده از نحو گسترده JSX `<Avatar {...props} />` ارسال کنید، اما از آن بیش از حد استفاده نکنید!
* JSX تو در تو مانند `<Card><Avatar /></Card>` به عنوان children prop کامپوننت Card ظاهر می‌شود.
* Props نسخه‌های خواندنی و فقط خواندنی در زمان: هر بار رندر، نسخه جدیدی از props دریافت می‌کند.
* شما نمی‌توانید props را تغییر دهید. هنگامی که به تعامل نیاز دارید، باید state را تنظیم کنید.

</Recap>



<Challenges>

#### Extract a component(یک کامپوننت را استخراج کنید) {/*extract-a-component*/}

This `Gallery` component contains some very similar markup for two profiles. Extract a `Profile` component out of it to reduce the duplication. You'll need to choose what props to pass to it.
////
این مؤلفه «گالری» دارای نشانه‌گذاری بسیار مشابه برای دو نمایه است. برای کاهش تکرار، یک جزء «نمایه» را از آن استخراج کنید. شما باید انتخاب کنید که چه لوازمی را به آن منتقل کنید.
////
لطفاً متن انگلیسی زیر را به فارسی ترجمه کنید: این کامپوننت Gallery شامل برچسب‌های بسیار شبیه به هم برای دو پروفایل است. یک کامپوننت Profile را از آن استخراج کنید تا تکرار را کاهش دهید. شما باید تصمیم بگیرید که چه props را به آن منتقل کنید1

<Sandpack>

```js App.js
import { getImageUrl } from './utils.js';

export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <section className="profile">
        <h2>Maria Skłodowska-Curie</h2>
        <img
          className="avatar"
          src={getImageUrl('szV5sdG')}
          alt="Maria Skłodowska-Curie"
          width={70}
          height={70}
        />
        <ul>
          <li>
            <b>Profession: </b> 
            physicist and chemist
          </li>
          <li>
            <b>Awards: 4 </b> 
            (Nobel Prize in Physics, Nobel Prize in Chemistry, Davy Medal, Matteucci Medal)
          </li>
          <li>
            <b>Discovered: </b>
            polonium (chemical element)
          </li>
        </ul>
      </section>
      <section className="profile">
        <h2>Katsuko Saruhashi</h2>
        <img
          className="avatar"
          src={getImageUrl('YfeOqp2')}
          alt="Katsuko Saruhashi"
          width={70}
          height={70}
        />
        <ul>
          <li>
            <b>Profession: </b> 
            geochemist
          </li>
          <li>
            <b>Awards: 2 </b> 
            (Miyake Prize for geochemistry, Tanaka Prize)
          </li>
          <li>
            <b>Discovered: </b>
            a method for measuring carbon dioxide in seawater
          </li>
        </ul>
      </section>
    </div>
  );
}
```

```js utils.js
export function getImageUrl(imageId, size = 's') {
  return (
    'https://i.imgur.com/' +
    imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; min-height: 70px; }
.profile {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}
h1, h2 { margin: 5px; }
h1 { margin-bottom: 10px; }
ul { padding: 0px 10px 0px 20px; }
li { margin: 5px; }
```

</Sandpack>

<Hint>

Start by extracting the markup for one of the scientists. Then find the pieces that don't match it in the second example, and make them configurable by props.
////
با استخراج نشانه گذاری برای یکی از دانشمندان شروع کنید. سپس قطعاتی را که با آن مطابقت ندارند را در مثال دوم پیدا کنید و آنها را با پایه‌ها تنظیم کنید.
////
لطفاً متن انگلیسی زیر را به فارسی ترجمه کنید: ابتدا برچسب‌های یکی از دانشمندان را استخراج کنید. سپس قطعاتی را که با آن مطابقت ندارند در مثال دوم پیدا کنید و آنها را با استفاده از props قابل پیکربندی کنید1

</Hint>

<Solution>

In this solution, the `Profile` component accepts multiple props: `imageId` (a string), `name` (a string), `profession` (a string), `awards` (an array of strings), `discovery` (a string), and `imageSize` (a number).

Note that the `imageSize` prop has a default value, which is why we don't pass it to the component.
////
در این راه حل، مولفه `«Profile»` چندین پروپوزال را می پذیرد: `«imageId»` (یک رشته)، «نام» (یک رشته)، `«حرفه»` (یک رشته)، «جوایز» (آرایه ای از رشته ها)، `«کشف»`. (یک رشته)، و `"imageSize"` (یک عدد).

توجه داشته باشید که prop `'imageSize'` یک مقدار پیش فرض دارد، به همین دلیل است که آن را به کامپوننت ارسال نمی کنیم.
////
در این راه‌حل، کامپوننت Profile چندین prop را شامل می‌شود: imageId (یک رشته)، name (یک رشته)، profession (یک رشته)، awards (یک آرایه از رشته‌ها)، discovery (یک رشته) و imageSize (یک عدد).

لطفاً توجه کنید که prop imageSize دارای مقدار پیش‌فرض است، به همین دلیل آن را به کامپوننت منتقل نمی‌کنیم.

<Sandpack>

```js App.js
import { getImageUrl } from './utils.js';

function Profile({
  imageId,
  name,
  profession,
  awards,
  discovery,
  imageSize = 70
}) {
  return (
    <section className="profile">
      <h2>{name}</h2>
      <img
        className="avatar"
        src={getImageUrl(imageId)}
        alt={name}
        width={imageSize}
        height={imageSize}
      />
      <ul>
        <li><b>Profession:</b> {profession}</li>
        <li>
          <b>Awards: {awards.length} </b>
          ({awards.join(', ')})
        </li>
        <li>
          <b>Discovered: </b>
          {discovery}
        </li>
      </ul>
    </section>
  );
}

export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <Profile
        imageId="szV5sdG"
        name="Maria Skłodowska-Curie"
        profession="physicist and chemist"
        discovery="polonium (chemical element)"
        awards={[
          'Nobel Prize in Physics',
          'Nobel Prize in Chemistry',
          'Davy Medal',
          'Matteucci Medal'
        ]}
      />
      <Profile
        imageId='YfeOqp2'
        name='Katsuko Saruhashi'
        profession='geochemist'
        discovery="a method for measuring carbon dioxide in seawater"
        awards={[
          'Miyake Prize for geochemistry',
          'Tanaka Prize'
        ]}
      />
    </div>
  );
}
```

```js utils.js
export function getImageUrl(imageId, size = 's') {
  return (
    'https://i.imgur.com/' +
    imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; min-height: 70px; }
.profile {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}
h1, h2 { margin: 5px; }
h1 { margin-bottom: 10px; }
ul { padding: 0px 10px 0px 20px; }
li { margin: 5px; }
```

</Sandpack>

Note how you don't need a separate `awardCount` prop if `awards` is an array. Then you can use `awards.length` to count the number of awards. Remember that props can take any values, and that includes arrays too!

Another solution, which is more similar to the earlier examples on this page, is to group all information about a person in a single object, and pass that object as one prop:
////
توجه داشته باشید که اگر «جوایز» یک آرایه است، چگونه به یک پایه `«awardCount»` جداگانه نیاز ندارید. سپس می‌توانید از `«awards.length»` برای شمارش تعداد جوایز استفاده کنید. به یاد داشته باشید که `props` می تواند هر مقداری را بگیرد، و این شامل آرایه ها نیز می شود!

راه حل دیگر، که بیشتر شبیه به مثال های قبلی در این صفحه است، این است که تمام اطلاعات مربوط به یک شخص را در یک شی واحد گروه بندی کنید و آن شی را به عنوان یک پایه ارسال کنید:
////
لطفاً متن انگلیسی زیر را به فارسی ترجمه کنید: توجه کنید که اگر `awards` یک آرایه باشد، نیازی به `prop` جداگانه `awardCount` نیست. سپس می‌توانید از `awards.length` برای شمارش تعداد جوایز استفاده کنید. به خاطر داشته باشید که `props` می‌توانند هر مقداری را بپذیرند، و این شامل آرایه‌ها نیز می‌شود!

راه‌حل دیگری که شبیه‌تر به مثال‌های قبلی در این صفحه است، گروه‌بندی تمام اطلاعات مربوط به یک شخص در یک شیء واحد است و این شیء را به عنوان یک `prop` منتقل می‌کنید.

<Sandpack>

```js App.js
import { getImageUrl } from './utils.js';

function Profile({ person, imageSize = 70 }) {
  const imageSrc = getImageUrl(person)

  return (
    <section className="profile">
      <h2>{person.name}</h2>
      <img
        className="avatar"
        src={imageSrc}
        alt={person.name}
        width={imageSize}
        height={imageSize}
      />
      <ul>
        <li>
          <b>Profession:</b> {person.profession}
        </li>
        <li>
          <b>Awards: {person.awards.length} </b>
          ({person.awards.join(', ')})
        </li>
        <li>
          <b>Discovered: </b>
          {person.discovery}
        </li>
      </ul>
    </section>
  )
}

export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <Profile person={{
        imageId: 'szV5sdG',
        name: 'Maria Skłodowska-Curie',
        profession: 'physicist and chemist',
        discovery: 'polonium (chemical element)',
        awards: [
          'Nobel Prize in Physics',
          'Nobel Prize in Chemistry',
          'Davy Medal',
          'Matteucci Medal'
        ],
      }} />
      <Profile person={{
        imageId: 'YfeOqp2',
        name: 'Katsuko Saruhashi',
        profession: 'geochemist',
        discovery: 'a method for measuring carbon dioxide in seawater',
        awards: [
          'Miyake Prize for geochemistry',
          'Tanaka Prize'
        ],
      }} />
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
.avatar { margin: 5px; border-radius: 50%; min-height: 70px; }
.profile {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}
h1, h2 { margin: 5px; }
h1 { margin-bottom: 10px; }
ul { padding: 0px 10px 0px 20px; }
li { margin: 5px; }
```

</Sandpack>

Although the syntax looks slightly different because you're describing properties of a JavaScript object rather than a collection of JSX attributes, these examples are mostly equivalent, and you can pick either approach.
////
اگرچه نحو کمی متفاوت به نظر می رسد، زیرا شما به جای مجموعه ای از ویژگی های JSX، ویژگی های یک شی جاوا اسکریپت را توصیف می کنید، این مثال ها اکثراً معادل هستند و می توانید هر یک از روش ها را انتخاب کنید.
////
اگرچه نحو ظاهری کمی متفاوت به نظر می‌رسد زیرا شما ویژگی‌های یک شیء JavaScript را توصیف می‌کنید به جای یک مجموعه از ویژگی‌های JSX، اما این مثال‌ها در بیشتر موارد معادل هستند و می‌توانید هر دو رویکرد را انتخاب کنید1

</Solution>

#### Adjust the image size based on a prop(اندازه پایه ی تصویر را بر اساس یک prop تنظیم کنید) {/*adjust-the-image-size-based-on-a-prop*/}

In this example, `Avatar` receives a numeric `size` prop which determines the `<img>` width and height. The `size` prop is set to `40` in this example. However, if you open the image in a new tab, you'll notice that the image itself is larger (`160` pixels). The real image size is determined by which thumbnail size you're requesting.

Change the `Avatar` component to request the closest image size based on the `size` prop. Specifically, if the `size` is less than `90`, pass `'s'` ("small") rather than `'b'` ("big") to the `getImageUrl` function. Verify that your changes work by rendering avatars with different values of the `size` prop and opening images in a new tab.
////
در این مثال، `«آواتار»` یک پایه `«اندازه»` عددی دریافت می‌کند که عرض و ارتفاع `«<img>»` را تعیین می‌کند. در این مثال پایه `«اندازه»` روی `«40»` تنظیم شده است. با این حال، اگر تصویر را در یک برگه جدید باز کنید، متوجه خواهید شد که خود تصویر بزرگتر است ('160' پیکسل). اندازه واقعی تصویر با توجه به اندازه تصویر کوچکی که درخواست می کنید تعیین می شود.

مولفه `«آواتار»` را تغییر دهید تا نزدیک‌ترین اندازه تصویر را براساس `«اندازه»` درخواست کنید. به طور خاص، اگر `«اندازه»` کمتر از `«۹۰»` باشد، به جای `«b»` (`«بزرگ»`) به تابع `«getImageUrl»`، `«'s»` (`«کوچک»`) ارسال کنید. بررسی کنید که تغییرات شما با رندر کردن آواتارها با مقادیر مختلف پایه `«اندازه»` و باز کردن تصاویر در یک برگه جدید کار می‌کنند.
////
در این مثال، کامپوننت Avatar یک prop عددی به نام size دریافت می‌کند که عرض و ارتفاع `<img>` را تعیین می‌کند. در این مثال، مقدار size برابر با 40 است. با این حال، اگر تصویر را در یک تب جدید باز کنید، متوجه خواهید شد که تصویر خود (160 پیکسل) بزرگتر است. اندازه واقعی تصویر توسط اندازه بندی تصویر کوچک مشخص می‌شود.

کامپوننت Avatar را تغییر دهید تا اندازه تصویر نزدیک‌ترین اندازه تصویر را بر اساس prop size درخواست کند. به طور خاص، اگر size کمتر از 90 باشد، به جای 'b' (“بزرگ”)، 's' (“کوچک”) را به تابع getImageUrl منتقل کنید. با رندر کردن آواتارها با مقادیر مختلف prop size و باز کردن تصاویر در یک تب جدید، از صحت تغییرات خود اطمینان حاصل کنید.

<Sandpack>

```js App.js
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person, 'b')}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <Avatar
      size={40}
      person={{ 
        name: 'Gregorio Y. Zara', 
        imageId: '7vQD0fP'
      }}
    />
  );
}
```

```js utils.js
export function getImageUrl(person, size) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 20px; border-radius: 50%; }
```

</Sandpack>

<Solution>

Here is how you could go about it:

<Sandpack>

```js App.js
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  let thumbnailSize = 's';
  if (size > 90) {
    thumbnailSize = 'b';
  }
  return (
    <img
      className="avatar"
      src={getImageUrl(person, thumbnailSize)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <>
      <Avatar
        size={40}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
      <Avatar
        size={120}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
    </>
  );
}
```

```js utils.js
export function getImageUrl(person, size) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 20px; border-radius: 50%; }
```

</Sandpack>

You could also show a sharper image for high DPI screens by taking [`window.devicePixelRatio`](https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio) into account:

<Sandpack>

```js App.js
import { getImageUrl } from './utils.js';

const ratio = window.devicePixelRatio;

function Avatar({ person, size }) {
  let thumbnailSize = 's';
  if (size * ratio > 90) {
    thumbnailSize = 'b';
  }
  return (
    <img
      className="avatar"
      src={getImageUrl(person, thumbnailSize)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <>
      <Avatar
        size={40}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
      <Avatar
        size={70}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
      <Avatar
        size={120}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
    </>
  );
}
```

```js utils.js
export function getImageUrl(person, size) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 20px; border-radius: 50%; }
```

</Sandpack>

Props let you encapsulate logic like this inside the `Avatar` component (and change it later if needed) so that everyone can use the `<Avatar>` component without thinking about how the images are requested and resized.

</Solution>

#### Passing JSX in a `children` prop {/*passing-jsx-in-a-children-prop*/}

Extract a `Card` component from the markup below, and use the `children` prop to pass different JSX to it:

<Sandpack>

```js
export default function Profile() {
  return (
    <div>
      <div className="card">
        <div className="card-content">
          <h1>Photo</h1>
          <img
            className="avatar"
            src="https://i.imgur.com/OKS67lhm.jpg"
            alt="Aklilu Lemma"
            width={70}
            height={70}
          />
        </div>
      </div>
      <div className="card">
        <div className="card-content">
          <h1>About</h1>
          <p>Aklilu Lemma was a distinguished Ethiopian scientist who discovered a natural treatment to schistosomiasis.</p>
        </div>
      </div>
    </div>
  );
}
```

```css
.card {
  width: fit-content;
  margin: 20px;
  padding: 20px;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.card-content {
  text-align: center;
}
.avatar {
  margin: 10px;
  border-radius: 50%;
}
h1 {
  margin: 5px;
  padding: 0;
  font-size: 24px;
}
```

</Sandpack>

<Hint>

Any JSX you put inside of a component's tag will be passed as the `children` prop to that component.

</Hint>

<Solution>

This is how you can use the `Card` component in both places:

<Sandpack>

```js
function Card({ children }) {
  return (
    <div className="card">
      <div className="card-content">
        {children}
      </div>
    </div>
  );
}

export default function Profile() {
  return (
    <div>
      <Card>
        <h1>Photo</h1>
        <img
          className="avatar"
          src="https://i.imgur.com/OKS67lhm.jpg"
          alt="Aklilu Lemma"
          width={100}
          height={100}
        />
      </Card>
      <Card>
        <h1>About</h1>
        <p>Aklilu Lemma was a distinguished Ethiopian scientist who discovered a natural treatment to schistosomiasis.</p>
      </Card>
    </div>
  );
}
```

```css
.card {
  width: fit-content;
  margin: 20px;
  padding: 20px;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.card-content {
  text-align: center;
}
.avatar {
  margin: 10px;
  border-radius: 50%;
}
h1 {
  margin: 5px;
  padding: 0;
  font-size: 24px;
}
```

</Sandpack>

You can also make `title` a separate prop if you want every `Card` to always have a title:

<Sandpack>

```js
function Card({ children, title }) {
  return (
    <div className="card">
      <div className="card-content">
        <h1>{title}</h1>
        {children}
      </div>
    </div>
  );
}

export default function Profile() {
  return (
    <div>
      <Card title="Photo">
        <img
          className="avatar"
          src="https://i.imgur.com/OKS67lhm.jpg"
          alt="Aklilu Lemma"
          width={100}
          height={100}
        />
      </Card>
      <Card title="About">
        <p>Aklilu Lemma was a distinguished Ethiopian scientist who discovered a natural treatment to schistosomiasis.</p>
      </Card>
    </div>
  );
}
```

```css
.card {
  width: fit-content;
  margin: 20px;
  padding: 20px;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.card-content {
  text-align: center;
}
.avatar {
  margin: 10px;
  border-radius: 50%;
}
h1 {
  margin: 5px;
  padding: 0;
  font-size: 24px;
}
```

</Sandpack>

</Solution>

</Challenges>
