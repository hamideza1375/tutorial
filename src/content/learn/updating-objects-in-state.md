---
title: Updating Objects in State
---

<Intro>

State can hold any kind of JavaScript value, including objects. But you shouldn't change objects that you hold in the React state directly. Instead, when you want to update an object, you need to create a new one (or make a copy of an existing one), and then set the state to use that copy.
//////
State می تواند هر نوع مقدار جاوا اسکریپت، از جمله اشیاء را در خود نگه دارد. اما شما نباید اشیایی را که در حالت React نگه می دارید مستقیماً تغییر دهید. در عوض، وقتی می‌خواهید یک شی را به‌روزرسانی کنید، باید یک شی جدید ایجاد کنید (یا یک کپی از یک موجود ایجاد کنید)، و سپس وضعیت را برای استفاده از آن کپی تنظیم کنید.

</Intro>

<YouWillLearn>

- How to correctly update an object in React state
- How to update a nested object without mutating it
- What immutability is, and how not to break it
- How to make object copying less repetitive with Immer

/////
- نحوه به روز رسانی صحیح یک شی در حالت React
- چگونه یک شی تو در تو را بدون تغییر به روز کنیم
- تغییر ناپذیری چیست و چگونه آن را نشکنیم
- چگونه با Immer کپی اشیاء را کمتر تکرار کنیم

</YouWillLearn>

## What's a mutation? {/*whats-a-mutation*/}

You can store any kind of JavaScript value in state.
شما می توانید هر نوع مقدار جاوا اسکریپت را در state ذخیره کنید.

```js
const [x, setX] = useState(0);
```

So far you've been working with numbers, strings, and booleans. These kinds of JavaScript values are "immutable", meaning unchangeable or "read-only". You can trigger a re-render to _replace_ a value:

/////////////

تاکنون با اعداد، رشته ها و بولی ها کار کرده اید. این نوع از مقادیر جاوا اسکریپت "immutable"، به معنای غیرقابل تغییر یا "فقط خواندنی" هستند. می توانید یک رندر مجدد برای _replace_ یک مقدار راه اندازی کنید:

```js
setX(5);
```

The `x` state changed from `0` to `5`, but the _number `0` itself_ did not change. It's not possible to make any changes to the built-in primitive values like numbers, strings, and booleans in JavaScript.

////////////////////////

حالت 'x' از '0' به '5' تغییر کرد، اما خود _number '0'_ تغییر نکرد. امکان ایجاد هیچ تغییری در مقادیر اولیه داخلی مانند اعداد، رشته ها و بولی ها در جاوا اسکریپت وجود ندارد.

```js

let x = 0;
let number = x;
number = 5;
// result
// x === 0 and number === 5

```

حال یک شی را در state در نظر بگیرید:

```js
const [position, setPosition] = useState({ x: 0, y: 0 });
```

Technically, it is possible to change the contents of _the object itself_. **This is called a mutation:**
/////
از نظر فنی، امکان تغییر محتویات _خود شی_ وجود دارد. **به این میگن میوتیشن:**

```js
position.x = 5;
```

However, although objects in React state are technically mutable, you should treat them **as if** they were immutable--like numbers, booleans, and strings. Instead of mutating them, you should always replace them.
//////
با این حال، اگرچه اشیاء در حالت React از نظر فنی قابل تغییر هستند، شما باید با آنها به گونه‌ای رفتار کنید که گویی غیرقابل تغییر هستند - مانند اعداد، بولی ها و رشته‌ها. به جای جهش، همیشه باید آنها را جایگزین کنید.

## Treat state as read-only(وضعیت را به عنوان فقط خواندنی در نظر بگیرید) {/*treat-state-as-read-only*/}

In other words, you should **treat any JavaScript object that you put into state as read-only.**
/////
به عبارت دیگر، شما باید **با هر شی جاوا اسکریپتی که در حالت قرار می دهید به عنوان فقط خواندنی رفتار کنید.**

This example holds an object in state to represent the current pointer position. The red dot is supposed to move when you touch or move the cursor over the preview area. But the dot stays in the initial position:
/////
این مثال یک object را در state نگه می دارد تا موقعیت اشاره گر فعلی را نشان دهد. زمانی که مکان نما را روی ناحیه پیش نمایش لمس می کنید یا حرکت می دهید، نقطه قرمز قرار است حرکت کند. اما نقطه در موقعیت اولیه باقی می ماند:

<Sandpack>

```js
import { useState } from 'react';
export default function MovingDot() {
  const [position, setPosition] = useState({
    x: 0,
    y: 0
  });
  return (
    <div
      onPointerMove={e => {
        position.x = e.clientX;
        position.y = e.clientY;
      }}
      style={{
        position: 'relative',
        width: '100vw',
        height: '100vh',
      }}>
      <div style={{
        position: 'absolute',
        backgroundColor: 'red',
        borderRadius: '50%',
        transform: `translate(${position.x}px, ${position.y}px)`,
        left: -10,
        top: -10,
        width: 20,
        height: 20,
      }} />
    </div>
  );
}
```

```css
body { margin: 0; padding: 0; height: 250px; }
```

</Sandpack>

The problem is with this bit of code.

```js
onPointerMove={e => {
  position.x = e.clientX;
  position.y = e.clientY;
}}
```

This code modifies the object assigned to `position` from [the previous render.](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time) But without using the state setting function, React has no idea that object has changed. So React does not do anything in response. It's like trying to change the order after you've already eaten the meal. While mutating state can work in some cases, we don't recommend it. You should treat the state value you have access to in a render as read-only.

/////////////////////

این کد شیء اختصاص داده شده به موقعیت از رندر قبلی را تغییر می دهد. اما بدون استفاده از تابع تنظیم stateا، React هیچ تصوری از تغییر شیء ندارد. بنابراین React در پاسخ هیچ کاری انجام نمی دهد. مثل این است که بعد از اینکه غذا را خورده اید سعی کنید ترتیب را تغییر دهید. در حالی که حالت جهش می تواند در برخی موارد کار کند، ما آن را توصیه نمی کنیم. شما باید مقدار حالتی را که در یک رندر به آن دسترسی دارید به عنوان فقط خواندنی در نظر بگیرید.

To actually [trigger a re-render](/learn/state-as-a-snapshot#setting-state-triggers-renders) in this case, **create a *new* object and pass it to the state setting function:**
//////
برای فعال کردن رندر مجدد در این مورد، یک شی جدید ایجاد کنید و آن را به تابع تنظیم کننده ی state ارسال کنید:

```js
onPointerMove={e => {
  setPosition({
    x: e.clientX,
    y: e.clientY
  });
}}
```

With `setPosition`, you're telling React:

* Replace `position` with this new object
* And render this component again

////////
با `setPosition`، به ریکت میگوید:

* موقعیت را با این شی جدید جایگزین کنید
* و دوباره این کامپوننت را رندر کنید

Notice how the red dot now follows your pointer when you touch or hover over the preview area:
///////
برای فعال کردن رندر مجدد در این مورد، یک شی جدید ایجاد کنید و آن را به تابع تنظیم کننده ی state ارسال کنید:

<Sandpack>

```js
import { useState } from 'react';
export default function MovingDot() {
  const [position, setPosition] = useState({
    x: 0,
    y: 0
  });
  return (
    <div
      onPointerMove={e => {
        setPosition({
          x: e.clientX,
          y: e.clientY
        });
      }}
      style={{
        position: 'relative',
        width: '100vw',
        height: '100vh',
      }}>
      <div style={{
        position: 'absolute',
        backgroundColor: 'red',
        borderRadius: '50%',
        transform: `translate(${position.x}px, ${position.y}px)`,
        left: -10,
        top: -10,
        width: 20,
        height: 20,
      }} />
    </div>
  );
}
```

```css
body { margin: 0; padding: 0; height: 250px; }
```

</Sandpack>

<DeepDive>

#### Local mutation is fine(میوتیشن محلی خوب هست) {/*local-mutation-is-fine*/}

Code like this is a problem because it modifies an *existing* object in state:
/////
کدی مانند این یک مشکل است زیرا یک شی موجود را در حالت تغییر می دهد:

```js
position.x = e.clientX;
position.y = e.clientY;
```

But code like this is **absolutely fine** because you're mutating a fresh object you have *just created*:
//////
اما کدی مانند این **کاملاً خوب** است زیرا شما در حال تغییر یک شیء تازه ای هستید که *به تازگی ایجاد کرده اید*:

```js
const nextPosition = {};
nextPosition.x = e.clientX;
nextPosition.y = e.clientY;
setPosition(nextPosition);
```

In fact, it is completely equivalent to writing this:
//////
در واقع، دقیق معادلش نوشتن برابر با این است:

```js
setPosition({
  x: e.clientX,
  y: e.clientY
});
```

Mutation is only a problem when you change *existing* objects that are already in state. Mutating an object you've just created is okay because *no other code references it yet.* Changing it isn't going to accidentally impact something that depends on it. This is called a "local mutation". You can even do local mutation [while rendering.](/learn/keeping-components-pure#local-mutation-your-components-little-secret) Very convenient and completely okay!
//////
تغییر(میوتیشن) تنها زمانی مشکل ایجاد می کند که اشیاء موجود را که قبلاً در state هستند تغییر دهید. تغییر دادن شیئی که به تازگی ایجاد کرده اید اشکالی ندارد زیرا هیچ کد دیگری هنوز به آن ارجاع نمی دهد. تغییر آن به طور تصادفی بر چیزی که به آن بستگی دارد تأثیر نمی گذارد. این "جهش محلی" نامیده می شود. حتی می توانید جهش محلی را در حین رندر انجام دهید. بسیار راحت و کاملاً خوب!

</DeepDive>  

## Copying objects with the spread syntax {/*copying-objects-with-the-spread-syntax*/}

In the previous example, the `position` object is always created fresh from the current cursor position. But often, you will want to include *existing* data as a part of the new object you're creating. For example, you may want to update *only one* field in a form, but keep the previous values for all other fields.

These input fields don't work because the `onChange` handlers mutate the state:

////////////////

در مثال قبلی، شی «position» همیشه تازه از موقعیت مکان نما ایجاد می شود. اما اغلب، شما می خواهید داده های *موجود* را به عنوان بخشی از شی جدیدی که ایجاد می کنید، بگنجانید. برای مثال، ممکن است بخواهید *فقط یک* فیلد را در یک فرم به روز کنید، اما مقادیر قبلی را برای همه فیلدهای دیگر حفظ کنید.

این فیلدهای ورودی کار نمی‌کنند زیرا کنترل‌کننده‌های «onChange» وضعیت را تغییر می‌دهند:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

  function handleFirstNameChange(e) {
    person.firstName = e.target.value;
  }

  function handleLastNameChange(e) {
    person.lastName = e.target.value;
  }

  function handleEmailChange(e) {
    person.email = e.target.value;
  }

  return (
    <>
      <label>
        First name:
        <input
          value={person.firstName}
          onChange={handleFirstNameChange}
        />
      </label>
      <label>
        Last name:
        <input
          value={person.lastName}
          onChange={handleLastNameChange}
        />
      </label>
      <label>
        Email:
        <input
          value={person.email}
          onChange={handleEmailChange}
        />
      </label>
      <p>
        {person.firstName}{' '}
        {person.lastName}{' '}
        ({person.email})
      </p>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; margin-bottom: 5px; }
```

</Sandpack>

For example, this line mutates the state from a past render:
/////
به عنوان مثال، این خط وضعیت را از یک رندر گذشته تغییر می دهد:

```js
person.firstName = e.target.value;
```

The reliable way to get the behavior you're looking for is to create a new object and pass it to `setPerson`. But here, you want to also **copy the existing data into it** because only one of the fields has changed:
/////
راه قابل اعتماد برای به دست آوردن رفتاری که به دنبال آن هستید، ایجاد یک شی جدید و ارسال آن به «setPerson» است. اما در اینجا، می‌خواهید **داده‌های موجود را نیز در آن کپی کنید** زیرا فقط یکی از فیلدها تغییر کرده است:

```js
setPerson({
  firstName: e.target.value, // New first name from the input
  lastName: person.lastName,
  email: person.email
});
```

You can use the `...` [object spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_object_literals) syntax so that you don't need to copy every property separately.
/////
می توانید از دستور ... object spread syntax استفاده کنید تا نیازی به کپی کردن هر ویژگی جداگانه نباشد.

```js
setPerson({
  ...person, // Copy the old fields
  firstName: e.target.value // But override this one
});
```

Now the form works! 

Notice how you didn't declare a separate state variable for each input field. For large forms, keeping all data grouped in an object is very convenient--as long as you update it correctly!
/////////////////////////////////
حالا فرم کار می کند!

توجه کنید که چگونه یک متغیر حالت جداگانه برای هر فیلد ورودی اعلام نکردید. برای فرم‌های بزرگ، نگه‌داشتن تمام داده‌ها در یک شی بسیار راحت است - تا زمانی که آن را به‌درستی به‌روزرسانی کنید!
////////////////////////////////
توجه داشته باشید که برای فرم‌های بزرگ، نگه داشتن تمام داده‌ها در یک شیء بسیار مناسب است، زیرا به شرط به‌روزرسانی صحیح آن، بسیار راحت است. توجه کنید که در مثالی که ارائه شده، شما برای هر فیلد ورودی، یک متغیر state جداگانه تعریف نکردید.

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

  function handleFirstNameChange(e) {
    setPerson({
      ...person,
      firstName: e.target.value
    });
  }

  function handleLastNameChange(e) {
    setPerson({
      ...person,
      lastName: e.target.value
    });
  }

  function handleEmailChange(e) {
    setPerson({
      ...person,
      email: e.target.value
    });
  }

  return (
    <>
      <label>
        First name:
        <input
          value={person.firstName}
          onChange={handleFirstNameChange}
        />
      </label>
      <label>
        Last name:
        <input
          value={person.lastName}
          onChange={handleLastNameChange}
        />
      </label>
      <label>
        Email:
        <input
          value={person.email}
          onChange={handleEmailChange}
        />
      </label>
      <p>
        {person.firstName}{' '}
        {person.lastName}{' '}
        ({person.email})
      </p>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; margin-bottom: 5px; }
```

</Sandpack>

Note that the `...` spread syntax is "shallow"--it only copies things one level deep. This makes it fast, but it also means that if you want to update a nested property, you'll have to use it more than once. 
//////////////
توجه داشته باشید که "..." spread syntax "کم عمق" است -- این فقط چیزها را در عمق یک سطح کپی می کند. این باعث می‌شود که سریع باشد، اما همچنین به این معنی است که اگر می‌خواهید یک ویژگی تودرتو را به‌روزرسانی کنید، باید بیش از یک بار از آن استفاده کنید.
/////////////////
توجه داشته باشید که دستور spread syntax اشیا در جاوااسکریپت، فقط یک سطح از اشیا را کپی می‌کند و به همین دلیل سریع است، اما اگر می‌خواهید یک خاصیت تو در تو را به‌روز کنید، باید از آن بیشتر از یک بار استفاده کنید.

<DeepDive>

#### Using a single event handler for multiple fields(استفاده از یک رویداد کنترل کننده برای چندین فیلد) {/*using-a-single-event-handler-for-multiple-fields*/}

You can also use the `[` and `]` braces inside your object definition to specify a property with dynamic name. Here is the same example, but with a single event handler instead of three different ones:
/////////////////
همچنین می‌توانید از بریس‌های `[` and `]` در تعریف شی خود برای تعیین یک ویژگی با نام پویا استفاده کنید. در اینجا همان مثال است، اما با یک کنترل کننده رویداد به جای سه مورد مختلف:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

  function handleChange(e) {
    setPerson({
      ...person,
      [e.target.name]: e.target.value
    });
  }

  return (
    <>
      <label>
        First name:
        <input
          name="firstName"
          value={person.firstName}
          onChange={handleChange}
        />
      </label>
      <label>
        Last name:
        <input
          name="lastName"
          value={person.lastName}
          onChange={handleChange}
        />
      </label>
      <label>
        Email:
        <input
          name="email"
          value={person.email}
          onChange={handleChange}
        />
      </label>
      <p>
        {person.firstName}{' '}
        {person.lastName}{' '}
        ({person.email})
      </p>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; margin-bottom: 5px; }
```

</Sandpack>

Here, `e.target.name` refers to the `name` property given to the `<input>` DOM element.
در اینجا، `e.target.name` به ویژگی نام داده شده به عنصر `<input>` DOM اشاره دارد.

</DeepDive>

## Updating a nested object(به روز رسانی یک شی تو در تو) {/*updating-a-nested-object*/}

Consider a nested object structure like this:
/////////////
یک ساختار شی تودرتو مانند زیر را در نظر بگیرید:

```js
const [person, setPerson] = useState({
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg',
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
});
```

If you wanted to update `person.artwork.city`, it's clear how to do it with mutation:
/////////////////////
اگر می‌خواهید `person.artwork.city` را به‌روزرسانی کنید، نحوه انجام این کار با mutation است:

```js
person.artwork.city = 'New Delhi';
```

But in React, you treat state as immutable! In order to change `city`, you would first need to produce the new `artwork` object (pre-populated with data from the previous one), and then produce the new `person` object which points at the new `artwork`:

//////////////////////

اما در React شما حالت را غیرقابل تغییر در نظر می گیرید! برای تغییر «شهر»، ابتدا باید شی «آثار هنری» جدید را تولید کنید (از قبل با داده های قبلی پر شده است)، و سپس شی «افراد» جدید را تولید کنید که به «آثار هنری» جدید اشاره می کند:

```js
const nextArtwork = { ...person.artwork, city: 'New Delhi' };
const nextPerson = { ...person, artwork: nextArtwork };
setPerson(nextPerson);
```

Or, written as a single function call:
/////////////////
یا به صورت یک فراخوانی تابع نوشته شده است:

```js
setPerson({
  ...person, // Copy other fields
  artwork: { // but replace the artwork
    ...person.artwork, // with the same one
    city: 'New Delhi' // but in New Delhi!
  }
});
```

This gets a bit wordy, but it works fine for many cases:
/////////////
این کمی لفظی می شود، اما برای بسیاری از موارد خوب کار می کند:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    setPerson({
      ...person,
      name: e.target.value
    });
  }

  function handleTitleChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        title: e.target.value
      }
    });
  }

  function handleCityChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        city: e.target.value
      }
    });
  }

  function handleImageChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        image: e.target.value
      }
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img 
        src={person.artwork.image} 
        alt={person.artwork.title}
      />
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; margin-bottom: 5px; }
img { width: 200px; height: 200px; }
```

</Sandpack>

<DeepDive>

#### Objects are not really nested (object ها واقعا تو در تو نیستن){/*objects-are-not-really-nested*/}

An object like this appears "nested" in code:
///////
یک شی مانند این "nested" در کد ظاهر می شود:

```js
let obj = {
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg',
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
};
```

However, "nesting" is an inaccurate way to think about how objects behave. When the code executes, there is no such thing as a "nested" object. You are really looking at two different objects:
///////
با این حال، "تودرتو" روشی نادرست برای تفکر در مورد نحوه رفتار اشیا است. هنگامی که کد اجرا می شود، چیزی به نام شی "تودرتو" وجود ندارد. شما واقعاً به دو شی متفاوت نگاه می کنید:

```js
let obj1 = {
  title: 'Blue Nana',
  city: 'Hamburg',
  image: 'https://i.imgur.com/Sd1AgUOm.jpg',
};

let obj2 = {
  name: 'Niki de Saint Phalle',
  artwork: obj1
};
```

The `obj1` object is not "inside" `obj2`. For example, `obj3` could "point" at `obj1` too:
/////
شی «obj1» «در داخل» «obj2» نیست. برای مثال، «obj3» می‌تواند به «obj1» نیز اشاره کند:

```js
let obj1 = {
  title: 'Blue Nana',
  city: 'Hamburg',
  image: 'https://i.imgur.com/Sd1AgUOm.jpg',
};

let obj2 = {
  name: 'Niki de Saint Phalle',
  artwork: obj1
};

let obj3 = {
  name: 'Copycat',
  artwork: obj1
};
```

If you were to mutate `obj3.artwork.city`, it would affect both `obj2.artwork.city` and `obj1.city`. This is because `obj3.artwork`, `obj2.artwork`, and `obj1` are the same object. This is difficult to see when you think of objects as "nested". Instead, they are separate objects "pointing" at each other with properties.
//////
اگر بخواهید «obj3.artwork.city» را تغییر دهید، روی «obj2.artwork.city» و «obj1.city» تأثیر می گذارد. دلیل آن این است که «obj3.artwork»، «obj2.artwork» و «obj1» یک شی هستند. وقتی به اشیاء "تودرتو" فکر می کنید، دیدن این امر دشوار است. در عوض، آنها اشیاء جداگانه ای هستند که با ویژگی هایی به یکدیگر "اشاره می کنند".

</DeepDive>  

### Write concise update logic with Immer(منطق به روز رسانی مختصر را با Immer بنویسید) {/*write-concise-update-logic-with-immer*/}

If your state is deeply nested, you might want to consider [flattening it.](/learn/choosing-the-state-structure#avoid-deeply-nested-state) But, if you don't want to change your state structure, you might prefer a shortcut to nested spreads. [Immer](https://github.com/immerjs/use-immer) is a popular library that lets you write using the convenient but mutating syntax and takes care of producing the copies for you. With Immer, the code you write looks like you are "breaking the rules" and mutating an object:
/////
اگر حالت شما عمیقاً تودرتو است، ممکن است بخواهید [صاف کردن آن را در نظر بگیرید.](/learn/choosing-the-state-structure#avoid-deeply-neted-state) اما، اگر نمی خواهید ساختار حالت خود را تغییر دهید. ، ممکن است میانبر را به گسترش های تودرتو ترجیح دهید. [Immer](https://github.com/immerjs/use-immer) یک کتابخانه محبوب است که به شما امکان می دهد با استفاده از نحو مناسب اما در حال تغییر بنویسید و از تولید کپی ها برای شما مراقبت می کند. با Immer، کدی که می نویسید به نظر می رسد که "قوانین را زیر پا می گذارید" و یک شی را تغییر می دهید:
//////
اگر وضعیت شما به صورت عمیق تو در تو باشد، ممکن است بخواهید آن را تسطیح کنید. اما اگر نمی‌خواهید ساختار وضعیت خود را تغییر دهید، ممکن است از یک میانبر به توزیع‌های تو در تو استفاده کنید. Immer یک کتابخانه محبوب است که به شما اجازه می‌دهد با استفاده از دستورات جایگزین، کدی را بنویسید که به نظر می‌رسد قوانین را نقض می‌کند و یک شیء را تغییر می‌دهد. این کتابخانه به طور خودکار کپی‌های لازم را برای شما تولید می‌کند.

```js
updatePerson(draft => {
  draft.artwork.city = 'Lagos';
});
```

But unlike a regular mutation, it doesn't overwrite the past state!
/////
اما برخلاف یک تغییر معمولی، state گذشته را بازنویسی نمی کند!
/////

<DeepDive>

#### How does Immer work?(ایمر چگونه کار می کند) {/*how-does-immer-work*/}

The `draft` provided by Immer is a special type of object, called a [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy), that "records" what you do with it. This is why you can mutate it freely as much as you like! Under the hood, Immer figures out which parts of the `draft` have been changed, and produces a completely new object that contains your edits.
/////
«پیش‌نویس» ارائه‌شده توسط Immer نوع خاصی از شی است، به نام [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)، که «ضبط "چیکار میکنی باهاش به همین دلیل است که می توانید آن را آزادانه هر چقدر که دوست دارید جهش دهید! زیر هود، Immer متوجه می‌شود که کدام بخش از «پیش‌نویس» تغییر کرده است و یک شی کاملاً جدید تولید می‌کند که حاوی ویرایش‌های شما است.
/////
پیش‌نویس ارائه شده توسط Immer نوع خاصی از شیء است که به عنوان یک Proxy شناخته می‌شود و “ثبت” می‌کند که شما با آن چه کار می‌کنید. به همین دلیل شما می‌توانید آن را به آسانی به هر اندازه که خواستید تغییر دهید! در زیر ساخت، Immer می‌فهمد کدام بخش‌های پیش‌نویس تغییر کرده‌اند و یک شیء کاملاً جدیدی را تولید می‌کند که ویرایش‌های شما را شامل می‌شود.

</DeepDive>

To try Immer:

1. Run `npm install use-immer` to add Immer as a dependency
2. Then replace `import { useState } from 'react'` with `import { useImmer } from 'use-immer'`

Here is the above example converted to Immer:
//////
برای امتحان Immer:

1. برای اضافه کردن Immer به عنوان یک وابستگی، `«npm install use-immer»` را اجرا کنید
2. سپس `«import { useState } `از `«react»` را با `«import { useImmer } from «use-immer»` جایگزین کنید.

در اینجا مثال بالا تبدیل به Immer است:

<Sandpack>

```js
import { useImmer } from 'use-immer';

export default function Form() {
  const [person, updatePerson] = useImmer({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    updatePerson(draft => {
      draft.name = e.target.value;
    });
  }

  function handleTitleChange(e) {
    updatePerson(draft => {
      draft.artwork.title = e.target.value;
    });
  }

  function handleCityChange(e) {
    updatePerson(draft => {
      draft.artwork.city = e.target.value;
    });
  }

  function handleImageChange(e) {
    updatePerson(draft => {
      draft.artwork.image = e.target.value;
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img 
        src={person.artwork.image} 
        alt={person.artwork.title}
      />
    </>
  );
}
```

```json package.json
{
  "dependencies": {
    "immer": "1.7.3",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "use-immer": "0.5.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```css
label { display: block; }
input { margin-left: 5px; margin-bottom: 5px; }
img { width: 200px; height: 200px; }
```

</Sandpack>

Notice how much more concise the event handlers have become. You can mix and match `useState` and `useImmer` in a single component as much as you like. Immer is a great way to keep the update handlers concise, especially if there's nesting in your state, and copying objects leads to repetitive code.
//////
توجه داشته باشید که کنترل کننده رویداد چقدر مختصرتر شده است. می‌توانید «useState» و «useImmer» را در یک مؤلفه به اندازه‌ای که دوست دارید ترکیب و مطابقت دهید. Immer یک راه عالی برای مختصر نگه داشتن کنترل‌کننده‌های به‌روزرسانی است، به‌خصوص اگر تودرتو در وضعیت شما وجود داشته باشد و کپی کردن اشیا منجر به کدهای تکراری شود.
//////////////
توجه کنید که چقدر event handlers کوتاه‌تر شده‌اند. شما می‌توانید useState و useImmer را در یک کامپوننت ترکیب کنید. استفاده از Immer روش خوبی برای کوتاه نگه داشتن event handlers است، به خصوص اگر در state شما nesting وجود داشته باشد و کپی کردن اشیاء منجر به کد تکراری شود.

<DeepDive>

#### Why is mutating state not recommended in React?(چرا تغییر state در ریکت توصیه نمیشود؟) {/*why-is-mutating-state-not-recommended-in-react*/}

There are a few reasons:
آنها چند مور هستند:
این متن درباره‌ی چند مورد است:

* **Debugging:** If you use `console.log` and don't mutate state, your past logs won't get clobbered by the more recent state changes. So you can clearly see how state has changed between renders.
////////////////
* **اشکال‌زدایی:** اگر از «console.log» استفاده می‌کنید و state را تغییر نمی‌دهید، گزارش‌های گذشته شما با تغییرات اخیر state مخدوش نمی‌شوند. بنابراین می توانید به وضوح ببینید که چگونه state بین رندرها تغییر کرده است.
///////////////
- اشکال زدایی: اگر از console.log استفاده کنید و state را تغییر ندهید، لاگ‌های قبلی شما توسط تغییرات state جدید پاک نخواهند شد. بنابراین می‌توانید به راحتی ببینید که state چگونه بین رندرها تغییر کرده است.


* **Optimizations:** Common React [optimization strategies](/reference/react/memo) rely on skipping work if previous props or state are the same as the next ones. If you never mutate state, it is very fast to check whether there were any changes. If `prevObj === obj`, you can be sure that nothing could have changed inside of it.
//////////
* **بهینه‌سازی‌ها: ** Common React [استراتژی‌های بهینه‌سازی](/reference/react/memo) به نادیده گرفتن کار تکیه می‌کنند اگر prop یا state قبلی مشابه prop بعدی باشد. اگر هرگز state تغییر نگرفتید، بررسی اینکه آیا تغییراتی وجود داشته است بسیار سریع است. اگر `prevObj === obj`، می توانید مطمئن باشید که هیچ چیزی در داخل آن تغییر نکرده است.
////////////
- بهینه‌سازی: استراتژی‌های بهینه‌سازی React معمول بر اساس این است که کار را برای props یا state قبلی انجام ندهد اگر همانند نسخه بعدی باشند. اگر state را هرگز تغییر ندهید، بسیار سریع است که بررسی کنید که آیا تغییری رخ داده است یا خیر. اگر prevObj === obj باشد، مطمئن باشید که هیچ چیز در داخل آن تغییر نکرده است.


* **New Features:** The new React features we're building rely on state being [treated like a snapshot.](/learn/state-as-a-snapshot) If you're mutating past versions of state, that may prevent you from using the new features.
////////
* **ویژگی‌های جدید:** ویژگی‌های جدید React که ما در حال ساخت آن هستیم بر روی state است که [مانند یک عکس فوری رفتار می‌شود.](/learn/state-as-a-snapshot) اگر نسخه‌های قبلی state را تغییر می‌دهید، ممکن است شما را از استفاده از ویژگی های جدید باز دارد.

* **Requirement Changes:** Some application features, like implementing Undo/Redo, showing a history of changes, or letting the user reset a form to earlier values, are easier to do when nothing is mutated. This is because you can keep past copies of state in memory, and reuse them when appropriate. If you start with a mutative approach, features like this can be difficult to add later on.
///////////
* **تغییرات الزامات:** برخی از ویژگی های برنامه، مانند پیاده سازی Undo/Redo، نشان دادن تاریخچه تغییرات، یا اجازه دادن به کاربر برای بازنشانی فرم به مقادیر قبلی، زمانی که هیچ چیز تغییر پیدا نکرده باشد، انجام آنها آسان تر است. این به این دلیل است که می توانید نسخه های قبلی state را در حافظه نگه دارید و در صورت لزوم از آنها مجددا استفاده کنید. اگر با یک رویکرد تغییر‌یافته شروع کنید، افزودن ویژگی‌هایی مانند این بعداً دشوار است.
///////////
- ویژگی‌های جدید: ویژگی‌های جدید React که در حال ساخت هستیم، بر این اساس است که state به عنوان یک snapshot در نظر گرفته شود. اگر نسخه‌های گذشته state را تغییر 
دهید، ممکن است از استفاده از ویژگی‌های جدید جلوگیری کنید.


* **Simpler Implementation:** Because React does not rely on mutation, it does not need to do anything special with your objects. It does not need to hijack their properties, always wrap them into Proxies, or do other work at initialization as many "reactive" solutions do. This is also why React lets you put any object into state--no matter how large--without additional performance or correctness pitfalls.
/////////////
* **پیاده سازی ساده تر:** چون React به تغییر متکی نیست، نیازی به انجام کار خاصی با اشیاء شما ندارد. نیازی به ربودن ویژگی‌های آن‌ها، قرار دادن آن‌ها در پروکسی‌ها، یا انجام کارهای دیگری مانند بسیاری از راه‌حل‌های «واکنشی» در زمان اولیه نیست. همچنین به همین دلیل است که React به شما امکان می‌دهد هر شیئی را - بدون توجه به بزرگی - بدون عملکرد اضافی یا مشکلات صحت، در state قرار دهید.
/////////////
- تغییرات نیاز: برخی از ویژگی‌های برنامه، مانند پیاده‌سازی Undo/Redo، نمایش تاریخچه تغییرات یا اجازه دادن به کاربر برای بازنشانی فرم به مقادیر قبلی، زمانی که هیچ چیز تغییر نکرده است، آسان‌تر است. این به این دلیل است که می‌توانید نسخه‌های گذشته state را در حافظه نگه دارید و آن‌ها را در صورت لزوم دوباره استفاده کنید. اگر با رویکرد mutative شروع کنید، اضافه کردن ویژگی‌هایی مانند این ممکن است بعداً دشوار باشد.


In practice, you can often "get away" with mutating state in React, but we strongly advise you not to do that so that you can use new React features developed with this approach in mind. Future contributors and perhaps even your future self will thank you!
///////////////
در عمل، اغلب می‌توانید از state تغییر‌یافته در React «دور» شوید، اما ما قویاً به شما توصیه می‌کنیم که این کار را نکنید تا بتوانید از ویژگی‌های جدید React که با این رویکرد توسعه یافته‌اند استفاده کنید. مشارکت کنندگان آینده و شاید حتی خود آینده شما از شما تشکر کنند!
//////////////
- پیاده‌سازی ساده‌تر: به دلیل اینکه React به متغیرهای state نیازی به mutation ندارد، نیازی به انجام کار خاصی با شی‌های شما ندارد. این نیازی به hijack کردن properties آن‌ها، همیشه wrap کردن آن‌ها در Proxies یا انجام کار دیگری در initialization ندارد، همانطور که بسیاری از راه‌حل‌های reactive انجام می‌دهند. همین دلیل است که React به شما اجازه می‌دهد هر شیء را به state بگذارید - بدون هیچ گونه مشکل عملکردی یا صحتی اضافی.
در عمل، شما می‌توانید اغل


</DeepDive>

<Recap>

* Treat all state in React as immutable.
* When you store objects in state, mutating them will not trigger renders and will change the state in previous render "snapshots".
* Instead of mutating an object, create a *new* version of it, and trigger a re-render by setting state to it.
* You can use the `{...obj, something: 'newValue'}` object spread syntax to create copies of objects.
* Spread syntax is shallow: it only copies one level deep.
* To update a nested object, you need to create copies all the way up from the place you're updating.
* To reduce repetitive copying code, use Immer.
////////
* تمام state های موجود در React را غیرقابل تغییر در نظر بگیرید.
* وقتی اشیا را در state ذخیره می‌کنید، تغییر آنها باعث ایجاد رندر نمی‌شود و وضعیت را در «عکس‌های فوری» رندر قبلی تغییر می‌دهد.
* به جای تغییر یک شی، یک نسخه *جدید* از آن ایجاد کنید، و با تنظیم state آن، یک رندر مجدد را آغاز کنید.
* می‌توانید از Spread syntax شی `«{...obj, something: 'newValue'}»` برای ایجاد کپی از اشیا استفاده کنید.
* Spread syntax سطحی است: فقط یک سطح عمیق را کپی می کند.
* برای به‌روزرسانی یک شی تودرتو، باید از جایی که به‌روزرسانی می‌کنید، نسخه‌هایی را از تمام راه بالا ایجاد کنید.
* برای کاهش تکرار کد کپی، از Immer استفاده کنید.

</Recap>



<Challenges>

#### Fix incorrect state updates {/*fix-incorrect-state-updates*/}

This form has a few bugs. Click the button that increases the score a few times. Notice that it does not increase. Then edit the first name, and notice that the score has suddenly "caught up" with your changes. Finally, edit the last name, and notice that the score has disappeared completely.

Your task is to fix all of these bugs. As you fix them, explain why each of them happens.

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [player, setPlayer] = useState({
    firstName: 'Ranjani',
    lastName: 'Shettar',
    score: 10,
  });

  function handlePlusClick() {
    player.score++;
  }

  function handleFirstNameChange(e) {
    setPlayer({
      ...player,
      firstName: e.target.value,
    });
  }

  function handleLastNameChange(e) {
    setPlayer({
      lastName: e.target.value
    });
  }

  return (
    <>
      <label>
        Score: <b>{player.score}</b>
        {' '}
        <button onClick={handlePlusClick}>
          +1
        </button>
      </label>
      <label>
        First name:
        <input
          value={player.firstName}
          onChange={handleFirstNameChange}
        />
      </label>
      <label>
        Last name:
        <input
          value={player.lastName}
          onChange={handleLastNameChange}
        />
      </label>
    </>
  );
}
```

```css
label { display: block; margin-bottom: 10px; }
input { margin-left: 5px; margin-bottom: 5px; }
```

</Sandpack>

<Solution>

Here is a version with both bugs fixed:

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [player, setPlayer] = useState({
    firstName: 'Ranjani',
    lastName: 'Shettar',
    score: 10,
  });

  function handlePlusClick() {
    setPlayer({
      ...player,
      score: player.score + 1,
    });
  }

  function handleFirstNameChange(e) {
    setPlayer({
      ...player,
      firstName: e.target.value,
    });
  }

  function handleLastNameChange(e) {
    setPlayer({
      ...player,
      lastName: e.target.value
    });
  }

  return (
    <>
      <label>
        Score: <b>{player.score}</b>
        {' '}
        <button onClick={handlePlusClick}>
          +1
        </button>
      </label>
      <label>
        First name:
        <input
          value={player.firstName}
          onChange={handleFirstNameChange}
        />
      </label>
      <label>
        Last name:
        <input
          value={player.lastName}
          onChange={handleLastNameChange}
        />
      </label>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; margin-bottom: 5px; }
```

</Sandpack>

The problem with `handlePlusClick` was that it mutated the `player` object. As a result, React did not know that there's a reason to re-render, and did not update the score on the screen. This is why, when you edited the first name, the state got updated, triggering a re-render which _also_ updated the score on the screen.

The problem with `handleLastNameChange` was that it did not copy the existing `...player` fields into the new object. This is why the score got lost after you edited the last name.

</Solution>

#### Find and fix the mutation {/*find-and-fix-the-mutation*/}

There is a draggable box on a static background. You can change the box's color using the select input.

But there is a bug. If you move the box first, and then change its color, the background (which isn't supposed to move!) will "jump" to the box position. But this should not happen: the `Background`'s `position` prop is set to `initialPosition`, which is `{ x: 0, y: 0 }`. Why is the background moving after the color change?

Find the bug and fix it.

<Hint>

If something unexpected changes, there is a mutation. Find the mutation in `App.js` and fix it.

</Hint>

<Sandpack>

```js App.js
import { useState } from 'react';
import Background from './Background.js';
import Box from './Box.js';

const initialPosition = {
  x: 0,
  y: 0
};

export default function Canvas() {
  const [shape, setShape] = useState({
    color: 'orange',
    position: initialPosition
  });

  function handleMove(dx, dy) {
    shape.position.x += dx;
    shape.position.y += dy;
  }

  function handleColorChange(e) {
    setShape({
      ...shape,
      color: e.target.value
    });
  }

  return (
    <>
      <select
        value={shape.color}
        onChange={handleColorChange}
      >
        <option value="orange">orange</option>
        <option value="lightpink">lightpink</option>
        <option value="aliceblue">aliceblue</option>
      </select>
      <Background
        position={initialPosition}
      />
      <Box
        color={shape.color}
        position={shape.position}
        onMove={handleMove}
      >
        Drag me!
      </Box>
    </>
  );
}
```

```js Box.js
import { useState } from 'react';

export default function Box({
  children,
  color,
  position,
  onMove
}) {
  const [
    lastCoordinates,
    setLastCoordinates
  ] = useState(null);

  function handlePointerDown(e) {
    e.target.setPointerCapture(e.pointerId);
    setLastCoordinates({
      x: e.clientX,
      y: e.clientY,
    });
  }

  function handlePointerMove(e) {
    if (lastCoordinates) {
      setLastCoordinates({
        x: e.clientX,
        y: e.clientY,
      });
      const dx = e.clientX - lastCoordinates.x;
      const dy = e.clientY - lastCoordinates.y;
      onMove(dx, dy);
    }
  }

  function handlePointerUp(e) {
    setLastCoordinates(null);
  }

  return (
    <div
      onPointerDown={handlePointerDown}
      onPointerMove={handlePointerMove}
      onPointerUp={handlePointerUp}
      style={{
        width: 100,
        height: 100,
        cursor: 'grab',
        backgroundColor: color,
        position: 'absolute',
        border: '1px solid black',
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        transform: `translate(
          ${position.x}px,
          ${position.y}px
        )`,
      }}
    >{children}</div>
  );
}
```

```js Background.js
export default function Background({
  position
}) {
  return (
    <div style={{
      position: 'absolute',
      transform: `translate(
        ${position.x}px,
        ${position.y}px
      )`,
      width: 250,
      height: 250,
      backgroundColor: 'rgba(200, 200, 0, 0.2)',
    }} />
  );
};
```

```css
body { height: 280px; }
select { margin-bottom: 10px; }
```

</Sandpack>

<Solution>

The problem was in the mutation inside `handleMove`. It mutated `shape.position`, but that's the same object that `initialPosition` points at. This is why both the shape and the background move. (It's a mutation, so the change doesn't reflect on the screen until an unrelated update--the color change--triggers a re-render.)

The fix is to remove the mutation from `handleMove`, and use the spread syntax to copy the shape. Note that `+=` is a mutation, so you need to rewrite it to use a regular `+` operation.

<Sandpack>

```js App.js
import { useState } from 'react';
import Background from './Background.js';
import Box from './Box.js';

const initialPosition = {
  x: 0,
  y: 0
};

export default function Canvas() {
  const [shape, setShape] = useState({
    color: 'orange',
    position: initialPosition
  });

  function handleMove(dx, dy) {
    setShape({
      ...shape,
      position: {
        x: shape.position.x + dx,
        y: shape.position.y + dy,
      }
    });
  }

  function handleColorChange(e) {
    setShape({
      ...shape,
      color: e.target.value
    });
  }

  return (
    <>
      <select
        value={shape.color}
        onChange={handleColorChange}
      >
        <option value="orange">orange</option>
        <option value="lightpink">lightpink</option>
        <option value="aliceblue">aliceblue</option>
      </select>
      <Background
        position={initialPosition}
      />
      <Box
        color={shape.color}
        position={shape.position}
        onMove={handleMove}
      >
        Drag me!
      </Box>
    </>
  );
}
```

```js Box.js
import { useState } from 'react';

export default function Box({
  children,
  color,
  position,
  onMove
}) {
  const [
    lastCoordinates,
    setLastCoordinates
  ] = useState(null);

  function handlePointerDown(e) {
    e.target.setPointerCapture(e.pointerId);
    setLastCoordinates({
      x: e.clientX,
      y: e.clientY,
    });
  }

  function handlePointerMove(e) {
    if (lastCoordinates) {
      setLastCoordinates({
        x: e.clientX,
        y: e.clientY,
      });
      const dx = e.clientX - lastCoordinates.x;
      const dy = e.clientY - lastCoordinates.y;
      onMove(dx, dy);
    }
  }

  function handlePointerUp(e) {
    setLastCoordinates(null);
  }

  return (
    <div
      onPointerDown={handlePointerDown}
      onPointerMove={handlePointerMove}
      onPointerUp={handlePointerUp}
      style={{
        width: 100,
        height: 100,
        cursor: 'grab',
        backgroundColor: color,
        position: 'absolute',
        border: '1px solid black',
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        transform: `translate(
          ${position.x}px,
          ${position.y}px
        )`,
      }}
    >{children}</div>
  );
}
```

```js Background.js
export default function Background({
  position
}) {
  return (
    <div style={{
      position: 'absolute',
      transform: `translate(
        ${position.x}px,
        ${position.y}px
      )`,
      width: 250,
      height: 250,
      backgroundColor: 'rgba(200, 200, 0, 0.2)',
    }} />
  );
};
```

```css
body { height: 280px; }
select { margin-bottom: 10px; }
```

</Sandpack>

</Solution>

#### Update an object with Immer {/*update-an-object-with-immer*/}

This is the same buggy example as in the previous challenge. This time, fix the mutation by using Immer. For your convenience, `useImmer` is already imported, so you need to change the `shape` state variable to use it.

<Sandpack>

```js App.js
import { useState } from 'react';
import { useImmer } from 'use-immer';
import Background from './Background.js';
import Box from './Box.js';

const initialPosition = {
  x: 0,
  y: 0
};

export default function Canvas() {
  const [shape, setShape] = useState({
    color: 'orange',
    position: initialPosition
  });

  function handleMove(dx, dy) {
    shape.position.x += dx;
    shape.position.y += dy;
  }

  function handleColorChange(e) {
    setShape({
      ...shape,
      color: e.target.value
    });
  }

  return (
    <>
      <select
        value={shape.color}
        onChange={handleColorChange}
      >
        <option value="orange">orange</option>
        <option value="lightpink">lightpink</option>
        <option value="aliceblue">aliceblue</option>
      </select>
      <Background
        position={initialPosition}
      />
      <Box
        color={shape.color}
        position={shape.position}
        onMove={handleMove}
      >
        Drag me!
      </Box>
    </>
  );
}
```

```js Box.js
import { useState } from 'react';

export default function Box({
  children,
  color,
  position,
  onMove
}) {
  const [
    lastCoordinates,
    setLastCoordinates
  ] = useState(null);

  function handlePointerDown(e) {
    e.target.setPointerCapture(e.pointerId);
    setLastCoordinates({
      x: e.clientX,
      y: e.clientY,
    });
  }

  function handlePointerMove(e) {
    if (lastCoordinates) {
      setLastCoordinates({
        x: e.clientX,
        y: e.clientY,
      });
      const dx = e.clientX - lastCoordinates.x;
      const dy = e.clientY - lastCoordinates.y;
      onMove(dx, dy);
    }
  }

  function handlePointerUp(e) {
    setLastCoordinates(null);
  }

  return (
    <div
      onPointerDown={handlePointerDown}
      onPointerMove={handlePointerMove}
      onPointerUp={handlePointerUp}
      style={{
        width: 100,
        height: 100,
        cursor: 'grab',
        backgroundColor: color,
        position: 'absolute',
        border: '1px solid black',
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        transform: `translate(
          ${position.x}px,
          ${position.y}px
        )`,
      }}
    >{children}</div>
  );
}
```

```js Background.js
export default function Background({
  position
}) {
  return (
    <div style={{
      position: 'absolute',
      transform: `translate(
        ${position.x}px,
        ${position.y}px
      )`,
      width: 250,
      height: 250,
      backgroundColor: 'rgba(200, 200, 0, 0.2)',
    }} />
  );
};
```

```css
body { height: 280px; }
select { margin-bottom: 10px; }
```

```json package.json
{
  "dependencies": {
    "immer": "1.7.3",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "use-immer": "0.5.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

</Sandpack>

<Solution>

This is the solution rewritten with Immer. Notice how the event handlers are written in a mutating fashion, but the bug does not occur. This is because under the hood, Immer never mutates the existing objects.

<Sandpack>

```js App.js
import { useImmer } from 'use-immer';
import Background from './Background.js';
import Box from './Box.js';

const initialPosition = {
  x: 0,
  y: 0
};

export default function Canvas() {
  const [shape, updateShape] = useImmer({
    color: 'orange',
    position: initialPosition
  });

  function handleMove(dx, dy) {
    updateShape(draft => {
      draft.position.x += dx;
      draft.position.y += dy;
    });
  }

  function handleColorChange(e) {
    updateShape(draft => {
      draft.color = e.target.value;
    });
  }

  return (
    <>
      <select
        value={shape.color}
        onChange={handleColorChange}
      >
        <option value="orange">orange</option>
        <option value="lightpink">lightpink</option>
        <option value="aliceblue">aliceblue</option>
      </select>
      <Background
        position={initialPosition}
      />
      <Box
        color={shape.color}
        position={shape.position}
        onMove={handleMove}
      >
        Drag me!
      </Box>
    </>
  );
}
```

```js Box.js
import { useState } from 'react';

export default function Box({
  children,
  color,
  position,
  onMove
}) {
  const [
    lastCoordinates,
    setLastCoordinates
  ] = useState(null);

  function handlePointerDown(e) {
    e.target.setPointerCapture(e.pointerId);
    setLastCoordinates({
      x: e.clientX,
      y: e.clientY,
    });
  }

  function handlePointerMove(e) {
    if (lastCoordinates) {
      setLastCoordinates({
        x: e.clientX,
        y: e.clientY,
      });
      const dx = e.clientX - lastCoordinates.x;
      const dy = e.clientY - lastCoordinates.y;
      onMove(dx, dy);
    }
  }

  function handlePointerUp(e) {
    setLastCoordinates(null);
  }

  return (
    <div
      onPointerDown={handlePointerDown}
      onPointerMove={handlePointerMove}
      onPointerUp={handlePointerUp}
      style={{
        width: 100,
        height: 100,
        cursor: 'grab',
        backgroundColor: color,
        position: 'absolute',
        border: '1px solid black',
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        transform: `translate(
          ${position.x}px,
          ${position.y}px
        )`,
      }}
    >{children}</div>
  );
}
```

```js Background.js
export default function Background({
  position
}) {
  return (
    <div style={{
      position: 'absolute',
      transform: `translate(
        ${position.x}px,
        ${position.y}px
      )`,
      width: 250,
      height: 250,
      backgroundColor: 'rgba(200, 200, 0, 0.2)',
    }} />
  );
};
```

```css
body { height: 280px; }
select { margin-bottom: 10px; }
```

```json package.json
{
  "dependencies": {
    "immer": "1.7.3",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "use-immer": "0.5.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

</Sandpack>

</Solution>

</Challenges>
