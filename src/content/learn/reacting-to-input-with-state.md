---
title: Reacting to Input with State(واکنش به ورودی با state)
---

<Intro>

React provides a declarative way to manipulate the UI. Instead of manipulating individual pieces of the UI directly, you describe the different states that your component can be in, and switch between them in response to the user input. This is similar to how designers think about the UI.
///////////
React روشی را برای دستکاری UI ارائه می دهد. به جای دستکاری تک تک تکه‌های رابط کاربری مستقیم، state‌های مختلفی را که جزء شما می‌تواند در آن قرار داشته باشد، توصیف می‌کنید و در پاسخ به ورودی کاربر، بین آن‌ها جابه‌جا می‌شوید. این شبیه طرز فکر طراحان در مورد رابط کاربری است.
////////////////////////
React یک روش اظهاری برای کنترل کردن رابط کاربری فراهم می کند. به جای کنترل مستقیم قسمت های مختلف رابط کاربری، شما وضعیت های مختلفی را که کامپوننت شما می تواند در آن ها باشد، توصیف می کنید و در پاسخ به ورودی کاربر بین آن ها تغییر می دهید. این مشابه طرز فکر طراحان در مورد رابط کاربری است.

</Intro>

<YouWillLearn>

* How declarative UI programming differs from imperative UI programming
* How to enumerate the different visual states your component can be in
* How to trigger the changes between the different visual states from code

/////////////////
* تفاوت برنامه نویسی UI اعلانی با برنامه نویسی ضروری UI
* نحوه برشمردن حالات بصری مختلف مولفه شما می تواند در آن باشد
* نحوه ایجاد تغییرات بین حالات بصری مختلف از کد

/////////////////////
به جای کنترل مستقیم قسمت های مختلف رابط کاربری، React یک روش اظهاری برای کنترل کردن رابط کاربری فراهم می کند. شما وضعیت های مختلفی را که کامپوننت شما می تواند در آن ها باشد، توصیف می کنید و در پاسخ به ورودی کاربر بین آن ها تغییر می دهید. این با طرز فکر طراحان در مورد رابط کاربری مشابه است.

برای نمایش وضعیت های مختلف کامپوننت خود، شما می توانید از یک شیء state استفاده کنید. state شامل وضعیت های مختلفی است که کامپوننت شما می تواند در آن ها باشد. شما می توانید با استفاده از setState()، وضعیت کامپوننت خود را تغییر دهید.

برای تغییر بین وضعیت های مختلف، شما می توانید از رویدادهای کاربری مانند کلیک کردن یک دکمه استفاده کنید. شما می توانید با استفاده از setState()، وضعیت کامپوننت خود را تغییر دهید و به وضعیت دیگری تغییر دهید.

</YouWillLearn>

## How declarative UI compares to imperative(چگونه رابط کاربری اعلانی با امری مقایسه می شود) {/*how-declarative-ui-compares-to-imperative*/}

When you design UI interactions, you probably think about how the UI *changes* in response to user actions. Consider a form that lets the user submit an answer:
///////////////
وقتی تعاملات رابط کاربری را طراحی می کنید، احتمالاً به این فکر می کنید که چگونه رابط کاربری *تغییر* می کند در پاسخ به اقدامات کاربر. فرمی را در نظر بگیرید که به کاربر اجازه می دهد پاسخ ارسال کند:
///////////////
وقتی شما تعاملات رابط کاربری را طراحی می کنید، احتمالاً به این فکر می کنید که رابط کاربری به چه شکل در پاسخ به اقدامات کاربر تغییر می کند. یک فرم را در نظر بگیرید که به کاربر اجازه می دهد یک پاسخ را ارسال کند:

* When you type something into the form, the "Submit" button **becomes enabled.**
* When you press "Submit", both the form and the button **become disabled,** and a spinner **appears.**
* If the network request succeeds, the form **gets hidden,** and the "Thank you" message **appears.**
* If the network request fails, an error message **appears,** and the form **becomes enabled** again.

/////////////////
* وقتی چیزی را در فرم تایپ می کنید، دکمه "ارسال" **فعال می شود.**
* وقتی "ارسال" را فشار می دهید، هم فرم و هم دکمه **غیرفعال می شوند** و یک چرخنده ** ظاهر می شود.**
* اگر درخواست شبکه با موفقیت انجام شود، فرم **مخفی می شود** و پیام "متشکرم" ** ظاهر می شود.**
* اگر درخواست شبکه ناموفق باشد، یک پیغام خطا ** ظاهر می شود،** و فرم ** دوباره فعال می شود**.

//////////////////////
* وقتی شما چیزی را در فرم تایپ می کنید، دکمه "ارسال" **فعال می شود**.
* وقتی شما "ارسال" را فشار می دهید، هر دو فرم و دکمه **غیرفعال می شوند** و یک spinner **ظاهر می شود**.
* اگر درخواست شبکه موفق باشد، فرم **مخفی می شود** و پیام "با تشکر" **ظاهر می شود**.
* اگر درخواست شبکه ناموفق باشد، یک پیام خطا **ظاهر می شود** و فرم **فعال می شود**.


In **imperative programming,** the above corresponds directly to how you implement interaction. You have to write the exact instructions to manipulate the UI depending on what just happened. Here's another way to think about this: imagine riding next to someone in a car and telling them turn by turn where to go.
///////////////
در **برنامه نویسی امری** موارد فوق مستقیماً با نحوه اجرای تعامل شما مطابقت دارد. بسته به اتفاقی که اخیراً رخ داده است، باید دستورالعمل های دقیق را برای دستکاری UI بنویسید. در اینجا یک راه دیگر برای فکر کردن در مورد این وجود دارد: تصور کنید که در کنار شخصی در ماشین سوار می شوید و به آنها می گویید که به نوبت به کجا بروید.
//////////////////////////
در برنامه نویسی امری، مطابق با آنچه که اتفاق افتاده است، باید دستورات دقیقی برای کنترل رابط کاربری بنویسید. به عبارت دیگر، باید دستورات دقیقی برای کنترل قسمت های مختلف رابط کاربری بنویسید. یک روش دیگر برای فهمیدن این موضوع این است: تصور کنید که در کنار کسی در یک ماشین نشسته اید و به او به ترتیب می گویید کجا باید بپیچد.

<Illustration src="/images/docs/illustrations/i_imperative-ui-programming.png"  alt="In a car driven by an anxious-looking person representing JavaScript, a passenger orders the driver to execute a sequence of complicated turn by turn navigations." />

They don't know where you want to go, they just follow your commands. (And if you get the directions wrong, you end up in the wrong place!) It's called *imperative* because you have to "command" each element, from the spinner to the button, telling the computer *how* to update the UI.
////////////////
آنها نمی دانند شما کجا می خواهید بروید، فقط دستورات شما را دنبال می کنند. (و اگر مسیرها را اشتباه دریافت کنید، در جای اشتباهی قرار می گیرید!) به آن *ضروری* می گویند زیرا باید به هر عنصر، از چرخان تا دکمه، دستور دهید و به رایانه بگویید *چگونه* رابط کاربری را به روز کند. .
/////////////////////
در برنامه نویسی امری، شما باید دستورات دقیقی برای کنترل رابط کاربری بنویسید. به عبارت دیگر، باید دستورات دقیقی برای کنترل قسمت های مختلف رابط کاربری بنویسید. یک روش دیگر برای فهمیدن این موضوع این است: تصور کنید که در کنار کسی در یک ماشین نشسته اید و به او به ترتیب می گویید کجا باید بپیچد. (و اگر دستورات را اشتباه بدهید، در جای اشتباهی قرار می گیرید!) به این دلیل به آن امری گفته می شود که شما باید به هر المان، از spinner تا button، دستور دهید و به کامپیوتر بگویید چگونه رابط کاربری را به روز کند.

In this example of imperative UI programming, the form is built *without* React. It only uses the browser [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model):
//////////////
در این مثال از برنامه نویسی واجب UI، فرم *بدون* React ساخته شده است. فقط از مرورگر [DOM] (https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) استفاده می کند:
///////////////////////
در برنامه نویسی امری، در این مثال از برنامه نویسی رابط کاربری، فرم بدون استفاده از React ساخته شده است. تنها از DOM مرورگر استفاده می کند.

<Sandpack>

```js index.js active
async function handleFormSubmit(e) {
  e.preventDefault();
  disable(textarea);
  disable(button);
  show(loadingMessage);
  hide(errorMessage);
  try {
    await submitForm(textarea.value);
    show(successMessage);
    hide(form);
  } catch (err) {
    show(errorMessage);
    errorMessage.textContent = err.message;
  } finally {
    hide(loadingMessage);
    enable(textarea);
    enable(button);
  }
}

function handleTextareaChange() {
  if (textarea.value.length === 0) {
    disable(button);
  } else {
    enable(button);
  }
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

function enable(el) {
  el.disabled = false;
}

function disable(el) {
  el.disabled = true;
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (answer.toLowerCase() === 'istanbul') {
        resolve();
      } else {
        reject(new Error('Good guess but a wrong answer. Try again!'));
      }
    }, 1500);
  });
}

let form = document.getElementById('form');
let textarea = document.getElementById('textarea');
let button = document.getElementById('button');
let loadingMessage = document.getElementById('loading');
let errorMessage = document.getElementById('error');
let successMessage = document.getElementById('success');
form.onsubmit = handleFormSubmit;
textarea.oninput = handleTextareaChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <h2>City quiz</h2>
  <p>
    What city is located on two continents?
  </p>
  <textarea id="textarea"></textarea>
  <br />
  <button id="button" disabled>Submit</button>
  <p id="loading" style="display: none">Loading...</p>
  <p id="error" style="display: none; color: red;"></p>
</form>
<h1 id="success" style="display: none">That's right!</h1>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
</style>
```

</Sandpack>

Manipulating the UI imperatively works well enough for isolated examples, but it gets exponentially more difficult to manage in more complex systems. Imagine updating a page full of different forms like this one. Adding a new UI element or a new interaction would require carefully checking all existing code to make sure you haven't introduced a bug (for example, forgetting to show or hide something).
///////////////
دستکاری UI به اندازه کافی برای نمونه های مجزا کار می کند، اما مدیریت آن در سیستم های پیچیده تر به طور تصاعدی دشوارتر می شود. تصور کنید صفحه ای پر از فرم های مختلف مانند این را به روز کنید. افزودن یک عنصر رابط کاربری جدید یا یک تعامل جدید مستلزم بررسی دقیق تمام کدهای موجود است تا مطمئن شوید که اشکالی را معرفی نکرده اید (مثلاً فراموش کرده اید چیزی را نشان دهید یا پنهان کنید).
///////////////////
در برنامه نویسی امری، کنترل مستقیم قسمت های مختلف رابط کاربری برای مثال های مجزا به خوبی کار می کند، اما در سیستم های پیچیده، مدیریت آن به صورت نمایه افزایی سخت تر می شود. تصور کنید که باید یک صفحه پر از فرم های مختلف مانند این را به روز کنید. اضافه کردن یک المان رابط کاربری جدید یا یک تعامل جدید نیاز به بررسی دقیق کل کد های موجود دارد تا مطمئن شوید که باگی (مانند فراموشی نمایش یا عدم نمایش چیزی) به برنامه شما اضافه نشده است.

React was built to solve this problem.
////////////
React برای حل این مشکل ساخته شده است.

In React, you don't directly manipulate the UI--meaning you don't enable, disable, show, or hide components directly. Instead, you **declare what you want to show,** and React figures out how to update the UI. Think of getting into a taxi and telling the driver where you want to go instead of telling them exactly where to turn. It's the driver's job to get you there, and they might even know some shortcuts you haven't considered!
/////////////////////
در React، شما مستقیماً UI را دستکاری نمی‌کنید - به این معنی که شما مؤلفه‌ها را مستقیماً فعال، غیرفعال، نمایش یا پنهان نمی‌کنید. درعوض، **آنچه را که می خواهید نشان دهید را اعلام می کنید** و React نحوه به روز رسانی UI را مشخص می کند. به این فکر کنید که سوار تاکسی شوید و به جای اینکه بگویید دقیقاً کجا باید بپیچید، به راننده بگویید کجا می خواهید بروید. این وظیفه راننده است که شما را به آنجا برساند و حتی ممکن است میانبرهایی را بدانند که شما در نظر نگرفته اید!
///////////////////////////////
در React، شما به طور مستقیم رابط کاربری را کنترل نمی کنید - به این معنی که شما به طور مستقیم قسمت های مختلف رابط کاربری را فعال، غیرفعال، نمایش یا عدم نمایش نمی کنید. به جای آن، شما تعریف می کنید که چه می خواهید نشان دهید و React شیوه به روزرسانی رابط کاربری را تعیین می کند. تصور کنید که در تاکسی نشسته اید و به راننده می گویید کجا می خواهید بروید به جای اینکه به دقت به او بگویید کجا باید بپیچید. کار راننده است که شما را به مقصد برساند و او حتی ممکن است مسیرهای کوتاهی را بشناسد که شما در نظر نگرفته اید!

<Illustration src="/images/docs/illustrations/i_declarative-ui-programming.png" alt="In a car driven by React, a passenger asks to be taken to a specific place on the map. React figures out how to do that." />

## Thinking about UI declaratively(فکر کردن در مورد UI به صورت اعلامی) {/*thinking-about-ui-declaratively*/}

You've seen how to implement a form imperatively above. To better understand how to think in React, you'll walk through reimplementing this UI in React below:
////////////
نحوه پیاده سازی یک فرم را در بالا مشاهده کرده اید. برای درک بهتر نحوه تفکر در React، در زیر به پیاده‌سازی مجدد این رابط کاربری در React می‌پردازید:
//////////////////////
شما در بالا دیدید که چگونه یک فرم را به صورت امری پیاده سازی کنید. برای بهتر درک کردن نحوه فکر کردن در React، شما می توانید این رابط کاربری را در React دوباره پیاده سازی کنید.

1. **Identify** your component's different visual states
2. **Determine** what triggers those state changes
3. **Represent** the state in memory using `useState`
4. **Remove** any non-essential state variables
5. **Connect** the event handlers to set the state

//////////////////
1. **شناسایی ** حالات بصری مختلف جزء خود را
2. **تعیین ** چه چیزی باعث این تغییرات state می شود
3. **نمایندگی** state در حافظه با استفاده از "useState".
4. **هر گونه متغیر state غیر ضروری را حذف کنید
5. برای تنظیم state، کنترل کننده های رویداد را ** وصل کنید

/////////////////////
۱. وضعیت های مختلف بصری کامپوننت خود را شناسایی کنید. ۲. تعیین کنید چه چیزی باعث تغییر وضعیت می شود. ۳. وضعیت را در حافظه با استفاده از useState نشان دهید. ۴. متغیرهای وضعیت غیرضروری را حذف کنید. ۵. رویدادهای کاربری را به منظور تنظیم وضعیت به هم وصل کنید.

### Step 1: Identify your component's different visual states(مرحله 1: حالات بصری مختلف جزء خود را شناسایی کنید) {/*step-1-identify-your-components-different-visual-states*/}

In computer science, you may hear about a ["state machine"](https://en.wikipedia.org/wiki/Finite-state_machine) being in one of several “states”. If you work with a designer, you may have seen mockups for different "visual states". React stands at the intersection of design and computer science, so both of these ideas are sources of inspiration.
//////////////////
در علوم کامپیوتر، ممکن است در مورد یک ["ماشین state"] (https://en.wikipedia.org/wiki/Finite-state_machine) در یکی از چندین "وضعیت" بشنوید. اگر با یک طراح کار می کنید، ممکن است ماکت هایی برای "state های بصری" مختلف دیده باشید. React در تقاطع طراحی و علوم کامپیوتر قرار دارد، بنابراین هر دوی این ایده ها منبع الهام هستند.
///////////////////////
در علوم کامپیوتر، شاید در مورد یک “ماشین state” که در یکی از چندین “state” قرار دارد، شنیده باشید. اگر با یک طراح کار می کنید، شاید ماکت هایی از وضعیت های بصری مختلف را دیده باشید. React در تلاقی طراحی و علوم کامپیوتر قرار دارد، بنابراین هر دو این ایده ها منبع الهام هستند.

First, you need to visualize all the different "states" of the UI the user might see:
////////////////////
ابتدا، باید تمام «وضعیت‌های» مختلف رابط کاربری را که کاربر ممکن است ببیند، تجسم کنید:

* **Empty**: Form has a disabled "Submit" button.
* **Typing**: Form has an enabled "Submit" button.
* **Submitting**: Form is completely disabled. Spinner is shown.
* **Success**: "Thank you" message is shown instead of a form.
* **Error**: Same as Typing state, but with an extra error message.

///////////////////
* **خالی**: فرم دارای دکمه "ارسال" غیرفعال است.
* **تایپ **: فرم دارای دکمه "ارسال" فعال است.
* **ارسال **: فرم کاملاً غیرفعال است. اسپینر نشان داده شده است.
* **موفقیت**: پیام "متشکرم" به جای فرم نمایش داده می شود.
* **خطا**: مانند state تایپ، اما با یک پیام خطای اضافی.

/////////////////////////////
* **خالی**: دکمه "ارسال" فعال نیست.
* **تایپ**: دکمه "ارسال" فعال است.
* **در حال ارسال**: فرم کاملاً غیرفعال است. Spinner نمایش داده می شود.
* **موفقیت**: پیام "با تشکر" به جای فرم نمایش داده می شود.
* **خطا**: همانند وضعیت تایپ، اما با یک پیام خطای اضافی.

Just like a designer, you'll want to "mock up" or create "mocks" for the different states before you add logic. For example, here is a mock for just the visual part of the form. This mock is controlled by a prop called `status` with a default value of `'empty'`:
////////////////////
درست مانند یک طراح، قبل از اینکه منطق را اضافه کنید، می خواهید برای state های مختلف "ملک آپ" یا "ملک" ایجاد کنید. به عنوان مثال، در اینجا یک تمسخر فقط برای بخش بصری فرم وجود دارد. این ماک توسط یک پایه به نام «وضعیت» با مقدار پیش‌فرض «خالی» کنترل می‌شود:
//////////////////////////
مانند یک طراح، قبل از اضافه کردن منطق، شما باید برای وضعیت های مختلف “mock up” یا “mocks” ایجاد کنید. به عنوان مثال، اینجا یک mock برای بخش بصری فرم وجود دارد. این mock توسط یک prop به نام status با مقدار پیش فرض 'empty' کنترل می شود:

<Sandpack>

```js
export default function Form({
  status = 'empty'
}) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form>
        <textarea />
        <br />
        <button>
          Submit
        </button>
      </form>
    </>
  )
}
```

</Sandpack>

You could call that prop anything you like, the naming is not important. Try editing `status = 'empty'` to `status = 'success'` to see the success message appear. Mocking lets you quickly iterate on the UI before you wire up any logic. Here is a more fleshed out prototype of the same component, still "controlled" by the `status` prop:
///////////////////////
شما می توانید آن پایه را هر چیزی که دوست دارید صدا کنید، نامگذاری آن مهم نیست. "وضعیت = "خالی" را به "وضعیت = "موفقیت"" ویرایش کنید تا پیام موفقیت ظاهر شود. تمسخر به شما این امکان را می‌دهد تا قبل از اینکه هر منطقی را وارد کنید، سریعاً روی UI تکرار کنید. در اینجا یک نمونه اولیه بیشتر از همان مؤلفه وجود دارد که هنوز توسط «وضعیت» «کنترل» می‌شود:
/////////////////////////////
شما می توانید این prop را به هر نامی که دوست دارید صدا بزنید، نامگذاری مهم نیست. برای دیدن پیام موفقیت، status = 'empty' را به status = 'success' تغییر دهید. Mocking به شما اجازه می دهد تا قبل از اضافه کردن منطق، به سرعت روی رابط کاربری تکرار شوید. در ادامه، یک نمونه پیش نمایش بیشتر از همان کامپوننت وجود دارد، همچنان توسط status prop “کنترل” می شود:

<Sandpack>

```js
export default function Form({
  // Try 'submitting', 'error', 'success':
  status = 'empty'
}) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form>
        <textarea disabled={
          status === 'submitting'
        } />
        <br />
        <button disabled={
          status === 'empty' ||
          status === 'submitting'
        }>
          Submit
        </button>
        {status === 'error' &&
          <p className="Error">
            Good guess but a wrong answer. Try again!
          </p>
        }
      </form>
      </>
  );
}
```

```css
.Error { color: red; }
```

</Sandpack>

<DeepDive>

#### Displaying many visual states at once(نمایش بسیاری از state های بصری در یک زمان) {/*displaying-many-visual-states-at-once*/}

If a component has a lot of visual states, it can be convenient to show them all on one page:
/////////////////////
اگر یک مؤلفه state های بصری زیادی داشته باشد، نشان دادن همه آنها در یک صفحه می تواند راحت باشد:

<Sandpack>

```js App.js active
import Form from './Form.js';

let statuses = [
  'empty',
  'typing',
  'submitting',
  'success',
  'error',
];

export default function App() {
  return (
    <>
      {statuses.map(status => (
        <section key={status}>
          <h4>Form ({status}):</h4>
          <Form status={status} />
        </section>
      ))}
    </>
  );
}
```

```js Form.js
export default function Form({ status }) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <form>
      <textarea disabled={
        status === 'submitting'
      } />
      <br />
      <button disabled={
        status === 'empty' ||
        status === 'submitting'
      }>
        Submit
      </button>
      {status === 'error' &&
        <p className="Error">
          Good guess but a wrong answer. Try again!
        </p>
      }
    </form>
  );
}
```

```css
section { border-bottom: 1px solid #aaa; padding: 20px; }
h4 { color: #222; }
body { margin: 0; }
.Error { color: red; }
```

</Sandpack>

Pages like this are often called "living styleguides" or "storybooks".
//////////////////
صفحاتی مانند این اغلب "راهنماهای سبک زندگی" یا "کتاب داستان" نامیده می شوند.

</DeepDive>

### Step 2: Determine what triggers those state changes(مرحله 2: تعیین کنید چه چیزی باعث این تغییرات state می شود) {/*step-2-determine-what-triggers-those-state-changes*/}

You can trigger state updates in response to two kinds of inputs:
/////////////////
می‌توانید به‌روزرسانی‌های state را در پاسخ به دو نوع ورودی فعال کنید:

* **Human inputs,** like clicking a button, typing in a field, navigating a link.
* **Computer inputs,** like a network response arriving, a timeout completing, an image loading.

////////////////////
* **ورودی های انسانی،** مانند کلیک کردن روی یک دکمه، تایپ کردن در یک فیلد، پیمایش یک پیوند.
* **ورودی های کامپیوتر،** مانند دریافت پاسخ شبکه، اتمام زمان پایان، بارگیری تصویر.

<IllustrationBlock>
  <Illustration caption="Human inputs" alt="A finger." src="/images/docs/illustrations/i_inputs1.png" />
  <Illustration caption="Computer inputs" alt="Ones and zeroes." src="/images/docs/illustrations/i_inputs2.png" />
</IllustrationBlock>

In both cases, **you must set [state variables](/learn/state-a-components-memory#anatomy-of-usestate) to update the UI.** For the form you're developing, you will need to change state in response to a few different inputs:
////////////////
در هر دو مورد، **شما باید [متغیرهای state](/learn/state-a-components-memory#anatomy-of-usestate) را برای به روز رسانی رابط کاربری تنظیم کنید.** برای فرمی که در حال توسعه هستید، باید تغییر state در پاسخ به چند ورودی مختلف:

* **Changing the text input** (human) should switch it from the *Empty* state to the *Typing* state or back, depending on whether the text box is empty or not.
* **Clicking the Submit button** (human) should switch it to the *Submitting* state.
* **Successful network response** (computer) should switch it to the *Success* state.
* **Failed network response** (computer) should switch it to the *Error* state with the matching error message.

/////////////////
* **تغییر ورودی متن** (انسان) باید بسته به خالی بودن یا نبودن کادر متن، آن را از state *خالی* به state *تایپ* تغییر دهید.
* **با کلیک بر روی دکمه ارسال** (انسان) باید آن را به state *ارسال* تغییر دهید.
* **پاسخ شبکه موفق** (کامپیوتر) باید آن را به state *موفقیت* تغییر دهد.
* **پاسخ شبکه ناموفق ** (کامپیوتر) باید با پیام خطای منطبق، آن را به state *خطا* تغییر دهد.

<Note>

Notice that human inputs often require [event handlers](/learn/responding-to-events)!
/////////////////////
توجه داشته باشید که ورودی های انسانی اغلب به [کنترل کننده رویداد](/learn/responding-to-events) نیاز دارند!

</Note>

To help visualize this flow, try drawing each state on paper as a labeled circle, and each change between two states as an arrow. You can sketch out many flows this way and sort out bugs long before implementation.
////////////////////
برای کمک به تجسم این جریان، سعی کنید هر state را روی کاغذ به عنوان یک دایره برچسب‌گذاری شده ترسیم کنید و هر state را به عنوان یک فلش بین دو state تغییر دهید. شما می توانید بسیاری از جریان ها را از این طریق ترسیم کنید و باگ ها را مدت ها قبل از اجرا مرتب کنید.

<DiagramGroup>

<Diagram name="responding_to_input_flow" height={350} width={688} alt="Flow chart moving left to right with 5 nodes. The first node labeled 'empty' has one edge labeled 'start typing' connected to a node labeled 'typing'. That node has one edge labeled 'press submit' connected to a node labeled 'submitting', which has two edges. The left edge is labeled 'network error' connecting to a node labeled 'error'. The right edge is labeled 'network success' connecting to a node labeled 'success'.">

Form states
//////////////
state ها را تشکیل دهید

</Diagram>

</DiagramGroup>

### Step 3: Represent the state in memory with `useState`(مرحله 3: state را در حافظه با «useState» نشان دهید) {/*step-3-represent-the-state-in-memory-with-usestate*/}

Next you'll need to represent the visual states of your component in memory with [`useState`.](/reference/react/useState) Simplicity is key: each piece of state is a "moving piece", and **you want as few "moving pieces" as possible.** More complexity leads to more bugs!
////////////////////
در مرحله بعد باید state های بصری جزء خود را در حافظه با [`useState` نشان دهید.](/reference/react/useState) سادگی کلید است: هر قطعه state یک "قطعه متحرک" است، و **شما می خواهید تا آنجا که ممکن است تعداد کمی "تکه های متحرک".** پیچیدگی بیشتر منجر به اشکالات بیشتر می شود!

Start with the state that *absolutely must* be there. For example, you'll need to store the `answer` for the input, and the `error` (if it exists) to store the last error:
//////////////////////
با stateی شروع کنید که *کاملاً باید* وجود داشته باشد. برای مثال، باید «پاسخ» را برای ورودی و «خطا» (در صورت وجود) را برای ذخیره آخرین خطا ذخیره کنید:

```js
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
```

Then, you'll need a state variable representing which one of the visual states that you want to display. There's usually more than a single way to represent that in memory, so you'll need to experiment with it.

If you struggle to think of the best way immediately, start by adding enough state that you're *definitely* sure that all the possible visual states are covered:
//////////////////////
سپس، به یک متغیر state نیاز دارید که نشان دهد کدام یک از state های بصری را می خواهید نمایش دهید. معمولاً بیش از یک راه برای نمایش آن در حافظه وجود دارد، بنابراین باید آن را آزمایش کنید.

اگر در تلاش هستید که فوراً به بهترین راه فکر کنید، با اضافه کردن state کافی شروع کنید که *قطعا* مطمئن باشید که تمام state های بصری ممکن پوشش داده شده است:

```js
const [isEmpty, setIsEmpty] = useState(true);
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isError, setIsError] = useState(false);
```

Your first idea likely won't be the best, but that's ok--refactoring state is a part of the process!
//////////////////
احتمالاً اولین ایده شما بهترین نخواهد بود، اما این مشکلی ندارد - state refactoring بخشی از فرآیند است!

### Step 4: Remove any non-essential state variables(مرحله 4: هر متغیر state غیر ضروری را حذف کنید) {/*step-4-remove-any-non-essential-state-variables*/}

You want to avoid duplication in the state content so you're only tracking what is essential. Spending a little time on refactoring your state structure will make your components easier to understand, reduce duplication, and avoid unintended meanings. Your goal is to **prevent the cases where the state in memory doesn't represent any valid UI that you'd want a user to see.** (For example, you never want to show an error message and disable the input at the same time, or the user won't be able to correct the error!)
//////////////////////////////////////////
شما می خواهید از تکراری شدن در محتوای state جلوگیری کنید، بنابراین فقط موارد ضروری را دنبال می کنید. صرف کمی زمان برای بازسازی ساختار state خود، درک اجزای شما را آسان‌تر می‌کند، تکرار را کاهش می‌دهد و از معانی ناخواسته جلوگیری می‌کند. هدف شما **جلوگیری از مواردی است که وضعیت موجود در حافظه نشان دهنده هیچ رابط کاربری معتبری نیست که می خواهید کاربر آن را ببیند.** (به عنوان مثال، شما هرگز نمی خواهید یک پیام خطا نشان دهید و ورودی را در آن غیرفعال کنید. در همان زمان، یا کاربر قادر به تصحیح خطا نخواهد بود!)

Here are some questions you can ask about your state variables:
/////////////////////
در اینجا چند سوال وجود دارد که می توانید در مورد متغیرهای state خود بپرسید:

* **Does this state cause a paradox?** For example, `isTyping` and `isSubmitting` can't both be `true`. A paradox usually means that the state is not constrained enough. There are four possible combinations of two booleans, but only three correspond to valid states. To remove the "impossible" state, you can combine these into a `status` that must be one of three values: `'typing'`, `'submitting'`, or `'success'`.
* **Is the same information available in another state variable already?** Another paradox: `isEmpty` and `isTyping` can't be `true` at the same time. By making them separate state variables, you risk them going out of sync and causing bugs. Fortunately, you can remove `isEmpty` and instead check `answer.length === 0`.
* **Can you get the same information from the inverse of another state variable?** `isError` is not needed because you can check `error !== null` instead.

////////////////////////
* **آیا این state باعث پارادوکس می شود؟** برای مثال، «isTyping» و «isSubmitting» هر دو نمی توانند «درست» باشند. پارادوکس معمولاً به این معنی است که دولت به اندازه کافی محدود نیست. چهار ترکیب ممکن از دو بولی وجود دارد، اما تنها سه مورد با state های معتبر مطابقت دارند. برای حذف state «غیرممکن»، می‌توانید اینها را در یک «وضعیت» ترکیب کنید که باید یکی از سه مقدار باشد: «تایپ کردن»، «ارسال» یا «موفقیت».
* **آیا همان اطلاعات قبلاً در متغیر state دیگری موجود است؟** پارادوکس دیگر: «isEmpty» و «isTyping» نمی‌توانند همزمان «درست» باشند. با جدا کردن آنها از متغیرهای state، خطر عدم هماهنگی آنها و ایجاد باگ را دارید. خوشبختانه، می‌توانید «isEmpty» را حذف کنید و به جای آن «answer.length === 0» را علامت بزنید.
* **آیا می توانید همان اطلاعات را از معکوس متغیر state دیگری دریافت کنید؟** به 'isError' نیازی نیست زیرا می توانید به جای آن 'error !== null' را علامت بزنید.

After this clean-up, you're left with 3 (down from 7!) *essential* state variables:
////////////////
پس از این پاکسازی، شما با 3 متغیر state *ضروری* (از 7 کاهش!) باقی خواهید ماند:

```js
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
const [status, setStatus] = useState('typing'); // 'typing', 'submitting', or 'success'
```

You know they are essential, because you can't remove any of them without breaking the functionality.
/////////////////////////
می دانید که آنها ضروری هستند، زیرا نمی توانید هیچ یک از آنها را بدون شکستن عملکرد حذف کنید.

<DeepDive>

#### Eliminating “impossible” states with a reducer(از بین بردن state های "غیر ممکن" با کاهنده) {/*eliminating-impossible-states-with-a-reducer*/}

These three variables are a good enough representation of this form's state. However, there are still some intermediate states that don't fully make sense. For example, a non-null `error` doesn't make sense when `status` is `'success'`. To model the state more precisely, you can [extract it into a reducer.](/learn/extracting-state-logic-into-a-reducer) Reducers let you unify multiple state variables into a single object and consolidate all the related logic!
///////////////////////
این سه متغیر به اندازه کافی نشان دهنده وضعیت این فرم هستند. با این حال، هنوز برخی از state های میانی وجود دارند که کاملاً منطقی نیستند. به عنوان مثال، زمانی که «وضعیت» «موفقیت» باشد، «خطا» غیر تهی معنا ندارد. برای مدل‌سازی دقیق‌تر state، می‌توانید [آن را به یک کاهنده استخراج کنید.](/learn/extracting-state-logic-into-a-reducer) کاهنده‌ها به شما امکان می‌دهند چندین متغیر state را در یک شی واحد متحد کرده و همه منطق مرتبط را ادغام کنید. !

</DeepDive>

### Step 5: Connect the event handlers to set state(مرحله 5: کنترل کننده های رویداد را به state تنظیم وصل کنید) {/*step-5-connect-the-event-handlers-to-set-state*/}

Lastly, create event handlers that update the state. Below is the final form, with all event handlers wired up:
///////////////////////
در نهایت، کنترل کننده های رویداد ایجاد کنید که وضعیت را به روز می کند. در زیر فرم نهایی با سیم‌کشی تمامی کنترل‌کننده‌های رویداد آمده است:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [answer, setAnswer] = useState('');
  const [error, setError] = useState(null);
  const [status, setStatus] = useState('typing');

  if (status === 'success') {
    return <h1>That's right!</h1>
  }

  async function handleSubmit(e) {
    e.preventDefault();
    setStatus('submitting');
    try {
      await submitForm(answer);
      setStatus('success');
    } catch (err) {
      setStatus('typing');
      setError(err);
    }
  }

  function handleTextareaChange(e) {
    setAnswer(e.target.value);
  }

  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form onSubmit={handleSubmit}>
        <textarea
          value={answer}
          onChange={handleTextareaChange}
          disabled={status === 'submitting'}
        />
        <br />
        <button disabled={
          answer.length === 0 ||
          status === 'submitting'
        }>
          Submit
        </button>
        {error !== null &&
          <p className="Error">
            {error.message}
          </p>
        }
      </form>
    </>
  );
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let shouldError = answer.toLowerCase() !== 'lima'
      if (shouldError) {
        reject(new Error('Good guess but a wrong answer. Try again!'));
      } else {
        resolve();
      }
    }, 1500);
  });
}
```

```css
.Error { color: red; }
```

</Sandpack>

Although this code is longer than the original imperative example, it is much less fragile. Expressing all interactions as state changes lets you later introduce new visual states without breaking existing ones. It also lets you change what should be displayed in each state without changing the logic of the interaction itself.
//////////////////////
اگرچه این کد طولانی‌تر از مثال ضروری اصلی است، اما بسیار کمتر شکننده است. بیان تمام فعل و انفعالات به عنوان تغییرات state به شما امکان می دهد بعداً state های بصری جدیدی را بدون شکستن state های موجود معرفی کنید. همچنین به شما امکان می دهد آنچه را که باید در هر state نمایش داده شود بدون تغییر منطق خود تعامل تغییر دهید.

<Recap>

* Declarative programming means describing the UI for each visual state rather than micromanaging the UI (imperative).
* When developing a component:
  1. Identify all its visual states.
  2. Determine the human and computer triggers for state changes.
  3. Model the state with `useState`.
  4. Remove non-essential state to avoid bugs and paradoxes.
  5. Connect the event handlers to set state.

//////////////////
* برنامه نویسی اعلانی به معنای توصیف UI برای هر state بصری به جای مدیریت ریز UI (ضروری) است.
* هنگام توسعه یک جزء:
  1. تمام حالات بصری آن را شناسایی کنید.
  2. محرک های انسان و کامپیوتر را برای تغییرات state تعیین کنید.
  3. state را با «useState» مدل کنید.
  4. حذف state غیر ضروری برای جلوگیری از اشکالات و پارادوکس.
  5. کنترل کننده های رویداد را به state تنظیم وصل کنید.

</Recap>



<Challenges>

#### Add and remove a CSS class {/*add-and-remove-a-css-class*/}

Make it so that clicking on the picture *removes* the `background--active` CSS class from the outer `<div>`, but *adds* the `picture--active` class to the `<img>`. Clicking the background again should restore the original CSS classes.

Visually, you should expect that clicking on the picture removes the purple background and highlights the picture border. Clicking outside the picture highlights the background, but removes the picture border highlight.

<Sandpack>

```js
export default function Picture() {
  return (
    <div className="background background--active">
      <img
        className="picture"
        alt="Rainbow houses in Kampung Pelangi, Indonesia"
        src="https://i.imgur.com/5qwVYb1.jpeg"
      />
    </div>
  );
}
```

```css
body { margin: 0; padding: 0; height: 250px; }

.background {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #eee;
}

.background--active {
  background: #a6b5ff;
}

.picture {
  width: 200px;
  height: 200px;
  border-radius: 10px;
}

.picture--active {
  border: 5px solid #a6b5ff;
}
```

</Sandpack>

<Solution>

This component has two visual states: when the image is active, and when the image is inactive:

* When the image is active, the CSS classes are `background` and `picture picture--active`.
* When the image is inactive, the CSS classes are `background background--active` and `picture`.

A single boolean state variable is enough to remember whether the image is active. The original task was to remove or add CSS classes. However, in React you need to *describe* what you want to see rather than *manipulate* the UI elements. So you need to calculate both CSS classes based on the current state. You also need to [stop the propagation](/learn/responding-to-events#stopping-propagation) so that clicking the image doesn't register as a click on the background.

Verify that this version works by clicking the image and then outside of it:

<Sandpack>

```js
import { useState } from 'react';

export default function Picture() {
  const [isActive, setIsActive] = useState(false);

  let backgroundClassName = 'background';
  let pictureClassName = 'picture';
  if (isActive) {
    pictureClassName += ' picture--active';
  } else {
    backgroundClassName += ' background--active';
  }

  return (
    <div
      className={backgroundClassName}
      onClick={() => setIsActive(false)}
    >
      <img
        onClick={e => {
          e.stopPropagation();
          setIsActive(true);
        }}
        className={pictureClassName}
        alt="Rainbow houses in Kampung Pelangi, Indonesia"
        src="https://i.imgur.com/5qwVYb1.jpeg"
      />
    </div>
  );
}
```

```css
body { margin: 0; padding: 0; height: 250px; }

.background {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #eee;
}

.background--active {
  background: #a6b5ff;
}

.picture {
  width: 200px;
  height: 200px;
  border-radius: 10px;
  border: 5px solid transparent;
}

.picture--active {
  border: 5px solid #a6b5ff;
}
```

</Sandpack>

Alternatively, you could return two separate chunks of JSX:

<Sandpack>

```js
import { useState } from 'react';

export default function Picture() {
  const [isActive, setIsActive] = useState(false);
  if (isActive) {
    return (
      <div
        className="background"
        onClick={() => setIsActive(false)}
      >
        <img
          className="picture picture--active"
          alt="Rainbow houses in Kampung Pelangi, Indonesia"
          src="https://i.imgur.com/5qwVYb1.jpeg"
          onClick={e => e.stopPropagation()}
        />
      </div>
    );
  }
  return (
    <div className="background background--active">
      <img
        className="picture"
        alt="Rainbow houses in Kampung Pelangi, Indonesia"
        src="https://i.imgur.com/5qwVYb1.jpeg"
        onClick={() => setIsActive(true)}
      />
    </div>
  );
}
```

```css
body { margin: 0; padding: 0; height: 250px; }

.background {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #eee;
}

.background--active {
  background: #a6b5ff;
}

.picture {
  width: 200px;
  height: 200px;
  border-radius: 10px;
  border: 5px solid transparent;
}

.picture--active {
  border: 5px solid #a6b5ff;
}
```

</Sandpack>

Keep in mind that if two different JSX chunks describe the same tree, their nesting (first `<div>` → first `<img>`) has to line up. Otherwise, toggling `isActive` would recreate the whole tree below and [reset its state.](/learn/preserving-and-resetting-state) This is why, if a similar JSX tree gets returned in both cases, it is better to write them as a single piece of JSX.

</Solution>

#### Profile editor {/*profile-editor*/}

Here is a small form implemented with plain JavaScript and DOM. Play with it to understand its behavior:

<Sandpack>

```js index.js active
function handleFormSubmit(e) {
  e.preventDefault();
  if (editButton.textContent === 'Edit Profile') {
    editButton.textContent = 'Save Profile';
    hide(firstNameText);
    hide(lastNameText);
    show(firstNameInput);
    show(lastNameInput);
  } else {
    editButton.textContent = 'Edit Profile';
    hide(firstNameInput);
    hide(lastNameInput);
    show(firstNameText);
    show(lastNameText);
  }
}

function handleFirstNameChange() {
  firstNameText.textContent = firstNameInput.value;
  helloText.textContent = (
    'Hello ' +
    firstNameInput.value + ' ' +
    lastNameInput.value + '!'
  );
}

function handleLastNameChange() {
  lastNameText.textContent = lastNameInput.value;
  helloText.textContent = (
    'Hello ' +
    firstNameInput.value + ' ' +
    lastNameInput.value + '!'
  );
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

let form = document.getElementById('form');
let editButton = document.getElementById('editButton');
let firstNameInput = document.getElementById('firstNameInput');
let firstNameText = document.getElementById('firstNameText');
let lastNameInput = document.getElementById('lastNameInput');
let lastNameText = document.getElementById('lastNameText');
let helloText = document.getElementById('helloText');
form.onsubmit = handleFormSubmit;
firstNameInput.oninput = handleFirstNameChange;
lastNameInput.oninput = handleLastNameChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <label>
    First name:
    <b id="firstNameText">Jane</b>
    <input
      id="firstNameInput"
      value="Jane"
      style="display: none">
  </label>
  <label>
    Last name:
    <b id="lastNameText">Jacobs</b>
    <input
      id="lastNameInput"
      value="Jacobs"
      style="display: none">
  </label>
  <button type="submit" id="editButton">Edit Profile</button>
  <p><i id="helloText">Hello, Jane Jacobs!</i></p>
</form>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
label { display: block; margin-bottom: 20px; }
</style>
```

</Sandpack>

This form switches between two modes: in the editing mode, you see the inputs, and in the viewing mode, you only see the result. The button label changes between "Edit" and "Save" depending on the mode you're in. When you change the inputs, the welcome message at the bottom updates in real time.

Your task is to reimplement it in React in the sandbox below. For your convenience, the markup was already converted to JSX, but you'll need to make it show and hide the inputs like the original does.

Make sure that it updates the text at the bottom, too!

<Sandpack>

```js
export default function EditProfile() {
  return (
    <form>
      <label>
        First name:{' '}
        <b>Jane</b>
        <input />
      </label>
      <label>
        Last name:{' '}
        <b>Jacobs</b>
        <input />
      </label>
      <button type="submit">
        Edit Profile
      </button>
      <p><i>Hello, Jane Jacobs!</i></p>
    </form>
  );
}
```

```css
label { display: block; margin-bottom: 20px; }
```

</Sandpack>

<Solution>

You will need two state variables to hold the input values: `firstName` and `lastName`. You're also going to need an `isEditing` state variable that holds whether to display the inputs or not. You should _not_ need a `fullName` variable because the full name can always be calculated from the `firstName` and the `lastName`.

Finally, you should use [conditional rendering](/learn/conditional-rendering) to show or hide the inputs depending on `isEditing`.

<Sandpack>

```js
import { useState } from 'react';

export default function EditProfile() {
  const [isEditing, setIsEditing] = useState(false);
  const [firstName, setFirstName] = useState('Jane');
  const [lastName, setLastName] = useState('Jacobs');

  return (
    <form onSubmit={e => {
      e.preventDefault();
      setIsEditing(!isEditing);
    }}>
      <label>
        First name:{' '}
        {isEditing ? (
          <input
            value={firstName}
            onChange={e => {
              setFirstName(e.target.value)
            }}
          />
        ) : (
          <b>{firstName}</b>
        )}
      </label>
      <label>
        Last name:{' '}
        {isEditing ? (
          <input
            value={lastName}
            onChange={e => {
              setLastName(e.target.value)
            }}
          />
        ) : (
          <b>{lastName}</b>
        )}
      </label>
      <button type="submit">
        {isEditing ? 'Save' : 'Edit'} Profile
      </button>
      <p><i>Hello, {firstName} {lastName}!</i></p>
    </form>
  );
}
```

```css
label { display: block; margin-bottom: 20px; }
```

</Sandpack>

Compare this solution to the original imperative code. How are they different?

</Solution>

#### Refactor the imperative solution without React {/*refactor-the-imperative-solution-without-react*/}

Here is the original sandbox from the previous challenge, written imperatively without React:

<Sandpack>

```js index.js active
function handleFormSubmit(e) {
  e.preventDefault();
  if (editButton.textContent === 'Edit Profile') {
    editButton.textContent = 'Save Profile';
    hide(firstNameText);
    hide(lastNameText);
    show(firstNameInput);
    show(lastNameInput);
  } else {
    editButton.textContent = 'Edit Profile';
    hide(firstNameInput);
    hide(lastNameInput);
    show(firstNameText);
    show(lastNameText);
  }
}

function handleFirstNameChange() {
  firstNameText.textContent = firstNameInput.value;
  helloText.textContent = (
    'Hello ' +
    firstNameInput.value + ' ' +
    lastNameInput.value + '!'
  );
}

function handleLastNameChange() {
  lastNameText.textContent = lastNameInput.value;
  helloText.textContent = (
    'Hello ' +
    firstNameInput.value + ' ' +
    lastNameInput.value + '!'
  );
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

let form = document.getElementById('form');
let editButton = document.getElementById('editButton');
let firstNameInput = document.getElementById('firstNameInput');
let firstNameText = document.getElementById('firstNameText');
let lastNameInput = document.getElementById('lastNameInput');
let lastNameText = document.getElementById('lastNameText');
let helloText = document.getElementById('helloText');
form.onsubmit = handleFormSubmit;
firstNameInput.oninput = handleFirstNameChange;
lastNameInput.oninput = handleLastNameChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <label>
    First name:
    <b id="firstNameText">Jane</b>
    <input
      id="firstNameInput"
      value="Jane"
      style="display: none">
  </label>
  <label>
    Last name:
    <b id="lastNameText">Jacobs</b>
    <input
      id="lastNameInput"
      value="Jacobs"
      style="display: none">
  </label>
  <button type="submit" id="editButton">Edit Profile</button>
  <p><i id="helloText">Hello, Jane Jacobs!</i></p>
</form>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
label { display: block; margin-bottom: 20px; }
</style>
```

</Sandpack>

Imagine React didn't exist. Can you refactor this code in a way that makes the logic less fragile and more similar to the React version? What would it look like if the state was explicit, like in React?

If you're struggling to think where to start, the stub below already has most of the structure in place. If you start here, fill in the missing logic in the `updateDOM` function. (Refer to the original code where needed.)

<Sandpack>

```js index.js active
let firstName = 'Jane';
let lastName = 'Jacobs';
let isEditing = false;

function handleFormSubmit(e) {
  e.preventDefault();
  setIsEditing(!isEditing);
}

function handleFirstNameChange(e) {
  setFirstName(e.target.value);
}

function handleLastNameChange(e) {
  setLastName(e.target.value);
}

function setFirstName(value) {
  firstName = value;
  updateDOM();
}

function setLastName(value) {
  lastName = value;
  updateDOM();
}

function setIsEditing(value) {
  isEditing = value;
  updateDOM();
}

function updateDOM() {
  if (isEditing) {
    editButton.textContent = 'Save Profile';
    // TODO: show inputs, hide content
  } else {
    editButton.textContent = 'Edit Profile';
    // TODO: hide inputs, show content
  }
  // TODO: update text labels
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

let form = document.getElementById('form');
let editButton = document.getElementById('editButton');
let firstNameInput = document.getElementById('firstNameInput');
let firstNameText = document.getElementById('firstNameText');
let lastNameInput = document.getElementById('lastNameInput');
let lastNameText = document.getElementById('lastNameText');
let helloText = document.getElementById('helloText');
form.onsubmit = handleFormSubmit;
firstNameInput.oninput = handleFirstNameChange;
lastNameInput.oninput = handleLastNameChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <label>
    First name:
    <b id="firstNameText">Jane</b>
    <input
      id="firstNameInput"
      value="Jane"
      style="display: none">
  </label>
  <label>
    Last name:
    <b id="lastNameText">Jacobs</b>
    <input
      id="lastNameInput"
      value="Jacobs"
      style="display: none">
  </label>
  <button type="submit" id="editButton">Edit Profile</button>
  <p><i id="helloText">Hello, Jane Jacobs!</i></p>
</form>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
label { display: block; margin-bottom: 20px; }
</style>
```

</Sandpack>

<Solution>

The missing logic included toggling the display of inputs and content, and updating the labels:

<Sandpack>

```js index.js active
let firstName = 'Jane';
let lastName = 'Jacobs';
let isEditing = false;

function handleFormSubmit(e) {
  e.preventDefault();
  setIsEditing(!isEditing);
}

function handleFirstNameChange(e) {
  setFirstName(e.target.value);
}

function handleLastNameChange(e) {
  setLastName(e.target.value);
}

function setFirstName(value) {
  firstName = value;
  updateDOM();
}

function setLastName(value) {
  lastName = value;
  updateDOM();
}

function setIsEditing(value) {
  isEditing = value;
  updateDOM();
}

function updateDOM() {
  if (isEditing) {
    editButton.textContent = 'Save Profile';
    hide(firstNameText);
    hide(lastNameText);
    show(firstNameInput);
    show(lastNameInput);
  } else {
    editButton.textContent = 'Edit Profile';
    hide(firstNameInput);
    hide(lastNameInput);
    show(firstNameText);
    show(lastNameText);
  }
  firstNameText.textContent = firstName;
  lastNameText.textContent = lastName;
  helloText.textContent = (
    'Hello ' +
    firstName + ' ' +
    lastName + '!'
  );
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

let form = document.getElementById('form');
let editButton = document.getElementById('editButton');
let firstNameInput = document.getElementById('firstNameInput');
let firstNameText = document.getElementById('firstNameText');
let lastNameInput = document.getElementById('lastNameInput');
let lastNameText = document.getElementById('lastNameText');
let helloText = document.getElementById('helloText');
form.onsubmit = handleFormSubmit;
firstNameInput.oninput = handleFirstNameChange;
lastNameInput.oninput = handleLastNameChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <label>
    First name:
    <b id="firstNameText">Jane</b>
    <input
      id="firstNameInput"
      value="Jane"
      style="display: none">
  </label>
  <label>
    Last name:
    <b id="lastNameText">Jacobs</b>
    <input
      id="lastNameInput"
      value="Jacobs"
      style="display: none">
  </label>
  <button type="submit" id="editButton">Edit Profile</button>
  <p><i id="helloText">Hello, Jane Jacobs!</i></p>
</form>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
label { display: block; margin-bottom: 20px; }
</style>
```

</Sandpack>

The `updateDOM` function you wrote shows what React does under the hood when you set the state. (However, React also avoids touching the DOM for properties that have not changed since the last time they were set.)

</Solution>

</Challenges>
