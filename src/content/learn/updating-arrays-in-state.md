---
title: Updating Arrays in State
---

<Intro>

//////////////////////////////////////

سلام! بهترین راه برای پیدا کردن یک آبجکت با id منحصر به فرد درون یک آرایه، استفاده از متد `findIndex` است. این متد، شماره ایندکس آبجکت مورد نظر را در آرایه برمی‌گرداند. سپس با استفاده از شماره ایندکس، می‌توانید آبجکت مورد نظر را پیدا کنید. 

این متد، با سرعت بیشتری نسبت به `find` کار می‌کند. برای استفاده از این متد، کافیست یک تابع callback برای آن تعریف کنید. این تابع، باید یک آرگومان داشته باشد و برای هر آیتم در آرایه، باید یک شرط بررسی کند. اگر شرط برای یک آیتم برقرار باشد، شماره ایندکس آن آیتم برگردانده می‌شود. در غیر این صورت، `-1` برگردانده می‌شود.

اینجا یک مثال از استفاده از `findIndex` برای پیدا کردن یک آبجکت با `id` منحصر به فرد درون یک آرایه را می‌بینید:

```javascript
const myArray = [{id: 1, name: 'John'}, {id: 2, name: 'Jane'}, {id: 3, name: 'Bob'}];
const idToFind = 2;

const index = myArray.findIndex(obj => obj.id === idToFind);

if (index !== -1) {
  console.log(`Found object at index ${index}:`, myArray[index]);
} else {
  console.log(`Object with id ${idToFind} not found in array`);
}
```

/////////////////////////////////

`createEntityAdapter` یک تابع در `Redux Toolkit` است که یک مجموعه از `reducer` ها و `selector` های پیش ساخته برای انجام عملیات CRUD روی یک ساختار داده نرمال شده حاوی نمونه هایی از یک نوع خاص از اشیاء داده ایجاد می کند. این توابع `reducer` می توانند به عنوان `case reducer` ها به `createReducer` و `createSlice` منتقل شوند. همچنین می توانند به عنوان توابع کمکی "mutating" در داخل `createReducer` و `createSlice` استفاده شوند. این API از کتابخانه `@ngrx/entity` ساخته شده توسط NgRx maintainers به `Redux Toolkit` منتقل شده است، اما برای استفاده با `Redux Toolkit` به طور قابل توجهی اصلاح شده است. ترم "Entity" برای ارجاع به نوع منحصر به فردی از شیء داده در برنامه استفاده می شود. برای مثال، در یک برنامه وبلاگ نویسی، شما ممکن است دارای شیء های داده ای User، Post و Comment باشید، که بسیاری از نمونه های هر کدام در کلاینت ذخیره شده و در سرور ذخیره می شوند. User یک "entity" است - یک نوع منحصر به فرد از شیء داده که برنامه از آن استفاده می کند. فرض می شود هر نمونه یک شناسه یکتا در یک فیلد خاص دارد. همانطور که در تمام منطق Redux، فقط اشیاء و آرایه های JS ساده باید به فروشگاه منتقل شوند - هیچ نمونه کلاسی نباید به فروشگاه منتقل شود! برای این منظور، ما از Entity برای ارجاع به نوع داده خاصی استفاده می کنیم که توسط یک نسخه از منطق `reducer` در یک بخش خاص از درخت حالت Redux مدیریت می شود، و از entity برای ارجاع به یک نمونه از آن نوع استفاده می شود. توابع تولید شده توسط `createEntityAdapter` همه یک ساختار "entity state" را که شامل موارد زیر است، تغییر می دهند:

```
{
  ids: [],
  entities: {}
}
```

`createEntityAdapter` می تواند در یک برنامه چند بار فراخوانی شود. اگر از آن با JavaScript ساده استفاده می کنید، ممکن است بتوانید یک تعریف adapter را با چندین نوع entity مشابه استفاده کنید، اگر آنها به اندازه کافی شبیه هم باشند (مانند داشتن یک فیلد entity.id). برای استفاده از TypeScript، شما باید برای هر ن

///////////////////////////////////////////////////////////////////


Arrays are mutable in JavaScript, but you should treat them as immutable when you store them in state. Just like with objects, when you want to update an array stored in state, you need to create a new one (or make a copy of an existing one), and then set state to use the new array.

////////////////////////
آرایه ها در جاوا اسکریپت قابل تغییر هستند، اما زمانی که آنها را در state ذخیره می کنید باید آنها را غیرقابل تغییر در نظر بگیرید. درست مانند اشیاء، زمانی که می خواهید یک آرایه ذخیره شده در حالت را به روز کنید، باید یک آرایه جدید ایجاد کنید (یا یک کپی از یک موجود تهیه کنید)، و سپس وضعیت را برای استفاده از آرایه جدید تنظیم کنید.

</Intro>

<YouWillLearn>

- How to add, remove, or change items in an array in React state
- How to update an object inside of an array
- How to make array copying less repetitive with Immer

/////////////////////

- نحوه افزودن، حذف یا تغییر موارد در یک آرایه در استیت React
- نحوه به روز رسانی یک شی در داخل یک آرایه
- چگونه با Immer کپی آرایه را کمتر تکرار کنیم

</YouWillLearn>

## Updating arrays without mutation(به روز رسانی آرایه ها بدون mutation) {/*updating-arrays-without-mutation*/}

In JavaScript, arrays are just another kind of object. [Like with objects](/learn/updating-objects-in-state), **you should treat arrays in React state as read-only.** This means that you shouldn't reassign items inside an array like `arr[0] = 'bird'`, and you also shouldn't use methods that mutate the array, such as `push()` and `pop()`.
////////////////
در جاوا اسکریپت، آرایه ها نوع دیگری از شی هستند. [مثل اشیاء](/learn/updating-objects-in-state)، **شما باید آرایه‌های موجود در React را فقط خواندنی در نظر بگیرید. `arr[0] = 'bird'`، و همچنین نباید از روش هایی استفاده کنید که آرایه را تغییر می دهند، مانند 'push()' و 'pop()'.

Instead, every time you want to update an array, you'll want to pass a *new* array to your state setting function. To do that, you can create a new array from the original array in your state by calling its non-mutating methods like `filter()` and `map()`. Then you can set your state to the resulting new array.

/////////////////////////////////////////
در عوض، هر بار که می‌خواهید یک آرایه را به‌روزرسانی کنید، می‌خواهید یک آرایه جدید را به تابع تنظیم state خود منتقل کنید. برای انجام این کار، می‌توانید با فراخوانی روش‌های غیر تغییر‌پذیر آن مانند filter() و map() یک آرایه جدید از آرایه اصلی در حالت خود ایجاد کنید. سپس می توانید حالت خود را روی آرایه جدید ایجاد شده تنظیم کنید.

Here is a reference table of common array operations. When dealing with arrays inside React state, you will need to avoid the methods in the left column, and instead prefer the methods in the right column:

|           | avoid (mutates the array)           | prefer (returns a new array)                                        |
| --------- | ----------------------------------- | ------------------------------------------------------------------- |
| adding    | `push`, `unshift`                   | `concat`, `[...arr]` spread syntax ([example](#adding-to-an-array)) |
| removing  | `pop`, `shift`, `splice`            | `filter`, `slice` ([example](#removing-from-an-array))              |
| replacing | `splice`, `arr[i] = ...` assignment | `map` ([example](#replacing-items-in-an-array))                     |
| sorting   | `reverse`, `sort`                   | copy the array first ([example](#making-other-changes-to-an-array)) |

در اینجا یک جدول مرجع از عملیات آرایه رایج است. وقتی با آرایه‌های داخل React State سروکار دارید، باید از متدهای ستون سمت چپ اجتناب کنید و در عوض روش‌های ستون سمت راست را ترجیح دهید:

///////////////////////////////////////////////////////////////

Alternatively, you can [use Immer](#write-concise-update-logic-with-immer) which lets you use methods from both columns.
//////////////
همچنین، می‌توانید [از Immer] (#write-concise-update-logic-with-immer) استفاده کنید که به شما امکان می‌دهد از روش‌های هر دو ستون استفاده کنید.

<Pitfall>

Unfortunately, [`slice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) and [`splice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) are named similarly but are very different:
////////////////////
متأسفانه، [`slice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) و [`splice`](https://developer.mozilla .org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) به طور مشابه نام‌گذاری شده‌اند، اما بسیار متفاوت هستند:

* `slice` lets you copy an array or a part of it.
* `splice` **mutates** the array (to insert or delete items).
////////////////
* 'slice' به شما امکان می دهد یک آرایه یا بخشی از آن را کپی کنید.
* `Splice` **آرایه را تغییر می دهد (برای درج یا حذف موارد).

In React, you will be using `slice` (no `p`!) a lot more often because you don't want to mutate objects or arrays in state. [Updating Objects](/learn/updating-objects-in-state) explains what mutation is and why it's not recommended for state.
/////////////////////
در React، شما اغلب از «slice» (بدون «p»!) استفاده می‌کنید، زیرا نمی‌خواهید اشیا یا آرایه‌ها را در state تغییر دهید. [Updating Objects](/learn/updating-objects-in-state) توضیح می دهد که میوتیت چیست و چرا برای state توصیه نمی شود.

</Pitfall>

### Adding to an array(اضافه کردن به یک آرایه) {/*adding-to-an-array*/}

`push()` will mutate an array, which you don't want:
//////////////////////
"push()" یک آرایه را تغییر می دهد که شما نمی خواهید:

<Sandpack>

```js
import { useState } from 'react';

let nextId = 0;

export default function List() {
  const [name, setName] = useState('');
  const [artists, setArtists] = useState([]);

  return (
    <>
      <h1>Inspiring sculptors:</h1>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={() => {
        artists.push({
          id: nextId++,
          name: name,
        });
      }}>Add</button>
      <ul>
        {artists.map(artist => (
          <li key={artist.id}>{artist.name}</li>
        ))}
      </ul>
    </>
  );
}
```

```css
button { margin-left: 5px; }
```

</Sandpack>

Instead, create a *new* array which contains the existing items *and* a new item at the end. There are multiple ways to do this, but the easiest one is to use the `...` [array spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_array_literals) syntax:
//////////////////////////////
در عوض، یک آرایه *جدید* ایجاد کنید که حاوی آیتم های موجود *و* یک آیتم جدید در پایان باشد. راه‌های مختلفی برای انجام این کار وجود دارد، اما ساده‌ترین راه استفاده از «...» [گسترش آرایه] است (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/ Spread_syntax#spread_in_array_literals) نحو:

```js
setArtists( // Replace the state
  [ // with a new array
    ...artists, // that contains all the old items
    { id: nextId++, name: name } // and one new item at the end
  ]
);
```

Now it works correctly:
////////////
حالا درست کار میکنه:

<Sandpack>

```js
import { useState } from 'react';

let nextId = 0;

export default function List() {
  const [name, setName] = useState('');
  const [artists, setArtists] = useState([]);

  return (
    <>
      <h1>Inspiring sculptors:</h1>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={() => {
        setArtists([
          ...artists,
          { id: nextId++, name: name }
        ]);
      }}>Add</button>
      <ul>
        {artists.map(artist => (
          <li key={artist.id}>{artist.name}</li>
        ))}
      </ul>
    </>
  );
}
```

```css
button { margin-left: 5px; }
```

</Sandpack>

The array spread syntax also lets you prepend an item by placing it *before* the original `...artists`:
/////////////////////
نحو (spread syntax) گسترش آرایه همچنین به شما این امکان را می دهد که یک آیتم را با قرار دادن آن *قبل از* "`...artists` اصلی، آماده کنید:
//////////
 همچنین، دستور گسترش(spread syntax) آرایه به شما این امکان را می‌دهد که یک مورد را به صورت پیش‌فرض قرار دهید، به شرطی که آن را قبل از مورد اصلی قرار دهید.

```js
setArtists([
  { id: nextId++, name: name },
  ...artists // Put old items at the end
]);
```

In this way, spread can do the job of both `push()` by adding to the end of an array and `unshift()` by adding to the beginning of an array. Try it in the sandbox above!
///////////////////
به این ترتیب، spread می‌تواند کار "push()" را با اضافه کردن به انتهای یک آرایه و "unshift()" با اضافه کردن به ابتدای یک آرایه انجام دهد. آن را در جعبه شنی بالا امتحان کنید!

### Removing from an array(حذف از یک آرایه) {/*removing-from-an-array*/}

The easiest way to remove an item from an array is to *filter it out*. In other words, you will produce a new array that will not contain that item. To do this, use the `filter` method, for example:
////////////////////////////////////////
ساده ترین راه برای حذف یک آیتم از آرایه، *فیلتر کردن آن* است. به عبارت دیگر، شما یک آرایه جدید تولید خواهید کرد که شامل آن آیتم نخواهد بود. برای انجام این کار، از روش «فیلتر» استفاده کنید، به عنوان مثال:

<Sandpack>

```js
import { useState } from 'react';

let initialArtists = [
  { id: 0, name: 'Marta Colvin Andrade' },
  { id: 1, name: 'Lamidi Olonade Fakeye'},
  { id: 2, name: 'Louise Nevelson'},
];

export default function List() {
  const [artists, setArtists] = useState(
    initialArtists
  );

  return (
    <>
      <h1>Inspiring sculptors:</h1>
      <ul>
        {artists.map(artist => (
          <li key={artist.id}>
            {artist.name}{' '}
            <button onClick={() => {
              setArtists(
                artists.filter(a =>
                  a.id !== artist.id
                )
              );
            }}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </>
  );
}
```

</Sandpack>

Click the "Delete" button a few times, and look at its click handler.
////////////////////////////
چند بار روی دکمه "حذف" کلیک کنید و به کنترل کننده کلیک آن نگاه کنید.

```js
setArtists(
  artists.filter(a => a.id !== artist.id)
);
```

Here, `artists.filter(a => a.id !== artist.id)` means "create an array that consists of those `artists` whose IDs are different from `artist.id`". In other words, each artist's "Delete" button will filter _that_ artist out of the array, and then request a re-render with the resulting array. Note that `filter` does not modify the original array.
///////////////////////////////////
در اینجا، `artists.filter(a => a.id !== artist.id)` به معنای ایجاد آرایه ای است که متشکل از هنرمندانی باشد که شناسه های آنها با `artist.id` متفاوت است. به عبارت دیگر، دکمه "حذف" هر هنرمند، هنرمند _that_ را از آرایه فیلتر می کند و سپس با آرایه به دست آمده درخواست رندر مجدد می کند. توجه داشته باشید که «فیلتر» آرایه اصلی را تغییر نمی‌دهد.

### Transforming an array(تبدیل یک آرایه) {/*transforming-an-array*/}

If you want to change some or all items of the array, you can use `map()` to create a **new** array. The function you will pass to `map` can decide what to do with each item, based on its data or its index (or both).
//////////////////////////////
اگر می‌خواهید برخی یا همه موارد آرایه را تغییر دهید، می‌توانید از `map()` برای ایجاد یک آرایه **جدید** استفاده کنید. تابعی که به `map()` ارسال می‌کنید، می‌تواند تصمیم بگیرد که با هر مورد، بر اساس داده‌ها یا شاخص آن (یا هر دو) چه کاری انجام دهد.

In this example, an array holds coordinates of two circles and a square. When you press the button, it moves only the circles down by 50 pixels. It does this by producing a new array of data using `map()`:
///////////////////////////////////
در این مثال، یک آرایه مختصات دو دایره و یک مربع را نگه می دارد. وقتی دکمه را فشار می دهید، فقط دایره ها را 50 پیکسل به پایین می برد. این کار را با تولید یک آرایه جدید از داده ها با استفاده از `map() انجام می دهد:
<Sandpack>

```js
import { useState } from 'react';

let initialShapes = [
  { id: 0, type: 'circle', x: 50, y: 100 },
  { id: 1, type: 'square', x: 150, y: 100 },
  { id: 2, type: 'circle', x: 250, y: 100 },
];

export default function ShapeEditor() {
  const [shapes, setShapes] = useState(
    initialShapes
  );

  function handleClick() {
    const nextShapes = shapes.map(shape => {
      if (shape.type === 'square') {
        // No change
        return shape;
      } else {
        // Return a new circle 50px below
        return {
          ...shape,
          y: shape.y + 50,
        };
      }
    });
    // Re-render with the new array
    setShapes(nextShapes);
  }

  return (
    <>
      <button onClick={handleClick}>
        Move circles down!
      </button>
      {shapes.map(shape => (
        <div
          key={shape.id}
          style={{
          background: 'purple',
          position: 'absolute',
          left: shape.x,
          top: shape.y,
          borderRadius:
            shape.type === 'circle'
              ? '50%' : '',
          width: 20,
          height: 20,
        }} />
      ))}
    </>
  );
}
```

```css
body { height: 300px; }
```

</Sandpack>

### Replacing items in an array(جایگزینی موارد در یک آرایه) {/*replacing-items-in-an-array*/}

It is particularly common to want to replace one or more items in an array. Assignments like `arr[0] = 'bird'` are mutating the original array, so instead you'll want to use `map` for this as well.

To replace an item, create a new array with `map`. Inside your `map` call, you will receive the item index as the second argument. Use it to decide whether to return the original item (the first argument) or something else:
/////////////////////
به خصوص رایج است که بخواهید یک یا چند آیتم را در یک آرایه جایگزین کنید. تکالیفی مانند «arr[0] = «پرنده» در حال تغییر در آرایه اصلی هستند، بنابراین در عوض می‌خواهید از `map` برای این کار نیز استفاده کنید.

برای جایگزینی یک مورد، یک آرایه جدید با `map` ایجاد کنید. در تماس `map` خود، نمایه مورد را به عنوان آرگومان دوم دریافت خواهید کرد. از آن برای بازگرداندن آیتم اصلی (آگومان اول) یا چیز دیگری استفاده کنید:
///////////////////////
به طور معمول، نیاز است که یک یا چند مورد را در یک آرایه جایگزین کنید. اختصاص‌هایی مانند arr[0] = 'bird' آرایه اصلی را تغییر می‌دهند، بنابراین به جای آن، برای این کار از map استفاده خواهید کرد.

برای جایگزینی یک مورد، یک آرایه جدید با map ایجاد کنید. درون تابع map، شاخص مورد را به عنوان آرگومان دوم دریافت خواهید کرد. از آن برای تصمیم‌گیری در مورد بازگشت مورد اصلی (آرگومان اول) یا چیز دیگری استفاده کنید:

<Sandpack>

```js
import { useState } from 'react';

let initialCounters = [
  0, 0, 0
];

export default function CounterList() {
  const [counters, setCounters] = useState(
    initialCounters
  );

  function handleIncrementClick(index) {
    const nextCounters = counters.map((c, i) => {
      if (i === index) {
        // Increment the clicked counter
        return c + 1;
      } else {
        // The rest haven't changed
        return c;
      }
    });
    setCounters(nextCounters);
  }

  return (
    <ul>
      {counters.map((counter, i) => (
        <li key={i}>
          {counter}
          <button onClick={() => {
            handleIncrementClick(i);
          }}>+1</button>
        </li>
      ))}
    </ul>
  );
}
```

```css
button { margin: 5px; }
```

</Sandpack>

### Inserting into an array(درج در یک آرایه) {/*inserting-into-an-array*/}

Sometimes, you may want to insert an item at a particular position that's neither at the beginning nor at the end. To do this, you can use the `...` array spread syntax together with the `slice()` method. The `slice()` method lets you cut a "slice" of the array. To insert an item, you will create an array that spreads the slice _before_ the insertion point, then the new item, and then the rest of the original array.

In this example, the Insert button always inserts at the index `1`:
///////////////////////////////
گاهی اوقات، ممکن است بخواهید یک مورد را در یک موقعیت خاص قرار دهید که نه در ابتدا و نه در پایان است. برای این کار، می‌توانید از نحو گسترش آرایه «...» همراه با متد «slice()» استفاده کنید. متد «slice()» به شما امکان می دهد یک «برش» از آرایه را برش دهید. برای درج یک آیتم، آرایه ای ایجاد می کنید که برش _قبل از_ نقطه درج، سپس آیتم جدید و سپس بقیه آرایه اصلی را پخش می کند.

در این مثال، دکمه Insert همیشه در شاخص «1» درج می‌شود:

<Sandpack>

```js
import { useState } from 'react';

let nextId = 3;
const initialArtists = [
  { id: 0, name: 'Marta Colvin Andrade' },
  { id: 1, name: 'Lamidi Olonade Fakeye'},
  { id: 2, name: 'Louise Nevelson'},
];

export default function List() {
  const [name, setName] = useState('');
  const [artists, setArtists] = useState(
    initialArtists
  );

  function handleClick() {
    const insertAt = 1; // Could be any index
    const nextArtists = [
      // Items before the insertion point:
      ...artists.slice(0, insertAt),
      // New item:
      { id: nextId++, name: name },
      // Items after the insertion point:
      ...artists.slice(insertAt)
    ];
    setArtists(nextArtists);
    setName('');
  }

  return (
    <>
      <h1>Inspiring sculptors:</h1>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={handleClick}>
        Insert
      </button>
      <ul>
        {artists.map(artist => (
          <li key={artist.id}>{artist.name}</li>
        ))}
      </ul>
    </>
  );
}
```

```css
button { margin-left: 5px; }
```

</Sandpack>

### Making other changes to an array(ایجاد تغییرات دیگر در یک آرایه) {/*making-other-changes-to-an-array*/}

There are some things you can't do with the spread syntax and non-mutating methods like `map()` and `filter()` alone. For example, you may want to reverse or sort an array. The JavaScript `reverse()` and `sort()` methods are mutating the original array, so you can't use them directly.
////////////////
برخی از کارها وجود دارد که نمی‌توانید با spread syntax و روش‌های غیر تغییر ‌پذیر مانند «map()» و «filter()» به تنهایی انجام دهید. برای مثال، ممکن است بخواهید یک آرایه را معکوس یا مرتب کنید. متدهای "reverse()" و "sort()" جاوا اسکریپت آرایه اصلی را تغییر می دهند، بنابراین نمی توانید مستقیماً از آنها استفاده کنید.

**However, you can copy the array first, and then make changes to it.**

** با این حال، می توانید ابتدا آرایه را کپی کنید و سپس تغییراتی در آن ایجاد کنید.**
For example:

<Sandpack>

```js
import { useState } from 'react';

let nextId = 3;
const initialList = [
  { id: 0, title: 'Big Bellies' },
  { id: 1, title: 'Lunar Landscape' },
  { id: 2, title: 'Terracotta Army' },
];

export default function List() {
  const [list, setList] = useState(initialList);

  function handleClick() {
    const nextList = [...list];
    nextList.reverse();
    setList(nextList);
  }

  return (
    <>
      <button onClick={handleClick}>
        Reverse
      </button>
      <ul>
        {list.map(artwork => (
          <li key={artwork.id}>{artwork.title}</li>
        ))}
      </ul>
    </>
  );
}
```

</Sandpack>

Here, you use the `[...list]` spread syntax to create a copy of the original array first. Now that you have a copy, you can use mutating methods like `nextList.reverse()` or `nextList.sort()`, or even assign individual items with `nextList[0] = "something"`.
//////////////////
در اینجا، شما از اسپرید سینتکس [...list] برای ایجاد یک کپی از آرایه اصلی استفاده می‌کنید. اکنون که یک کپی دارید، می‌توانید از روش‌های میوتیتینگ مانند «nextList.reverse()» یا «nextList.sort()» استفاده کنید، یا حتی آیتم‌های فردی را با  `nextList[0] = "something"` اختصاص دهید.

However, **even if you copy an array, you can't mutate existing items _inside_ of it directly.** This is because copying is shallow--the new array will contain the same items as the original one. So if you modify an object inside the copied array, you are mutating the existing state. For example, code like this is a problem.
////////////////////
با این حال، **حتی اگر یک آرایه را کپی کنید، نمی‌توانید آیتم‌های موجود را _درون_ آن را مستقیماً تغییر دهید. بنابراین اگر یک شی را در داخل آرایه کپی شده تغییر دهید، استیت موجود را تغییر می دهید. برای مثال، کدهایی مانند این مشکل دارند.

```js
const nextList = [...list];
nextList[0].seen = true; // Problem: mutates list[0]
setList(nextList);
```

Although `nextList` and `list` are two different arrays, **`nextList[0]` and `list[0]` point to the same object.** So by changing `nextList[0].seen`, you are also changing `list[0].seen`. This is a state mutation, which you should avoid! You can solve this issue in a similar way to [updating nested JavaScript objects](/learn/updating-objects-in-state#updating-a-nested-object)--by copying individual items you want to change instead of mutating them. Here's how.
/////////////////////////////////
اگرچه nextList و list دو آرایه متفاوت هستند، nextList[0] و list[0] به یک شی اشاره می کنند. بنابراین با تغییر nextList[0].seen، list[0].seen را نیز تغییر می دهید. این یک تغییر حالت است که باید از آن اجتناب کنید! می‌توانید این مشکل را به روشی مشابه به‌روزرسانی اشیاء جاوا اسکریپت تودرتو حل کنید—با کپی کردن موارد فردی که می‌خواهید تغییر دهید به جای جهش. در اینجا نحوه

## Updating objects inside arrays(به روز رسانی اشیاء داخل آرایه ها) {/*updating-objects-inside-arrays*/}

Objects are not _really_ located "inside" arrays. They might appear to be "inside" in code, but each object in an array is a separate value, to which the array "points". This is why you need to be careful when changing nested fields like `list[0]`. Another person's artwork list may point to the same element of the array!
///////////////////
اشیاء _واقعا_ در آرایه های "داخل" قرار ندارند. ممکن است در کد "درون" به نظر برسند، اما هر شیء در یک آرایه یک مقدار جداگانه است که آرایه به آن "اشاره می کند". به همین دلیل است که هنگام تغییر فیلدهای تودرتو مانند «list[0]» باید مراقب باشید. فهرست آثار هنری شخص دیگری ممکن است به همان عنصر آرایه اشاره کند!

**When updating nested state, you need to create copies from the point where you want to update, and all the way up to the top level.** Let's see how this works.

** هنگام به‌روزرسانی حالت تودرتو، باید از نقطه‌ای که می‌خواهید به‌روزرسانی شود، کپی‌هایی ایجاد کنید، و تا سطح بالا. ** بیایید ببینیم این کار چگونه کار می‌کند.

In this example, two separate artwork lists have the same initial state. They are supposed to be isolated, but because of a mutation, their state is accidentally shared, and checking a box in one list affects the other list:
/////////////////////////////
در این مثال، دو لیست آثار هنری مجزا حالت اولیه یکسانی دارند. آنها قرار است ایزوله شوند، اما به دلیل یک تغییر، وضعیت آنها به طور تصادفی به اشتراک گذاشته می شود و علامت زدن یک کادر در یک لیست روی لیست دیگر تأثیر می گذارد:

<Sandpack>

```js
import { useState } from 'react';

let nextId = 3;
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

export default function BucketList() {
  const [myList, setMyList] = useState(initialList);
  const [yourList, setYourList] = useState(
    initialList
  );

  function handleToggleMyList(artworkId, nextSeen) {
    const myNextList = [...myList];
    const artwork = myNextList.find(
      a => a.id === artworkId
    );
    artwork.seen = nextSeen;
    setMyList(myNextList);
  }

  function handleToggleYourList(artworkId, nextSeen) {
    const yourNextList = [...yourList];
    const artwork = yourNextList.find(
      a => a.id === artworkId
    );
    artwork.seen = nextSeen;
    setYourList(yourNextList);
  }

  return (
    <>
      <h1>Art Bucket List</h1>
      <h2>My list of art to see:</h2>
      <ItemList
        artworks={myList}
        onToggle={handleToggleMyList} />
      <h2>Your list of art to see:</h2>
      <ItemList
        artworks={yourList}
        onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map(artwork => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={e => {
                onToggle(
                  artwork.id,
                  e.target.checked
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
```

</Sandpack>

The problem is in code like this:
مشکل در کدهای زیر است:

```js
const myNextList = [...myList];
const artwork = myNextList.find(a => a.id === artworkId);
artwork.seen = nextSeen; // Problem: mutates an existing item
setMyList(myNextList);
```

Although the `myNextList` array itself is new, the *items themselves* are the same as in the original `myList` array. So changing `artwork.seen` changes the *original* artwork item. That artwork item is also in `yourList`, which causes the bug. Bugs like this can be difficult to think about, but thankfully they disappear if you avoid mutating state.
////////////////
اگرچه خود آرایه myNextList جدید است، اما خود آیتم ها مانند آرایه اصلی myList هستند. بنابراین تغییر artwork.seen آیتم اصلی اثر هنری را تغییر می دهد. آن آیتم اثر هنری نیز در yourList است که باعث ایجاد باگ می شود. فکر کردن درباره اشکالاتی مانند این ممکن است دشوار باشد، اما خوشبختانه در صورت اجتناب از حالت جهش، ناپدید می شوند.

**You can use `map` to substitute an old item with its updated version without mutation.**
/////////////////////////
**شما می توانید از «نقشه» برای جایگزینی یک مورد قدیمی با نسخه به روز شده آن بدون جهش استفاده کنید.**

```js
setMyList(myList.map(artwork => {
  if (artwork.id === artworkId) {
    // Create a *new* object with changes
    return { ...artwork, seen: nextSeen };
  } else {
    // No changes
    return artwork;
  }
}));
```

Here, `...` is the object spread syntax used to [create a copy of an object.](/learn/updating-objects-in-state#copying-objects-with-the-spread-syntax)

With this approach, none of the existing state items are being mutated, and the bug is fixed:
///////////////////
در اینجا، `...`  spread syntax شی مورد استفاده برای [ایجاد یک کپی از یک شی است.](/learn/updating-objects-in-state#copying-objects-with-the-spread-syntax)

با این رویکرد، هیچ یک از آیتم های حالت موجود تغییر پیدا نمی کند، و باگ برطرف می شود:

<Sandpack>

```js
import { useState } from 'react';

let nextId = 3;
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

export default function BucketList() {
  const [myList, setMyList] = useState(initialList);
  const [yourList, setYourList] = useState(
    initialList
  );

  function handleToggleMyList(artworkId, nextSeen) {
    setMyList(myList.map(artwork => {
      if (artwork.id === artworkId) {
        // Create a *new* object with changes
        return { ...artwork, seen: nextSeen };
      } else {
        // No changes
        return artwork;
      }
    }));
  }

  function handleToggleYourList(artworkId, nextSeen) {
    setYourList(yourList.map(artwork => {
      if (artwork.id === artworkId) {
        // Create a *new* object with changes
        return { ...artwork, seen: nextSeen };
      } else {
        // No changes
        return artwork;
      }
    }));
  }

  return (
    <>
      <h1>Art Bucket List</h1>
      <h2>My list of art to see:</h2>
      <ItemList
        artworks={myList}
        onToggle={handleToggleMyList} />
      <h2>Your list of art to see:</h2>
      <ItemList
        artworks={yourList}
        onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map(artwork => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={e => {
                onToggle(
                  artwork.id,
                  e.target.checked
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
```

</Sandpack>

In general, **you should only mutate objects that you have just created.** If you were inserting a *new* artwork, you could mutate it, but if you're dealing with something that's already in state, you need to make a copy.
//////////////////////////
به طور کلی، **شما باید فقط اشیایی را تغییر دهید که به تازگی ایجاد کرده اید.** اگر یک اثر هنری *جدید* را درج می کنید، می توانید آن را تغییر دهید، اما اگر با چیزی سروکار دارید که از قبل در حالت است، باید آن را ایجاد کنید. یک کپی.

### Write concise update logic with Immer(منطق به روز رسانی مختصر را با Immer بنویسید) {/*write-concise-update-logic-with-immer*/}

Updating nested arrays without mutation can get a little bit repetitive. [Just as with objects](/learn/updating-objects-in-state#write-concise-update-logic-with-immer):
/////////////////////////////
به روز رسانی آرایه های تو در تو بدون جهش می تواند کمی تکراری شود. [درست مانند اشیاء](/learn/updating-objects-in-state#write-concise-update-logic-with-immer):

- Generally, you shouldn't need to update state more than a couple of levels deep. If your state objects are very deep, you might want to [restructure them differently](/learn/choosing-the-state-structure#avoid-deeply-nested-state) so that they are flat.
- If you don't want to change your state structure, you might prefer to use [Immer](https://github.com/immerjs/use-immer), which lets you write using the convenient but mutating syntax and takes care of producing the copies for you.
//////////////////////////
- به طور کلی، شما نباید نیاز به به روز رسانی حالت بیش از چند سطح عمیق داشته باشید. اگر اشیاء حالت شما بسیار عمیق هستند، ممکن است بخواهید [آنها را به گونه ای دیگر ساختار مجدد دهید](/learn/choosing-the-state-structure#avoid-deeply-neted-state) به طوری که آنها مسطح باشند.
- اگر نمی‌خواهید ساختار حالت خود را تغییر دهید، ممکن است ترجیح دهید از [Immer] (https://github.com/immerjs/use-immer) استفاده کنید، که به شما امکان می‌دهد با استفاده از نحو راحت اما جهش‌یافته بنویسید و مراقب باشید. از تولید کپی برای شما.

Here is the Art Bucket List example rewritten with Immer:
در اینجا مثال Art Bucket List است که با Immer بازنویسی شده است:

<Sandpack>

```js
import { useState } from 'react';
import { useImmer } from 'use-immer';

let nextId = 3;
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

export default function BucketList() {
  const [myList, updateMyList] = useImmer(
    initialList
  );
  const [yourList, updateYourList] = useImmer(
    initialList
  );

  function handleToggleMyList(id, nextSeen) {
    updateMyList(draft => {
      const artwork = draft.find(a =>
        a.id === id
      );
      artwork.seen = nextSeen;
    });
  }

  function handleToggleYourList(artworkId, nextSeen) {
    updateYourList(draft => {
      const artwork = draft.find(a =>
        a.id === artworkId
      );
      artwork.seen = nextSeen;
    });
  }

  return (
    <>
      <h1>Art Bucket List</h1>
      <h2>My list of art to see:</h2>
      <ItemList
        artworks={myList}
        onToggle={handleToggleMyList} />
      <h2>Your list of art to see:</h2>
      <ItemList
        artworks={yourList}
        onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map(artwork => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={e => {
                onToggle(
                  artwork.id,
                  e.target.checked
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
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

</Sandpack>

Note how with Immer, **mutation like `artwork.seen = nextSeen` is now okay:**
توجه داشته باشید که چگونه با Immer، **mutation مانند «artwork.seen = nextSeen» اکنون مشکلی ندارد:**

```js
updateMyTodos(draft => {
  const artwork = draft.find(a => a.id === artworkId);
  artwork.seen = nextSeen;
});
```

This is because you're not mutating the _original_ state, but you're mutating a special `draft` object provided by Immer. Similarly, you can apply mutating methods like `push()` and `pop()` to the content of the `draft`.

Behind the scenes, Immer always constructs the next state from scratch according to the changes that you've done to the `draft`. This keeps your event handlers very concise without ever mutating state.
/////////////////
این به این دلیل است که شما حالت _original_ را تغییر نمی‌دهید، بلکه در حال تغییر یک شی «پیش‌نویس» ویژه ارائه‌شده توسط Immer هستید. به طور مشابه، می‌توانید روش‌های جهش‌یافته مانند «push()» و «pop()» را برای محتوای «پیش‌نویس» اعمال کنید.

در پشت صحنه، Immer همیشه با توجه به تغییراتی که در «پیش‌نویس» انجام داده‌اید، وضعیت بعدی را از ابتدا می‌سازد. این باعث می‌شود که کنترل‌کننده‌های رویداد شما بسیار مختصر باشند، بدون اینکه حالت تغییر کند.
<Recap>

- You can put arrays into state, but you can't change them.
- Instead of mutating an array, create a *new* version of it, and update the state to it.
- You can use the `[...arr, newItem]` array spread syntax to create arrays with new items.
- You can use `filter()` and `map()` to create new arrays with filtered or transformed items.
- You can use Immer to keep your code concise.
////////////////////////
- می توانید آرایه ها را در state قرار دهید، اما نمی توانید آنها را تغییر دهید.
- به جای تغییر یک آرایه، یک نسخه *جدید* از آن ایجاد کنید و state را به آن به روز کنید.
- برای ایجاد آرایه‌ها با آیتم‌های جدید، می‌توانید از spread syntax آرایه `[...arr, newItem]` استفاده کنید.
- می توانید از «filter()» و «map()» برای ایجاد آرایه های جدید با موارد فیلتر شده یا تبدیل شده استفاده کنید.
- می توانید از Immer برای مختصر نگه داشتن کد خود استفاده کنید.

</Recap>



<Challenges>

#### Update an item in the shopping cart {/*update-an-item-in-the-shopping-cart*/}

Fill in the `handleIncreaseClick` logic so that pressing "+" increases the corresponding number:

<Sandpack>

```js
import { useState } from 'react';

const initialProducts = [{
  id: 0,
  name: 'Baklava',
  count: 1,
}, {
  id: 1,
  name: 'Cheese',
  count: 5,
}, {
  id: 2,
  name: 'Spaghetti',
  count: 2,
}];

export default function ShoppingCart() {
  const [
    products,
    setProducts
  ] = useState(initialProducts)

  function handleIncreaseClick(productId) {

  }

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>
          {product.name}
          {' '}
          (<b>{product.count}</b>)
          <button onClick={() => {
            handleIncreaseClick(product.id);
          }}>
            +
          </button>
        </li>
      ))}
    </ul>
  );
}
```

```css
button { margin: 5px; }
```

</Sandpack>

<Solution>

You can use the `map` function to create a new array, and then use the `...` object spread syntax to create a copy of the changed object for the new array:

<Sandpack>

```js
import { useState } from 'react';

const initialProducts = [{
  id: 0,
  name: 'Baklava',
  count: 1,
}, {
  id: 1,
  name: 'Cheese',
  count: 5,
}, {
  id: 2,
  name: 'Spaghetti',
  count: 2,
}];

export default function ShoppingCart() {
  const [
    products,
    setProducts
  ] = useState(initialProducts)

  function handleIncreaseClick(productId) {
    setProducts(products.map(product => {
      if (product.id === productId) {
        return {
          ...product,
          count: product.count + 1
        };
      } else {
        return product;
      }
    }))
  }

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>
          {product.name}
          {' '}
          (<b>{product.count}</b>)
          <button onClick={() => {
            handleIncreaseClick(product.id);
          }}>
            +
          </button>
        </li>
      ))}
    </ul>
  );
}
```

```css
button { margin: 5px; }
```

</Sandpack>

</Solution>

#### Remove an item from the shopping cart {/*remove-an-item-from-the-shopping-cart*/}

This shopping cart has a working "+" button, but the "–" button doesn't do anything. You need to add an event handler to it so that pressing it decreases the `count` of the corresponding product. If you press "–" when the count is 1, the product should automatically get removed from the cart. Make sure it never shows 0.

<Sandpack>

```js
import { useState } from 'react';

const initialProducts = [{
  id: 0,
  name: 'Baklava',
  count: 1,
}, {
  id: 1,
  name: 'Cheese',
  count: 5,
}, {
  id: 2,
  name: 'Spaghetti',
  count: 2,
}];

export default function ShoppingCart() {
  const [
    products,
    setProducts
  ] = useState(initialProducts)

  function handleIncreaseClick(productId) {
    setProducts(products.map(product => {
      if (product.id === productId) {
        return {
          ...product,
          count: product.count + 1
        };
      } else {
        return product;
      }
    }))
  }

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>
          {product.name}
          {' '}
          (<b>{product.count}</b>)
          <button onClick={() => {
            handleIncreaseClick(product.id);
          }}>
            +
          </button>
          <button>
            –
          </button>
        </li>
      ))}
    </ul>
  );
}
```

```css
button { margin: 5px; }
```

</Sandpack>

<Solution>

You can first use `map` to produce a new array, and then `filter` to remove products with a `count` set to `0`:

<Sandpack>

```js
import { useState } from 'react';

const initialProducts = [{
  id: 0,
  name: 'Baklava',
  count: 1,
}, {
  id: 1,
  name: 'Cheese',
  count: 5,
}, {
  id: 2,
  name: 'Spaghetti',
  count: 2,
}];

export default function ShoppingCart() {
  const [
    products,
    setProducts
  ] = useState(initialProducts)

  function handleIncreaseClick(productId) {
    setProducts(products.map(product => {
      if (product.id === productId) {
        return {
          ...product,
          count: product.count + 1
        };
      } else {
        return product;
      }
    }))
  }

  function handleDecreaseClick(productId) {
    let nextProducts = products.map(product => {
      if (product.id === productId) {
        return {
          ...product,
          count: product.count - 1
        };
      } else {
        return product;
      }
    });
    nextProducts = nextProducts.filter(p =>
      p.count > 0
    );
    setProducts(nextProducts)
  }

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>
          {product.name}
          {' '}
          (<b>{product.count}</b>)
          <button onClick={() => {
            handleIncreaseClick(product.id);
          }}>
            +
          </button>
          <button onClick={() => {
            handleDecreaseClick(product.id);
          }}>
            –
          </button>
        </li>
      ))}
    </ul>
  );
}
```

```css
button { margin: 5px; }
```

</Sandpack>

</Solution>

#### Fix the mutations using non-mutative methods {/*fix-the-mutations-using-non-mutative-methods*/}

In this example, all of the event handlers in `App.js` use mutation. As a result, editing and deleting todos doesn't work. Rewrite `handleAddTodo`, `handleChangeTodo`, and `handleDeleteTodo` to use the non-mutative methods:

<Sandpack>

```js App.js
import { useState } from 'react';
import AddTodo from './AddTodo.js';
import TaskList from './TaskList.js';

let nextId = 3;
const initialTodos = [
  { id: 0, title: 'Buy milk', done: true },
  { id: 1, title: 'Eat tacos', done: false },
  { id: 2, title: 'Brew tea', done: false },
];

export default function TaskApp() {
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAddTodo(title) {
    todos.push({
      id: nextId++,
      title: title,
      done: false
    });
  }

  function handleChangeTodo(nextTodo) {
    const todo = todos.find(t =>
      t.id === nextTodo.id
    );
    todo.title = nextTodo.title;
    todo.done = nextTodo.done;
  }

  function handleDeleteTodo(todoId) {
    const index = todos.findIndex(t =>
      t.id === todoId
    );
    todos.splice(index, 1);
  }

  return (
    <>
      <AddTodo
        onAddTodo={handleAddTodo}
      />
      <TaskList
        todos={todos}
        onChangeTodo={handleChangeTodo}
        onDeleteTodo={handleDeleteTodo}
      />
    </>
  );
}
```

```js AddTodo.js
import { useState } from 'react';

export default function AddTodo({ onAddTodo }) {
  const [title, setTitle] = useState('');
  return (
    <>
      <input
        placeholder="Add todo"
        value={title}
        onChange={e => setTitle(e.target.value)}
      />
      <button onClick={() => {
        setTitle('');
        onAddTodo(title);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  todos,
  onChangeTodo,
  onDeleteTodo
}) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <Task
            todo={todo}
            onChange={onChangeTodo}
            onDelete={onDeleteTodo}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ todo, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let todoContent;
  if (isEditing) {
    todoContent = (
      <>
        <input
          value={todo.title}
          onChange={e => {
            onChange({
              ...todo,
              title: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    todoContent = (
      <>
        {todo.title}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={todo.done}
        onChange={e => {
          onChange({
            ...todo,
            done: e.target.checked
          });
        }}
      />
      {todoContent}
      <button onClick={() => onDelete(todo.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

<Solution>

In `handleAddTodo`, you can use the array spread syntax. In `handleChangeTodo`, you can create a new array with `map`. In `handleDeleteTodo`, you can create a new array with `filter`. Now the list works correctly:

<Sandpack>

```js App.js
import { useState } from 'react';
import AddTodo from './AddTodo.js';
import TaskList from './TaskList.js';

let nextId = 3;
const initialTodos = [
  { id: 0, title: 'Buy milk', done: true },
  { id: 1, title: 'Eat tacos', done: false },
  { id: 2, title: 'Brew tea', done: false },
];

export default function TaskApp() {
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAddTodo(title) {
    setTodos([
      ...todos,
      {
        id: nextId++,
        title: title,
        done: false
      }
    ]);
  }

  function handleChangeTodo(nextTodo) {
    setTodos(todos.map(t => {
      if (t.id === nextTodo.id) {
        return nextTodo;
      } else {
        return t;
      }
    }));
  }

  function handleDeleteTodo(todoId) {
    setTodos(
      todos.filter(t => t.id !== todoId)
    );
  }

  return (
    <>
      <AddTodo
        onAddTodo={handleAddTodo}
      />
      <TaskList
        todos={todos}
        onChangeTodo={handleChangeTodo}
        onDeleteTodo={handleDeleteTodo}
      />
    </>
  );
}
```

```js AddTodo.js
import { useState } from 'react';

export default function AddTodo({ onAddTodo }) {
  const [title, setTitle] = useState('');
  return (
    <>
      <input
        placeholder="Add todo"
        value={title}
        onChange={e => setTitle(e.target.value)}
      />
      <button onClick={() => {
        setTitle('');
        onAddTodo(title);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  todos,
  onChangeTodo,
  onDeleteTodo
}) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <Task
            todo={todo}
            onChange={onChangeTodo}
            onDelete={onDeleteTodo}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ todo, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let todoContent;
  if (isEditing) {
    todoContent = (
      <>
        <input
          value={todo.title}
          onChange={e => {
            onChange({
              ...todo,
              title: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    todoContent = (
      <>
        {todo.title}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={todo.done}
        onChange={e => {
          onChange({
            ...todo,
            done: e.target.checked
          });
        }}
      />
      {todoContent}
      <button onClick={() => onDelete(todo.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

</Solution>


#### Fix the mutations using Immer {/*fix-the-mutations-using-immer*/}

This is the same example as in the previous challenge. This time, fix the mutations by using Immer. For your convenience, `useImmer` is already imported, so you need to change the `todos` state variable to use it.

<Sandpack>

```js App.js
import { useState } from 'react';
import { useImmer } from 'use-immer';
import AddTodo from './AddTodo.js';
import TaskList from './TaskList.js';

let nextId = 3;
const initialTodos = [
  { id: 0, title: 'Buy milk', done: true },
  { id: 1, title: 'Eat tacos', done: false },
  { id: 2, title: 'Brew tea', done: false },
];

export default function TaskApp() {
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAddTodo(title) {
    todos.push({
      id: nextId++,
      title: title,
      done: false
    });
  }

  function handleChangeTodo(nextTodo) {
    const todo = todos.find(t =>
      t.id === nextTodo.id
    );
    todo.title = nextTodo.title;
    todo.done = nextTodo.done;
  }

  function handleDeleteTodo(todoId) {
    const index = todos.findIndex(t =>
      t.id === todoId
    );
    todos.splice(index, 1);
  }

  return (
    <>
      <AddTodo
        onAddTodo={handleAddTodo}
      />
      <TaskList
        todos={todos}
        onChangeTodo={handleChangeTodo}
        onDeleteTodo={handleDeleteTodo}
      />
    </>
  );
}
```

```js AddTodo.js
import { useState } from 'react';

export default function AddTodo({ onAddTodo }) {
  const [title, setTitle] = useState('');
  return (
    <>
      <input
        placeholder="Add todo"
        value={title}
        onChange={e => setTitle(e.target.value)}
      />
      <button onClick={() => {
        setTitle('');
        onAddTodo(title);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  todos,
  onChangeTodo,
  onDeleteTodo
}) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <Task
            todo={todo}
            onChange={onChangeTodo}
            onDelete={onDeleteTodo}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ todo, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let todoContent;
  if (isEditing) {
    todoContent = (
      <>
        <input
          value={todo.title}
          onChange={e => {
            onChange({
              ...todo,
              title: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    todoContent = (
      <>
        {todo.title}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={todo.done}
        onChange={e => {
          onChange({
            ...todo,
            done: e.target.checked
          });
        }}
      />
      {todoContent}
      <button onClick={() => onDelete(todo.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
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

With Immer, you can write code in the mutative fashion, as long as you're only mutating parts of the `draft` that Immer gives you. Here, all mutations are performed on the `draft` so the code works:

<Sandpack>

```js App.js
import { useState } from 'react';
import { useImmer } from 'use-immer';
import AddTodo from './AddTodo.js';
import TaskList from './TaskList.js';

let nextId = 3;
const initialTodos = [
  { id: 0, title: 'Buy milk', done: true },
  { id: 1, title: 'Eat tacos', done: false },
  { id: 2, title: 'Brew tea', done: false },
];

export default function TaskApp() {
  const [todos, updateTodos] = useImmer(
    initialTodos
  );

  function handleAddTodo(title) {
    updateTodos(draft => {
      draft.push({
        id: nextId++,
        title: title,
        done: false
      });
    });
  }

  function handleChangeTodo(nextTodo) {
    updateTodos(draft => {
      const todo = draft.find(t =>
        t.id === nextTodo.id
      );
      todo.title = nextTodo.title;
      todo.done = nextTodo.done;
    });
  }

  function handleDeleteTodo(todoId) {
    updateTodos(draft => {
      const index = draft.findIndex(t =>
        t.id === todoId
      );
      draft.splice(index, 1);
    });
  }

  return (
    <>
      <AddTodo
        onAddTodo={handleAddTodo}
      />
      <TaskList
        todos={todos}
        onChangeTodo={handleChangeTodo}
        onDeleteTodo={handleDeleteTodo}
      />
    </>
  );
}
```

```js AddTodo.js
import { useState } from 'react';

export default function AddTodo({ onAddTodo }) {
  const [title, setTitle] = useState('');
  return (
    <>
      <input
        placeholder="Add todo"
        value={title}
        onChange={e => setTitle(e.target.value)}
      />
      <button onClick={() => {
        setTitle('');
        onAddTodo(title);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  todos,
  onChangeTodo,
  onDeleteTodo
}) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <Task
            todo={todo}
            onChange={onChangeTodo}
            onDelete={onDeleteTodo}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ todo, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let todoContent;
  if (isEditing) {
    todoContent = (
      <>
        <input
          value={todo.title}
          onChange={e => {
            onChange({
              ...todo,
              title: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    todoContent = (
      <>
        {todo.title}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={todo.done}
        onChange={e => {
          onChange({
            ...todo,
            done: e.target.checked
          });
        }}
      />
      {todoContent}
      <button onClick={() => onDelete(todo.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
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

You can also mix and match the mutative and non-mutative approaches with Immer.

For example, in this version `handleAddTodo` is implemented by mutating the Immer `draft`, while `handleChangeTodo` and `handleDeleteTodo` use the non-mutative `map` and `filter` methods:

<Sandpack>

```js App.js
import { useState } from 'react';
import { useImmer } from 'use-immer';
import AddTodo from './AddTodo.js';
import TaskList from './TaskList.js';

let nextId = 3;
const initialTodos = [
  { id: 0, title: 'Buy milk', done: true },
  { id: 1, title: 'Eat tacos', done: false },
  { id: 2, title: 'Brew tea', done: false },
];

export default function TaskApp() {
  const [todos, updateTodos] = useImmer(
    initialTodos
  );

  function handleAddTodo(title) {
    updateTodos(draft => {
      draft.push({
        id: nextId++,
        title: title,
        done: false
      });
    });
  }

  function handleChangeTodo(nextTodo) {
    updateTodos(todos.map(todo => {
      if (todo.id === nextTodo.id) {
        return nextTodo;
      } else {
        return todo;
      }
    }));
  }

  function handleDeleteTodo(todoId) {
    updateTodos(
      todos.filter(t => t.id !== todoId)
    );
  }

  return (
    <>
      <AddTodo
        onAddTodo={handleAddTodo}
      />
      <TaskList
        todos={todos}
        onChangeTodo={handleChangeTodo}
        onDeleteTodo={handleDeleteTodo}
      />
    </>
  );
}
```

```js AddTodo.js
import { useState } from 'react';

export default function AddTodo({ onAddTodo }) {
  const [title, setTitle] = useState('');
  return (
    <>
      <input
        placeholder="Add todo"
        value={title}
        onChange={e => setTitle(e.target.value)}
      />
      <button onClick={() => {
        setTitle('');
        onAddTodo(title);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  todos,
  onChangeTodo,
  onDeleteTodo
}) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <Task
            todo={todo}
            onChange={onChangeTodo}
            onDelete={onDeleteTodo}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ todo, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let todoContent;
  if (isEditing) {
    todoContent = (
      <>
        <input
          value={todo.title}
          onChange={e => {
            onChange({
              ...todo,
              title: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    todoContent = (
      <>
        {todo.title}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={todo.done}
        onChange={e => {
          onChange({
            ...todo,
            done: e.target.checked
          });
        }}
      />
      {todoContent}
      <button onClick={() => onDelete(todo.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
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

With Immer, you can pick the style that feels the most natural for each separate case.

</Solution>

</Challenges>
