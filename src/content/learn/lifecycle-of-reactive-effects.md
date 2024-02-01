---
title: 'Lifecycle of Reactive Effects'
---

<Intro>

Effects have a different lifecycle from components. Components may mount, update, or unmount. An Effect can only do two things: to start synchronizing something, and later to stop synchronizing it. This cycle can happen multiple times if your Effect depends on props and state that change over time. React provides a linter rule to check that you've specified your Effect's dependencies correctly. This keeps your Effect synchronized to the latest props and state.
////////////////
تأثیرات دارای چرخه‌ی متفاوتی نسبت به کامپوننت‌ها هستند. کامپوننت‌ها ممکن است نصب شوند، به‌روزرسانی شوند یا حذف شوند. یک Effect تنها می‌تواند دو کار انجام دهد: شروع همگام‌سازی چیزی و سپس متوقف کردن همگام‌سازی آن. این چرخه ممکن است چندین بار اتفاق بیفتد اگر Effect شما به props و state بستگی دارد که با گذر زمان تغییر می‌کند. React قانون لینتری را برای بررسی اینکه آیا وابستگی‌های Effect شما به درستی مشخص شده‌اند، فراهم می‌کند. این باعث می‌شود که Effect شما با props و state جدید همگام شود.

</Intro>

<YouWillLearn>

- How an Effect's lifecycle is different from a component's lifecycle
- How to think about each individual Effect in isolation
- When your Effect needs to re-synchronize, and why
- How your Effect's dependencies are determined
- What it means for a value to be reactive
- What an empty dependency array means
- How React verifies your dependencies are correct with a linter
- What to do when you disagree with the linter

///////////////////////
- چگونگی تفاوت چرخه‌ی مدت زندگی یک Effect با چرخه‌ی مدت زندگی یک کامپوننت
- چگونگی فکر کردن در مورد هر Effect به صورت جداگانه
- زمانی که Effect شما نیاز به همگام‌سازی مجدد دارد و چرا
- چگونگی تعیین وابستگی‌های Effect شما
- معنای یک مقدار راکتیو
- معنای یک آرایه‌ی وابستگی خالی
- چگونگی تأیید وابستگی‌های شما با یک لینتر در React
- چه کاری باید انجام دهید وقتی با لینتر مخالف هستید

</YouWillLearn>

## The lifecycle of an Effect {/*the-lifecycle-of-an-effect*/}

Every React component goes through the same lifecycle:

- A component _mounts_ when it's added to the screen.
- A component _updates_ when it receives new props or state, usually in response to an interaction.
- A component _unmounts_ when it's removed from the screen.
/////////////////////
هر کامپوننت React از یک چرخه عمر مشابه عبور می کند:

- کامپوننت _mounts_ وقتی به صفحه اضافه می شود.
- کامپوننت _updates_ وقتی وضعیت جدیدی دریافت می کند، معمولاً به عنوان پاسخ به یک تعامل.
- کامپوننت _unmounts_ وقتی از صفحه حذف می شود.

**It's a good way to think about components, but _not_ about Effects.** Instead, try to think about each Effect independently from your component's lifecycle. An Effect describes how to [synchronize an external system](/learn/synchronizing-with-effects) to the current props and state. As your code changes, synchronization will need to happen more or less often.
//////////////////
این یک قسمت خوب برای فهمیدن کامپوننت هاست، اما برای افکت ها به همین شکل فکر نکنید. به جای آن، سعی کنید به صورت مستقل به هر افکت فکر کنید. یک افکت توصیف می کند که چگونه یک سیستم خارجی را با وضعیت فعلی props و state همگام سازی کنید. با تغییر کد شما، همگام سازی باید بیشتر یا کمتر اتفاق بیفتد.

To illustrate this point, consider this Effect connecting your component to a chat server:
برای توضیح این نکته، به این افکت که کامپوننت شما را به یک سرور چت متصل می کند، توجه کنید:

```js
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```

Your Effect's body specifies how to **start synchronizing:**
بدنه Effect شما نحوه **شروع همگام سازی را مشخص می کند:**

```js {2-3}
    // ...
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
    // ...
```

The cleanup function returned by your Effect specifies how to **stop synchronizing:**
تابع پاکسازی که توسط افکت شما برگردانده شده است نحوه **توقف همگام سازی را مشخص می کند:**

```js {5}
    // ...
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
    // ...
```

Intuitively, you might think that React would **start synchronizing** when your component mounts and **stop synchronizing** when your component unmounts. However, this is not the end of the story! Sometimes, it may also be necessary to **start and stop synchronizing multiple times** while the component remains mounted.
//////////
به طور ذاتی، ممکن است فکر کنید که React هنگامی که کامپوننت شما mount می شود، **شروع به همگام سازی** می کند و هنگامی که کامپوننت شما unmount می شود، **متوقف می شود**. با این حال، این پایان داستان نیست! گاهی اوقات، لازم است که همگام سازی را **چندین بار شروع و متوقف کنید** در حالی که کامپوننت هنوز mount شده است.

Let's look at _why_ this is necessary, _when_ it happens, and _how_ you can control this behavior.
بیایید به _چرا_ این امر ضروری است، _وقتی_ اتفاق می افتد و _چگونه_ می توانید این رفتار را کنترل کنید، نگاه کنیم.

<Note>

Some Effects don't return a cleanup function at all. [More often than not,](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development) you'll want to return one--but if you don't, React will behave as if you returned an empty cleanup function.
////////////////
برخی از افکت ها به هیچ عنوان یک تابع پاکسازی را برنمی گردانند. [بیشتر اوقات،](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development) شما می خواهید یک تابع پاکسازی را برگردانید - اما اگر این کار را نکنید، React به عنوان یک تابع پاکسازی خالی عمل خواهد کرد.

</Note>

### Why synchronization may need to happen more than once {/*why-synchronization-may-need-to-happen-more-than-once*/}

Imagine this `ChatRoom` component receives a `roomId` prop that the user picks in a dropdown. Let's say that initially the user picks the `"general"` room as the `roomId`. Your app displays the `"general"` chat room:
////////////////////////////
تصور کنید که کامپوننت `ChatRoom` یک `roomId` prop دریافت می کند که کاربر آن را در یک کشویی انتخاب می کند. بگذارید بگوییم که در ابتدا کاربر اتاق `"general"` را به عنوان `roomId` انتخاب کرده است. برنامه شما اتاق چت `"general"` را نمایش می دهد.

```js {3}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId /* "general" */ }) {
  // ...
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

After the UI is displayed, React will run your Effect to **start synchronizing.** It connects to the `"general"` room:
///////////////////////
ترجمه کن: پس از نمایش رابط کاربری، React افکت شما را برای **شروع همگام سازی** اجرا می کند. به اتاق `"general"` متصل می شود:

```js {3,4}
function ChatRoom({ roomId /* "general" */ }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // Connects to the "general" room
    connection.connect();
    return () => {
      connection.disconnect(); // Disconnects from the "general" room
    };
  }, [roomId]);
  // ...
```

So far, so good.
تا اینجای کار خیلی خوبه.

Later, the user picks a different room in the dropdown (for example, `"travel"`). First, React will update the UI:
//////////////////////
ترجمه کن: بعداً، کاربر یک اتاق متفاوت را در کشویی انتخاب می کند (به عنوان مثال، `"travel"`). ابتدا، React واسط کاربری را به روز می کند:

```js {1}
function ChatRoom({ roomId /* "travel" */ }) {
  // ...
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

Think about what should happen next. The user sees that `"travel"` is the selected chat room in the UI. However, the Effect that ran the last time is still connected to the `"general"` room. **The `roomId` prop has changed, so what your Effect did back then (connecting to the `"general"` room) no longer matches the UI.**
///////////////
ترجمه کن: در ادامه، به این فکر کنید که چه اتفاقی باید بیفتد. کاربر می بیند که اتاق چت `"travel"` در رابط کاربری انتخاب شده است. با این حال، افکتی که آخرین بار اجرا شده است هنوز به اتاق `"general"` متصل است. **`roomId` prop تغییر کرده است، بنابراین این کاری که افکت شما در آن زمان انجام داد (متصل شدن به اتاق `"general"`) دیگر با رابط کاربری مطابقت ندارد.**

At this point, you want React to do two things:
در این مرحله، شما می خواهید React دو کار را انجام دهد:

1. Stop synchronizing with the old `roomId` (disconnect from the `"general"` room)
2. Start synchronizing with the new `roomId` (connect to the `"travel"` room)
///////////////
1. همگام‌سازی با «roomId» قدیمی را متوقف کنید (ارتباط با اتاق «عمومی» را قطع کنید)
2. همگام سازی را با «roomId» جدید شروع کنید (به اتاق «سفر» متصل شوید)

**Luckily, you've already taught React how to do both of these things!** Your Effect's body specifies how to start synchronizing, and your cleanup function specifies how to stop synchronizing. All that React needs to do now is to call them in the correct order and with the correct props and state. Let's see how exactly that happens.
////////////////////
با خوشبختی بگوییم که شما React را قبلاً چگونگی انجام هر دو این کارها آموخته اید! بدنه افکت شما نحوه شروع همگام سازی را مشخص می کند و تابع پاکسازی شما نحوه متوقف کردن همگام سازی را مشخص می کند. حالا تنها کاری که React باید انجام دهد، فراخوانی آنها به ترتیب صحیح و با props و state درست است. بیایید ببینیم دقیقاً چگونه این اتفاق می افتد.

### How React re-synchronizes your Effect {/*how-react-re-synchronizes-your-effect*/}

Recall that your `ChatRoom` component has received a new value for its `roomId` prop. It used to be `"general"`, and now it is `"travel"`. React needs to re-synchronize your Effect to re-connect you to a different room.
//////////////
به یاد داشته باشید که کامپوننت `ChatRoom` شما یک مقدار جدید برای `roomId` prop دریافت کرده است. قبلاً `"general"` بود و حالا `"travel"` است. React برای مجدداً اتصال شما به یک اتاق متفاوت، نیاز به همگام سازی افکت شما دارد.

To **stop synchronizing,** React will call the cleanup function that your Effect returned after connecting to the `"general"` room. Since `roomId` was `"general"`, the cleanup function disconnects from the `"general"` room:
//////////////
برای **توقف همگام سازی**، React تابع پاکسازی را که افکت شما پس از متصل شدن به اتاق `"general"` برگردانده بود، فراخوانی می کند. از آنجایی که `roomId` `"general"` بود، تابع پاکسازی از اتاق `"general"` قطع ارتباط می کند:

```js {6}
function ChatRoom({ roomId /* "general" */ }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // Connects to the "general" room
    connection.connect();
    return () => {
      connection.disconnect(); // Disconnects from the "general" room
    };
    // ...
```

Then React will run the Effect that you've provided during this render. This time, `roomId` is `"travel"` so it will **start synchronizing** to the `"travel"` chat room (until its cleanup function is eventually called too):
//////////////////
ترجمه کن: سپس React افکتی که در این رندر ارائه داده اید را اجرا خواهد کرد. در این بار، `roomId` `"travel"` است، بنابراین به اتاق چت `"travel"` **شروع به همگام سازی** خواهد کرد (تا زمانی که تابع پاکسازی آن نیز فراخوانی شود):

```js {3,4}
function ChatRoom({ roomId /* "travel" */ }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // Connects to the "travel" room
    connection.connect();
    // ...
```

Thanks to this, you're now connected to the same room that the user chose in the UI. Disaster averted!
//////////////
به لطف این، شما اکنون به همان اتاقی متصل هستید که کاربر در رابط کاربری انتخاب کرده است. فاجعه جلوگیری شد!

Every time after your component re-renders with a different `roomId`, your Effect will re-synchronize. For example, let's say the user changes `roomId` from `"travel"` to `"music"`. React will again **stop synchronizing** your Effect by calling its cleanup function (disconnecting you from the `"travel"` room). Then it will **start synchronizing** again by running its body with the new `roomId` prop (connecting you to the `"music"` room).
//////////////
هر بار که کامپوننت شما با `roomId` متفاوتی دوباره رندر می شود، افکت شما مجدداً همگام سازی خواهد شد. به عنوان مثال، فرض کنید کاربر `roomId` را از `"travel"` به `"music"` تغییر داده است. React با فراخوانی تابع پاکسازی افکت شما (قطع ارتباط شما از اتاق `"travel"`) **توقف همگام سازی** را دوباره انجام خواهد داد. سپس با اجرای بدنه افکت شما با `roomId` جدید (اتصال شما به اتاق `"music"`)، دوباره **شروع به همگام سازی** خواهد کرد.

Finally, when the user goes to a different screen, `ChatRoom` unmounts. Now there is no need to stay connected at all. React will **stop synchronizing** your Effect one last time and disconnect you from the `"music"` chat room.
//////////////////
در نهایت، وقتی کاربر به صفحه دیگری می رود، `ChatRoom` unmount می شود. حالا دیگر نیازی به اتصال ندارید. React برای آخرین بار **توقف همگام سازی** افکت شما را فراخوانی کرده و شما را از اتاق چت `"music"` قطع ارتباط می کند.

### Thinking from the Effect's perspective {/*thinking-from-the-effects-perspective*/}

Let's recap everything that's happened from the `ChatRoom` component's perspective:
بیایید همه چیزهایی را که از دیدگاه component's «ChatRoom» اتفاق افتاده است، مرور کنیم:

1. `ChatRoom` mounted with `roomId` set to `"general"`
1. `ChatRoom` updated with `roomId` set to `"travel"`
1. `ChatRoom` updated with `roomId` set to `"music"`
1. `ChatRoom` unmounted

During each of these points in the component's lifecycle, your Effect did different things:
در طول هر یک از این نقاط در چرخه عمر کامپوننت، Effect شما کارهای مختلفی انجام داد:

1. Your Effect connected to the `"general"` room
1. Your Effect disconnected from the `"general"` room and connected to the `"travel"` room
1. Your Effect disconnected from the `"travel"` room and connected to the `"music"` room
1. Your Effect disconnected from the `"music"` room

Now let's think about what happened from the perspective of the Effect itself:
حالا بیایید از منظر خود افکت به اتفاقی که افتاده فکر کنیم:

```js
  useEffect(() => {
    // Your Effect connected to the room specified with roomId...
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      // ...until it disconnected
      connection.disconnect();
    };
  }, [roomId]);
```

This code's structure might inspire you to see what happened as a sequence of non-overlapping time periods:
///////////////////
ساختار کد این امکان را به شما می دهد که اتفاقات را به عنوان یک دنباله ای از دوره های زمانی غیر همپوشانی دیده شود.

1. Your Effect connected to the `"general"` room (until it disconnected)
1. Your Effect connected to the `"travel"` room (until it disconnected)
1. Your Effect connected to the `"music"` room (until it disconnected)

Previously, you were thinking from the component's perspective. When you looked from the component's perspective, it was tempting to think of Effects as "callbacks" or "lifecycle events" that fire at a specific time like "after a render" or "before unmount". This way of thinking gets complicated very fast, so it's best to avoid.
///////////////
قبلاً از دیدگاه کامپوننت فکر می کردید. وقتی از دیدگاه کامپوننت نگاه می کردید، جذاب بود که به افکت ها به عنوان "callback" یا "رویدادهای چرخه عمر" فکر کنید که در زمان خاصی مانند "پس از رندر" یا "قبل از unmount" فعال می شوند. این روش فکر کردن بسیار سریع پیچیده می شود، بنابراین بهتر است از آن خودداری کنید.

**Instead, always focus on a single start/stop cycle at a time. It shouldn't matter whether a component is mounting, updating, or unmounting. All you need to do is to describe how to start synchronization and how to stop it. If you do it well, your Effect will be resilient to being started and stopped as many times as it's needed.**
//////////////
به جای آن، همیشه بر روی یک چرخه شروع / توقف تمرکز کنید. مهم نیست که یک کامپوننت mount، update یا unmount شود. تنها کافیست توصیف کنید که چگونه همگام سازی را شروع و متوقف کنید. اگر این کار را خوب انجام دهید، افکت شما مقاوم در برابر شروع و توقف چندین بار خواهد بود.

This might remind you how you don't think whether a component is mounting or updating when you write the rendering logic that creates JSX. You describe what should be on the screen, and React [figures out the rest.](/learn/reacting-to-input-with-state)
///////////////
این ممکن است به شما یادآوری کند که وقتی منطق رندری که JSX را ایجاد می کند را می نویسید، به فکر این نیستید که کامپوننت mount یا update شود. شما توصیف می کنید که چه چیزی باید روی صفحه نمایش داده شود و React [باقی را حل می کند.](/learn/reacting-to-input-with-state)

### How React verifies that your Effect can re-synchronize {/*how-react-verifies-that-your-effect-can-re-synchronize*/}

Here is a live example that you can play with. Press "Open chat" to mount the `ChatRoom` component:
///////////////////
در اینجا یک مثال زنده است که می توانید با آن بازی کنید. برای نصب مولفه «ChatRoom»، «Open chat» را فشار دهید:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [show, setShow] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Close chat' : 'Open chat'}
      </button>
      {show && <hr />}
      {show && <ChatRoom roomId={roomId} />}
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Notice that when the component mounts for the first time, you see three logs:
توجه داشته باشید که وقتی کامپوننت برای اولین بار سوار می شود، سه لاگ را مشاهده می کنید:

1. `✅ Connecting to "general" room at https://localhost:1234...` *(development-only)*
1. `❌ Disconnected from "general" room at https://localhost:1234.` *(development-only)*
1. `✅ Connecting to "general" room at https://localhost:1234...`

The first two logs are development-only. In development, React always remounts each component once.
//////////////
اولین دو لاگ فقط برای توسعه است. در حالت توسعه، React همیشه هر کامپوننت را یک بار mount می کند.

**React verifies that your Effect can re-synchronize by forcing it to do that immediately in development.** This might remind you of opening a door and closing it an extra time to check if the door lock works. React starts and stops your Effect one extra time in development to check [you've implemented its cleanup well.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)
////////////////
**React با اجرای افکت شما در حالت توسعه، از اینکه افکت شما می تواند مجدداً همگام سازی شود، اطمینان حاصل می کند.** این ممکن است به شما یادآوری کند که برای بررسی اینکه قفل درب کار می کند یا نه، درب را یک بار دیگر باز و بسته می کنید. React در حالت توسعه، افکت شما را یک بار اضافی شروع و متوقف می کند تا بررسی کند [آیا شما پاکسازی آن را به خوبی پیاده کرده اید یا نه.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

The main reason your Effect will re-synchronize in practice is if some data it uses has changed. In the sandbox above, change the selected chat room. Notice how, when the `roomId` changes, your Effect re-synchronizes.
//////////////////////
دلیل اصلی مجدداً همگام سازی افکت شما در عمل این است که اگر داده هایی که از آن استفاده می کند تغییر کرده باشد. در محیط بالا، اتاق چت انتخاب شده را تغییر دهید. توجه کنید که هنگامی که `roomId` تغییر می کند، افکت شما مجدداً همگام سازی می شود.

However, there are also more unusual cases in which re-synchronization is necessary. For example, try editing the `serverUrl` in the sandbox above while the chat is open. Notice how the Effect re-synchronizes in response to your edits to the code. In the future, React may add more features that rely on re-synchronization.
/////////////
به طور نادر، ممکن است نیاز به مجدداً همگام سازی باشد. به عنوان مثال، در حالی که چت باز است، `serverUrl` را در محیط بالا ویرایش کنید. توجه کنید که افکت به ویرایش کد شما پاسخ می دهد و مجدداً همگام سازی می کند. در آینده، React ممکن است ویژگی های بیشتری را اضافه کند که به مجدداً همگام سازی نیاز دارند..

### How React knows that it needs to re-synchronize the Effect {/*how-react-knows-that-it-needs-to-re-synchronize-the-effect*/}

You might be wondering how React knew that your Effect needed to re-synchronize after `roomId` changes. It's because *you told React* that its code depends on `roomId` by including it in the [list of dependencies:](/learn/synchronizing-with-effects#step-2-specify-the-effect-dependencies)
/////////////
شاید به این فکر کنید که React چگونه می دانست که افکت شما باید پس از تغییرات `roomId` مجدداً همگام سازی شود. به دلیل اینکه *شما به React گفتید* که کد آن به `roomId` وابسته است، با اضافه کردن آن به [لیست وابستگی ها:](/learn/synchronizing-with-effects#step-2-specify-the-effect-dependencies)

```js {1,3,8}
function ChatRoom({ roomId }) { // The roomId prop may change over time
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // This Effect reads roomId 
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]); // So you tell React that this Effect "depends on" roomId
  // ...
```

Here's how this works:

1. You knew `roomId` is a prop, which means it can change over time.
2. You knew that your Effect reads `roomId` (so its logic depends on a value that may change later).
3. This is why you specified it as your Effect's dependency (so that it re-synchronizes when `roomId` changes).
//////////////////
به این شکل کار می کند:

1. شما می دانستید `roomId` یک prop است، به این معنی که ممکن است در طول زمان تغییر کند.
2. شما می دانستید که افکت شما `roomId` را می خواند (بنابراین منطق آن به یک مقداری که ممکن است بعداً تغییر کند وابسته است).
3. به همین دلیل آن را به عنوان وابستگی افکت خود مشخص کردید (تا زمانی که `roomId` تغییر کند، مجدداً همگام سازی شود).

Every time after your component re-renders, React will look at the array of dependencies that you have passed. If any of the values in the array is different from the value at the same spot that you passed during the previous render, React will re-synchronize your Effect.
////////////
هر بار که کامپوننت شما بازنشانی می شود، React به آرایه وابستگی هایی که شما ارسال کرده اید نگاه می کند. اگر هر یک از مقادیر در آرایه با مقدار در همان نقطه ای که در رندر قبلی ارسال کرده اید متفاوت باشد، React افکت شما را مجدداً همگام سازی خواهد کرد.

For example, if you passed `["general"]` during the initial render, and later you passed `["travel"]` during the next render, React will compare `"general"` and `"travel"`. These are different values (compared with [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)), so React will re-synchronize your Effect. On the other hand, if your component re-renders but `roomId` has not changed, your Effect will remain connected to the same room.
//////////////
به عنوان مثال، اگر در رندر اولیه `["general"]` را ارسال کرده بودید و در رندر بعدی `["travel"]` را ارسال کردید، React `"general"` و `"travel"` را مقایسه خواهد کرد. این مقادیر متفاوت هستند (نسبت به [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) مقایسه شده اند)، بنابراین React افکت شما را مجدداً همگام سازی خواهد کرد. از طرف دیگر، اگر کامپوننت شما دوباره رندر می شود اما `roomId` تغییر نکرده است، افکت شما به همان اتاق متصل خواهد ماند.

### Each Effect represents a separate synchronization process {/*each-effect-represents-a-separate-synchronization-process*/}

Resist adding unrelated logic to your Effect only because this logic needs to run at the same time as an Effect you already wrote. For example, let's say you want to send an analytics event when the user visits the room. You already have an Effect that depends on `roomId`, so you might feel tempted to add the analytics call there:
///////////
ترجمه کن: تلاش کنید منطق نامربوط را به اثر خود اضافه نکنید فقط به این دلیل که این منطق باید در همان زمانی که اثری که قبلاً نوشته اید اجرا می شود، اجرا شود. به عنوان مثال، فرض کنید می خواهید هنگام بازدید کاربر از اتاق، یک رویداد تحلیلی ارسال کنید. شما قبلاً یک افکت داشتید که به `roomId` وابسته است، بنابراین ممکن است احساس تمایل کنید که تماس تحلیلی را در آنجا اضافه کنید:

```js {3}
function ChatRoom({ roomId }) {
  useEffect(() => {
    logVisit(roomId);
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```

But imagine you later add another dependency to this Effect that needs to re-establish the connection. If this Effect re-synchronizes, it will also call `logVisit(roomId)` for the same room, which you did not intend. Logging the visit **is a separate process** from connecting. Write them as two separate Effects:
///////////////
اما تصور کنید که بعداً وابستگی دیگری را به این افکت اضافه کردید که نیاز به بازسازی اتصال دارد. اگر این افکت مجدداً همگام سازی شود، همچنین `logVisit(roomId)` را برای همان اتاق فراخوانی خواهد کرد که شما نمی خواستید. ورود به سیستم **فرآیند جداگانه ای است** از اتصال. آنها را به عنوان دو افکت جداگانه بنویسید:


```js {2-4}
function ChatRoom({ roomId }) {
  useEffect(() => {
    logVisit(roomId);
  }, [roomId]);

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    // ...
  }, [roomId]);
  // ...
}
```

**Each Effect in your code should represent a separate and independent synchronization process.**
**هر افکت در کد شما باید یک فرآیند همگام سازی مجزا و مستقل را نشان دهد.**

In the above example, deleting one Effect wouldn’t break the other Effect's logic. This is a good indication that they synchronize different things, and so it made sense to split them up. On the other hand, if you split up a cohesive piece of logic into separate Effects, the code may look "cleaner" but will be [more difficult to maintain.](/learn/you-might-not-need-an-effect#chains-of-computations) This is why you should think whether the processes are same or separate, not whether the code looks cleaner.
///////////////
در مثال بالا، حذف یک Effect منطق دیگر Effect را نمی‌شکند. این نشان‌دهنده هماهنگی موارد مختلف است و به همین دلیل منطق را به دو بخش تقسیم کردیم. از طرفی، اگر یک قطعه منطقی را به چندین Effect تقسیم کنید، کد شاید "تمیزتر" به نظر برسد، اما [دشوارتر در نگهداری خواهد بود.](/learn/you-might-not-need-an-effect#chains-of-computations) به همین دلیل، باید بررسی کنید که فرآیندها یکسان هستند یا متفاوت، نه اینکه کد چگونه به نظر می‌رسد.

## Effects "react" to reactive values {/*effects-react-to-reactive-values*/}

Your Effect reads two variables (`serverUrl` and `roomId`), but you only specified `roomId` as a dependency:
//////////////
ترجمه: افکت شما دو متغیر (`serverUrl` و `roomId`) را می‌خواند، اما تنها `roomId` را به عنوان وابستگی مشخص کرده‌اید.

```js {5,10}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```

Why doesn't `serverUrl` need to be a dependency?

This is because the `serverUrl` never changes due to a re-render. It's always the same no matter how many times the component re-renders and why. Since `serverUrl` never changes, it wouldn't make sense to specify it as a dependency. After all, dependencies only do something when they change over time!
////////////
ترجمه: این به این دلیل است که `serverUrl` هرگز به دلیل یک re-render تغییر نمی‌کند. همیشه همان مقدار را دارد، بدون توجه به تعداد بارهایی که کامپوننت re-render شده است. از آنجا که `serverUrl` هرگز تغییر نمی‌کند، مشخص کردن آن به عنوان وابستگی معنی ندارد. در نهایت، وابستگی‌ها فقط زمانی کاری انجام می‌دهند که در طول زمان تغییر می‌کنند!

On the other hand, `roomId` may be different on a re-render. **Props, state, and other values declared inside the component are _reactive_ because they're calculated during rendering and participate in the React data flow.**
//////////////
ترجمه: از طرفی، `roomId` ممکن است در یک re-render متفاوت باشد. **Props، state و سایر مقادیری که درون کامپوننت اعلام شده‌اند، _ری‌اکتیو_ هستند زیرا در زمان رندر شدن محاسبه می‌شوند و در جریان داده ری‌اکت شرکت می‌کنند.**

If `serverUrl` was a state variable, it would be reactive. Reactive values must be included in dependencies:
//////////////
اگر «serverUrl» یک متغیر state بود، واکنش‌پذیر بود. مقادیر واکنشی باید در وابستگی ها گنجانده شوند:

```js {2,5,10}
function ChatRoom({ roomId }) { // Props change over time
  const [serverUrl, setServerUrl] = useState('https://localhost:1234'); // State may change over time

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // Your Effect reads props and state
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId, serverUrl]); // So you tell React that this Effect "depends on" on props and state
  // ...
}
```

By including `serverUrl` as a dependency, you ensure that the Effect re-synchronizes after it changes.

Try changing the selected chat room or edit the server URL in this sandbox:
/////////////////
ترجمه: با اضافه کردن `serverUrl` به عنوان وابستگی، مطمئن می‌شوید که افکت پس از تغییرات، دوباره هماهنگ می‌شود.

برای تغییر اتاق چت انتخاب شده یا ویرایش URL سرور در این sandbox امتحان کنید:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return (
    <>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Whenever you change a reactive value like `roomId` or `serverUrl`, the Effect re-connects to the chat server.
/////////////////
ترجمه: هرگاه مقداری ری‌اکتیو مانند `roomId` یا `serverUrl` را تغییر دهید، افکت به سرور چت متصل می‌شود.

### What an Effect with empty dependencies means {/*what-an-effect-with-empty-dependencies-means*/}

What happens if you move both `serverUrl` and `roomId` outside the component?
اگر «serverUrl» و «roomId» را به خارج از component منتقل کنید، چه اتفاقی می‌افتد؟

```js {1,2}
const serverUrl = 'https://localhost:1234';
const roomId = 'general';

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []); // ✅ All dependencies declared
  // ...
}
```

Now your Effect's code does not use *any* reactive values, so its dependencies can be empty (`[]`).
//////////
اکنون کد افکت شما از *هیچ* مقدار واکنشی استفاده نمی کند، بنابراین وابستگی های آن می تواند خالی باشد (`[]`).

Thinking from the component's perspective, the empty `[]` dependency array means this Effect connects to the chat room only when the component mounts, and disconnects only when the component unmounts. (Keep in mind that React would still [re-synchronize it an extra time](#how-react-verifies-that-your-effect-can-re-synchronize) in development to stress-test your logic.)
//////////////////////////
با توجه به منظری که از جانب کامپوننت داریم، آرایه وابستگی `[]` خالی به این معنی است که این اثر فقط هنگام نصب کامپوننت به اتاق چت متصل می شود و فقط هنگام عدم نصب کامپوننت قطع ارتباط می کند. (در نظر داشته باشید که React در حالت توسعه هنوز [یک بار دیگر آن را همگام می کند] تا منطق شما را تست کند.)


<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'general';

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Close chat' : 'Open chat'}
      </button>
      {show && <hr />}
      {show && <ChatRoom />}
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

However, if you [think from the Effect's perspective,](#thinking-from-the-effects-perspective) you don't need to think about mounting and unmounting at all. What's important is you've specified what your Effect does to start and stop synchronizing. Today, it has no reactive dependencies. But if you ever want the user to change `roomId` or `serverUrl` over time (and they would become reactive), your Effect's code won't change. You will only need to add them to the dependencies.
///////////////
با این حال، اگر از دیدگاه اثر فکر کنید، نیازی به فکر کردن در مورد نصب و حذف نصب نیست. مهم این است که شما مشخص کرده‌اید که اثر شما چه کاری را برای شروع و متوقف کردن همگام سازی انجام می‌دهد. امروزه، هیچ وابستگی واکنشی ندارد. اما اگر همیشه می‌خواهید که کاربر `roomId` یا `serverUrl` را در طول زمان تغییر دهد (و آنها واکنشی می‌شوند)، کد اثر شما تغییر نخواهد کرد. تنها باید آنها را به وابستگی‌ها اضافه کنید.

### All variables declared in the component body are reactive {/*all-variables-declared-in-the-component-body-are-reactive*/}

Props and state aren't the only reactive values. Values that you calculate from them are also reactive. If the props or state change, your component will re-render, and the values calculated from them will also change. This is why all variables from the component body used by the Effect should be in the Effect dependency list.
///////////////
`Props` و `state` تنها مقادیر واکنشی نیستند. مقادیری که از آنها محاسبه می‌شوند نیز واکنشی هستند. اگر `props` یا `state` تغییر کنند، کامپوننت شما دوباره رندر می‌شود و مقادیری که از آنها محاسبه شده‌اند نیز تغییر می‌کنند. به همین دلیل، تمام متغیرهایی که در بدنه کامپوننت استفاده شده‌اند و توسط `Effect` استفاده می‌شوند، باید در لیست وابستگی‌های `Effect` قرار گیرند.

Let's say that the user can pick a chat server in the dropdown, but they can also configure a default server in settings. Suppose you've already put the settings state in a [context](/learn/scaling-up-with-reducer-and-context) so you read the `settings` from that context. Now you calculate the `serverUrl` based on the selected server from props and the default server:
/////////////////
فرض کنید که کاربر می‌تواند یک سرور چت را از لیست کشویی انتخاب کند، اما همچنین می‌تواند یک سرور پیش‌فرض را در تنظیمات پیکربندی کند. فرض کنید که شما قبلاً وضعیت تنظیمات را در یک [context](/learn/scaling-up-with-reducer-and-context) قرار داده‌اید، بنابراین `settings` را از آن context می‌خوانید. حالا `serverUrl` را بر اساس سرور انتخابی از `props` و سرور پیش‌فرض محاسبه می‌کنید.

```js {3,5,10}
function ChatRoom({ roomId, selectedServerUrl }) { // roomId is reactive
  const settings = useContext(SettingsContext); // settings is reactive
  const serverUrl = selectedServerUrl ?? settings.defaultServerUrl; // serverUrl is reactive
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // Your Effect reads roomId and serverUrl
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId, serverUrl]); // So it needs to re-synchronize when either of them changes!
  // ...
}
```

In this example, `serverUrl` is not a prop or a state variable. It's a regular variable that you calculate during rendering. But it's calculated during rendering, so it can change due to a re-render. This is why it's reactive.
///////////////
در این مثال، `serverUrl` متغیر `prop` یا `state` نیست. این یک متغیر معمولی است که در طول رندر شدن محاسبه می‌شود. اما زمانی که در حال رندر شدن است، ممکن است به دلیل رندر مجدد تغییر کند. به همین دلیل، واکنشی است.

**All values inside the component (including props, state, and variables in your component's body) are reactive. Any reactive value can change on a re-render, so you need to include reactive values as Effect's dependencies.**
/////////
**تمام مقادیر داخل کامپوننت (شامل props، state و متغیرهای بدنه کامپوننت) واکنشی هستند. هر مقدار واکنشی می‌تواند در رندر مجدد تغییر کند، بنابراین باید مقادیر واکنشی را به عنوان وابستگی‌های `Effect` در نظر بگیرید.**

In other words, Effects "react" to all values from the component body.
به عبارت دیگر، افکت‌ها به تمام مقادیر بدنه component واکنش نشان می‌دهند.
<DeepDive>

#### Can global or mutable values be dependencies? {/*can-global-or-mutable-values-be-dependencies*/}

Mutable values (including global variables) aren't reactive.
مقادیر قابل تغییر (از جمله متغیرهای سراسری) واکنشی نیستند.

**A mutable value like [`location.pathname`](https://developer.mozilla.org/en-US/docs/Web/API/Location/pathname) can't be a dependency.** It's mutable, so it can change at any time completely outside of the React rendering data flow. Changing it wouldn't trigger a re-render of your component. Therefore, even if you specified it in the dependencies, React *wouldn't know* to re-synchronize the Effect when it changes. This also breaks the rules of React because reading mutable data during rendering (which is when you calculate the dependencies) breaks [purity of rendering.](/learn/keeping-components-pure) Instead, you should read and subscribe to an external mutable value with [`useSyncExternalStore`.](/learn/you-might-not-need-an-effect#subscribing-to-an-external-store)
//////////////////
`مقادیر قابل تغییر از جمله متغیرهای جهانی واکنشی نیستند`. مقدار قابل تغییر مانند `location.pathname` نمی‌تواند به عنوان وابستگی در نظر گرفته شود. این مقدار قابل تغییر است، بنابراین می‌تواند در هر زمان کاملاً خارج از جریان داده رندرینگ React تغییر کند. تغییر آن باعث رندر مجدد کامپوننت شما نخواهد شد. به همین دلیل، حتی اگر آن را در وابستگی‌ها مشخص کنید، React نمی‌داند که باید `Effect` را هنگام تغییر مجدد همگام سازی کند. این همچنین با قوانین React در تضاد است زیرا خواندن داده‌های قابل تغییر در طول رندرینگ (زمانی که وابستگی‌ها را محاسبه می‌کنید) باعث شکست [خلوص رندرینگ](/learn/keeping-components-pure) می‌شود. به جای آن، باید مقدار قابل تغییر خارجی را با [`useSyncExternalStore`](/learn/you-might-not-need-an-effect#subscribing-to-an-external-store) خوانده و مشترک شوید..

**A mutable value like [`ref.current`](/reference/react/useRef#reference) or things you read from it also can't be a dependency.** The ref object returned by `useRef` itself can be a dependency, but its `current` property is intentionally mutable. It lets you [keep track of something without triggering a re-render.](/learn/referencing-values-with-refs) But since changing it doesn't trigger a re-render, it's not a reactive value, and React won't know to re-run your Effect when it changes.
/////////////////
`مقادیر قابل تغییر از جمله متغیرهای جهانی واکنشی نیستند`. مقدار قابل تغییر مانند `ref.current` یا چیزهایی که از آن خوانده می‌شوند نیز به عنوان وابستگی در نظر گرفته نمی‌شوند. شیء `ref` بازگشتی از `useRef` به تنهایی می‌تواند یک وابستگی باشد، اما خاصیت `current` آن قابل تغییر است. این به شما اجازه می‌دهد که [چیزی را بدون رندر مجدد پیگیری کنید.](/learn/referencing-values-with-refs) اما از آنجایی که تغییر آن رندر مجدد را فراخوانی نمی‌کند، مقدار واکنشی نیست و React نمی‌داند که هنگام تغییر آن `Effect` را دوباره اجرا کند.

As you'll learn below on this page, a linter will check for these issues automatically.
همانطور که در زیر در این صفحه یاد خواهید گرفت، یک لینتر به طور خودکار این مسائل را بررسی می کند.

</DeepDive>

### React verifies that you specified every reactive value as a dependency {/*react-verifies-that-you-specified-every-reactive-value-as-a-dependency*/}

If your linter is [configured for React,](/learn/editor-setup#linting) it will check that every reactive value used by your Effect's code is declared as its dependency. For example, this is a lint error because both `roomId` and `serverUrl` are reactive:
/////////////
اگر لینتر شما برای React پیکربندی شده باشد، بررسی می‌کند که هر مقدار واکنشی که توسط کد `Effect` شما استفاده می‌شود به عنوان وابستگی آن اعلام شده باشد. به عنوان مثال، این یک خطای لینتر است زیرا هر دو `roomId` و `serverUrl` واکنشی هستند:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

function ChatRoom({ roomId }) { // roomId is reactive
  const [serverUrl, setServerUrl] = useState('https://localhost:1234'); // serverUrl is reactive

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // <-- Something's wrong here!

  return (
    <>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

This may look like a React error, but really React is pointing out a bug in your code. Both `roomId` and `serverUrl` may change over time, but you're forgetting to re-synchronize your Effect when they change. You will remain connected to the initial `roomId` and `serverUrl` even after the user picks different values in the UI.

To fix the bug, follow the linter's suggestion to specify `roomId` and `serverUrl` as dependencies of your Effect:
///////////////////
این ممکن است به نظر یک خطای React بیاید، اما در واقع React یک باگ در کد شما را نشان می‌دهد. هر دو `roomId` و `serverUrl` ممکن است در طول زمان تغییر کنند، اما شما فراموش کرده‌اید که هنگام تغییر آنها، `Effect` را دوباره همگام سازی کنید. شما حتی پس از انتخاب کاربر مقادیر مختلف در رابط کاربری، به `roomId` و `serverUrl` اولیه متصل خواهید ماند.

برای رفع باگ، پیشنهاد لینتر را برای مشخص کردن `roomId` و `serverUrl` به عنوان وابستگی‌های `Effect` دنبال کنید:

```js {9}
function ChatRoom({ roomId }) { // roomId is reactive
  const [serverUrl, setServerUrl] = useState('https://localhost:1234'); // serverUrl is reactive
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [serverUrl, roomId]); // ✅ All dependencies declared
  // ...
}
```

Try this fix in the sandbox above. Verify that the linter error is gone, and the chat re-connects when needed.
/////////////////
این اصلاح را در جعبه شنی بالا امتحان کنید. بررسی کنید که خطای linter از بین رفته باشد و چت در صورت نیاز دوباره وصل شود.

<Note>

In some cases, React *knows* that a value never changes even though it's declared inside the component. For example, the [`set` function](/reference/react/useState#setstate) returned from `useState` and the ref object returned by [`useRef`](/reference/react/useRef) are *stable*--they are guaranteed to not change on a re-render. Stable values aren't reactive, so you may omit them from the list. Including them is allowed: they won't change, so it doesn't matter.
//////////////
در برخی موارد، React *می‌داند* که یک مقدار هرگز تغییر نمی‌کند، اگرچه در داخل کامپوننت اعلام شده باشد. به عنوان مثال، تابع [`set`](/reference/react/useState#setstate) که از `useState` بازگشت داده می‌شود و شیء `ref` بازگشتی از [`useRef`](/reference/react/useRef) *پایدار* هستند - تضمین می‌شود که در رندر مجدد تغییر نمی‌کنند. مقادیر پایدار واکنشی نیستند، بنابراین می‌توانید آنها را از لیست حذف کنید. شامل آنها مجاز است: آنها تغییر نمی‌کنند، بنابراین مهم نیست..

</Note>

### What to do when you don't want to re-synchronize {/*what-to-do-when-you-dont-want-to-re-synchronize*/}

In the previous example, you've fixed the lint error by listing `roomId` and `serverUrl` as dependencies.
///////////////
در مثال قبلی، خطای lint را با فهرست کردن «roomId» و «serverUrl» به عنوان وابستگی برطرف کرده‌اید.

**However, you could instead "prove" to the linter that these values aren't reactive values,** i.e. that they *can't* change as a result of a re-render. For example, if `serverUrl` and `roomId` don't depend on rendering and always have the same values, you can move them outside the component. Now they don't need to be dependencies:
/////////////////
می‌توانید به جای اینکه به لینتر ثابت کنید که این مقادیر مقادیر واکنشی نیستند، یعنی که به عنوان نتیجه‌ای از رندر مجدد تغییر نمی‌کنند. به عنوان مثال، اگر `serverUrl` و `roomId` به رندرینگ وابسته نباشند و همیشه مقادیر یکسانی داشته باشند، می‌توانید آنها را خارج از کامپوننت منتقل کنید. حالا آنها به عنوان وابستگی‌ها نیاز ندارند:

```js {1,2,11}
const serverUrl = 'https://localhost:1234'; // serverUrl is not reactive
const roomId = 'general'; // roomId is not reactive

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []); // ✅ All dependencies declared
  // ...
}
```

You can also move them *inside the Effect.* They aren't calculated during rendering, so they're not reactive:
///////////
شما همچنین می‌توانید آنها را *داخل `Effect`* منتقل کنید. آنها در طول رندرینگ محاسبه نمی‌شوند، بنابراین واکنشی نیستند.

```js {3,4,10}
function ChatRoom() {
  useEffect(() => {
    const serverUrl = 'https://localhost:1234'; // serverUrl is not reactive
    const roomId = 'general'; // roomId is not reactive
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []); // ✅ All dependencies declared
  // ...
}
```

**Effects are reactive blocks of code.** They re-synchronize when the values you read inside of them change. Unlike event handlers, which only run once per interaction, Effects run whenever synchronization is necessary.
//////////////////
`Effect` ها بلوک های کد واکنشی هستند. آنها هنگامی که مقادیری که در داخل آنها خوانده می‌شوند تغییر می‌کنند، دوباره همگام سازی می‌شوند. برخلاف `event handler` ها که فقط یک بار در هر تعامل اجرا می‌شوند، `Effect` ها هر زمان که همگام سازی لازم باشد، اجرا می‌شوند.

**You can't "choose" your dependencies.** Your dependencies must include every [reactive value](#all-variables-declared-in-the-component-body-are-reactive) you read in the Effect. The linter enforces this. Sometimes this may lead to problems like infinite loops and to your Effect re-synchronizing too often. Don't fix these problems by suppressing the linter! Here's what to try instead:
///////////////////
می‌توانید وابستگی‌های خود را "انتخاب" نکنید. وابستگی‌های شما باید شامل هر [مقدار واکنشی](٫all-variables-declared-in-the-component-body-are-reactive) که در Effect مورد استفاده قرار می‌گیرد باشد. لینتر این را اجبار می‌کند. گاهی اوقات این مسئله می‌تواند به مشکلاتی مانند حلقه‌های بی‌نهایت و همگام‌سازی بیش از حد Effect شما منجر شود. این مشکلات را با سرکوب کردن لینتر رفع نکنید! به جای آن، این را امتحان کنید:.


* **Check that your Effect represents an independent synchronization process.** If your Effect doesn't synchronize anything, [it might be unnecessary.](/learn/you-might-not-need-an-effect) If it synchronizes several independent things, [split it up.](#each-effect-represents-a-separate-synchronization-process)
////////////////////
* **بررسی کنید که Effect شما یک فرآیند همگام‌سازی مستقل را نشان می‌دهد.** اگر Effect شما هیچ چیز را همگام‌سازی نمی‌کند، [ممکن است غیرضروری باشد.](/learn/you-might-not-need-an-effect) اگر چندین چیز مستقل را همگام‌سازی می‌کند، [آن‌ها را جدا کنید.](٫each-effect-represents-a-separate-synchronization-process)

* **If you want to read the latest value of props or state without "reacting" to it and re-synchronizing the Effect,** you can split your Effect into a reactive part (which you'll keep in the Effect) and a non-reactive part (which you'll extract into something called an _Effect Event_). [Read about separating Events from Effects.](/learn/separating-events-from-effects)
//////////////
* **اگر می‌خواهید مقدار جدید props یا state را بدون "واکنش" به آن بخوانید و Effect خود را مجدداً همگام‌سازی نکنید،** می‌توانید Effect خود را به دو بخش تقسیم کنید: بخش واکنش‌گر (که در Effect خود نگه می‌دارید) و بخش غیر واکنش‌گر (که آن را در چیزی به نام _رویداد Effect_ استخراج می‌کنید). [درباره جداسازی رویدادها از Effect ها بخوانید.](/learn/separating-events-from-effects)

* **Avoid relying on objects and functions as dependencies.** If you create objects and functions during rendering and then read them from an Effect, they will be different on every render. This will cause your Effect to re-synchronize every time. [Read more about removing unnecessary dependencies from Effects.](/learn/removing-effect-dependencies)
////////////////
* **از وابستگی به شی‌ها و توابع به عنوان وابستگی‌ها خودداری کنید.** اگر شی‌ها و توابعی را در زمان رندرینگ ایجاد کرده و سپس آن‌ها را از Effect خود بخوانید، در هر رندر متفاوت خواهند بود. این باعث می‌شود Effect شما هر بار که رندر می‌شود، مجدداً همگام‌سازی شود. [بیشتر درباره حذف وابستگی‌های غیرضروری از Effects بخوانید.](/learn/removing-effect-dependencies)

<Pitfall>

The linter is your friend, but its powers are limited. The linter only knows when the dependencies are *wrong*. It doesn't know *the best* way to solve each case. If the linter suggests a dependency, but adding it causes a loop, it doesn't mean the linter should be ignored. You need to change the code inside (or outside) the Effect so that that value isn't reactive and doesn't *need* to be a dependency.

If you have an existing codebase, you might have some Effects that suppress the linter like this:
/////////////////////
دوست داشتنی است که لینتر شماست، اما قدرت آن محدود است. لینتر فقط زمانی می‌داند که وابستگی‌ها "نادرست" هستند. این نمی‌داند که *بهترین* راه حل برای هر مورد چیست. اگر لینتر یک وابستگی را پیشنهاد می‌دهد، اما اضافه کردن آن باعث ایجاد یک حلقه می‌شود، این بدان معنا نیست که لینتر باید نادیده گرفته شود. شما باید کد داخل (یا خارج) Effect خود را تغییر دهید، به طوری که آن مقدار واکنشی نباشد و نیازی به وابستگی نداشته باشد.

اگر یک کد پایه دارید، ممکن است برخی از Effects شما را به طوری که در زیر آمده است، سرکوب کنند:

```js {3-4}
useEffect(() => {
  // ...
  // 🔴 Avoid suppressing the linter like this:
  // eslint-ignore-next-line react-hooks/exhaustive-deps
}, []);
```

On the [next](/learn/separating-events-from-effects) [pages](/learn/removing-effect-dependencies), you'll learn how to fix this code without breaking the rules. It's always worth fixing!
////////////////////
ترجمه: در [صفحات بعدی](/learn/separating-events-from-effects)، شما یاد خواهید گرفت که چگونه این کد را بدون نقض قوانین تعمیر کنید. همیشه ارزش تعمیر دادن دارد!

</Pitfall>

<Recap>

- Components can mount, update, and unmount.
- Each Effect has a separate lifecycle from the surrounding component.
- Each Effect describes a separate synchronization process that can *start* and *stop*.
- When you write and read Effects, think from each individual Effect's perspective (how to start and stop synchronization) rather than from the component's perspective (how it mounts, updates, or unmounts).
- Values declared inside the component body are "reactive".
- Reactive values should re-synchronize the Effect because they can change over time.
- The linter verifies that all reactive values used inside the Effect are specified as dependencies.
- All errors flagged by the linter are legitimate. There's always a way to fix the code to not break the rules.

//////////////////
- کامپوننت‌ها می‌توانند نصب، به‌روزرسانی و حذف شوند.
- هر Effect یک چرخه جداگانه از کامپوننت اطراف دارد.
- هر Effect یک فرآیند همگام‌سازی جداگانه را توصیف می‌کند که می‌تواند *شروع* و *پایان* پیدا کند.
- هنگام نوشتن و خواندن Effects، از دیدگاه هر Effect جداگانه (چگونه شروع و پایان همگام‌سازی را) به جای دیدگاه کامپوننت (چگونه نصب، به‌روزرسانی یا حذف می‌شود) فکر کنید.
- مقادیری که در بدنه کامپوننت اعلام می‌شوند، "واکنشی" هستند.
- مقادیر واکنشی باید Effect را مجدداً همگام‌سازی کنند زیرا ممکن است در طول زمان تغییر کنند.
- لینتر تأیید می‌کند که تمام مقادیر واکنشی مورد استفاده در داخل Effect به عنوان وابستگی‌ها مشخص شده‌اند.
- تمام خطاهایی که توسط لینتر مشخص شده‌اند، معتبر هستند. همیشه راهی برای تعمیر کد و عدم نقض قوانین وجود دارد.


</Recap>

<Challenges>

#### Fix reconnecting on every keystroke {/*fix-reconnecting-on-every-keystroke*/}

In this example, the `ChatRoom` component connects to the chat room when the component mounts, disconnects when it unmounts, and reconnects when you select a different chat room. This behavior is correct, so you need to keep it working.

However, there is a problem. Whenever you type into the message box input at the bottom, `ChatRoom` *also* reconnects to the chat. (You can notice this by clearing the console and typing into the input.) Fix the issue so that this doesn't happen.

<Hint>

You might need to add a dependency array for this Effect. What dependencies should be there?

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  });

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
    </>
  );
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

<Solution>

This Effect didn't have a dependency array at all, so it re-synchronized after every re-render. First, add a dependency array. Then, make sure that every reactive value used by the Effect is specified in the array. For example, `roomId` is reactive (because it's a prop), so it should be included in the array. This ensures that when the user selects a different room, the chat reconnects. On the other hand, `serverUrl` is defined outside the component. This is why it doesn't need to be in the array.

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
    </>
  );
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

</Solution>

#### Switch synchronization on and off {/*switch-synchronization-on-and-off*/}

In this example, an Effect subscribes to the window [`pointermove`](https://developer.mozilla.org/en-US/docs/Web/API/Element/pointermove_event) event to move a pink dot on the screen. Try hovering over the preview area (or touching the screen if you're on a mobile device), and see how the pink dot follows your movement.

There is also a checkbox. Ticking the checkbox toggles the `canMove` state variable, but this state variable is not used anywhere in the code. Your task is to change the code so that when `canMove` is `false` (the checkbox is ticked off), the dot should stop moving. After you toggle the checkbox back on (and set `canMove` to `true`), the box should follow the movement again. In other words, whether the dot can move or not should stay synchronized to whether the checkbox is checked.

<Hint>

You can't declare an Effect conditionally. However, the code inside the Effect can use conditions!

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, []);

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)} 
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

<Solution>

One solution is to wrap the `setPosition` call into an `if (canMove) { ... }` condition:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  useEffect(() => {
    function handleMove(e) {
      if (canMove) {
        setPosition({ x: e.clientX, y: e.clientY });
      }
    }
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, [canMove]);

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)} 
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

Alternatively, you could wrap the *event subscription* logic into an `if (canMove) { ... }` condition:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    if (canMove) {
      window.addEventListener('pointermove', handleMove);
      return () => window.removeEventListener('pointermove', handleMove);
    }
  }, [canMove]);

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)} 
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

In both of these cases, `canMove` is a reactive variable that you read inside the Effect. This is why it must be specified in the list of Effect dependencies. This ensures that the Effect re-synchronizes after every change to its value.

</Solution>

#### Investigate a stale value bug {/*investigate-a-stale-value-bug*/}

In this example, the pink dot should move when the checkbox is on, and should stop moving when the checkbox is off. The logic for this has already been implemented: the `handleMove` event handler checks the `canMove` state variable.

However, for some reason, the `canMove` state variable inside `handleMove` appears to be "stale": it's always `true`, even after you tick off the checkbox. How is this possible? Find the mistake in the code and fix it.

<Hint>

If you see a linter rule being suppressed, remove the suppression! That's where the mistakes usually are.

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  function handleMove(e) {
    if (canMove) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
  }

  useEffect(() => {
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)} 
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

<Solution>

The problem with the original code was suppressing the dependency linter. If you remove the suppression, you'll see that this Effect depends on the `handleMove` function. This makes sense: `handleMove` is declared inside the component body, which makes it a reactive value. Every reactive value must be specified as a dependency, or it can potentially get stale over time!

The author of the original code has "lied" to React by saying that the Effect does not depend (`[]`) on any reactive values. This is why React did not re-synchronize the Effect after `canMove` has changed (and `handleMove` with it). Because React did not re-synchronize the Effect, the `handleMove` attached as a listener is the `handleMove` function created during the initial render. During the initial render, `canMove` was `true`, which is why `handleMove` from the initial render will forever see that value.

**If you never suppress the linter, you will never see problems with stale values.** There are a few different ways to solve this bug, but you should always start by removing the linter suppression. Then change the code to fix the lint error.

You can change the Effect dependencies to `[handleMove]`, but since it's going to be a newly defined function for every render, you might as well remove dependencies array altogether. Then the Effect *will* re-synchronize after every re-render:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  function handleMove(e) {
    if (canMove) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
  }

  useEffect(() => {
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  });

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)} 
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

This solution works, but it's not ideal. If you put `console.log('Resubscribing')` inside the Effect, you'll notice that it resubscribes after every re-render. Resubscribing is fast, but it would still be nice to avoid doing it so often.

A better fix would be to move the `handleMove` function *inside* the Effect. Then `handleMove` won't be a reactive value, and so your Effect won't depend on a function. Instead, it will need to depend on `canMove` which your code now reads from inside the Effect. This matches the behavior you wanted, since your Effect will now stay synchronized with the value of `canMove`:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  useEffect(() => {
    function handleMove(e) {
      if (canMove) {
        setPosition({ x: e.clientX, y: e.clientY });
      }
    }

    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, [canMove]);

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)} 
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

Try adding `console.log('Resubscribing')` inside the Effect body and notice that now it only resubscribes when you toggle the checkbox (`canMove` changes) or edit the code. This makes it better than the previous approach that always resubscribed.

You'll learn a more general approach to this type of problem in [Separating Events from Effects.](/learn/separating-events-from-effects)

</Solution>

#### Fix a connection switch {/*fix-a-connection-switch*/}

In this example, the chat service in `chat.js` exposes two different APIs: `createEncryptedConnection` and `createUnencryptedConnection`. The root `App` component lets the user choose whether to use encryption or not, and then passes down the corresponding API method to the child `ChatRoom` component as the `createConnection` prop.

Notice that initially, the console logs say the connection is not encrypted. Try toggling the checkbox on: nothing will happen. However, if you change the selected room after that, then the chat will reconnect *and* enable encryption (as you'll see from the console messages). This is a bug. Fix the bug so that toggling the checkbox *also* causes the chat to reconnect.

<Hint>

Suppressing the linter is always suspicious. Could this be a bug?

</Hint>

<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isEncrypted, setIsEncrypted] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <label>
        <input
          type="checkbox"
          checked={isEncrypted}
          onChange={e => setIsEncrypted(e.target.checked)}
        />
        Enable encryption
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        createConnection={isEncrypted ?
          createEncryptedConnection :
          createUnencryptedConnection
        }
      />
    </>
  );
}
```

```js ChatRoom.js active
import { useState, useEffect } from 'react';

export default function ChatRoom({ roomId, createConnection }) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();
    return () => connection.disconnect();
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js chat.js
export function createEncryptedConnection(roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ 🔐 Connecting to "' + roomId + '... (encrypted)');
    },
    disconnect() {
      console.log('❌ 🔐 Disconnected from "' + roomId + '" room (encrypted)');
    }
  };
}

export function createUnencryptedConnection(roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '... (unencrypted)');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room (unencrypted)');
    }
  };
}
```

```css
label { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

If you remove the linter suppression, you will see a lint error. The problem is that `createConnection` is a prop, so it's a reactive value. It can change over time! (And indeed, it should--when the user ticks the checkbox, the parent component passes a different value of the `createConnection` prop.) This is why it should be a dependency. Include it in the list to fix the bug:

<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isEncrypted, setIsEncrypted] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <label>
        <input
          type="checkbox"
          checked={isEncrypted}
          onChange={e => setIsEncrypted(e.target.checked)}
        />
        Enable encryption
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        createConnection={isEncrypted ?
          createEncryptedConnection :
          createUnencryptedConnection
        }
      />
    </>
  );
}
```

```js ChatRoom.js active
import { useState, useEffect } from 'react';

export default function ChatRoom({ roomId, createConnection }) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, createConnection]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js chat.js
export function createEncryptedConnection(roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ 🔐 Connecting to "' + roomId + '... (encrypted)');
    },
    disconnect() {
      console.log('❌ 🔐 Disconnected from "' + roomId + '" room (encrypted)');
    }
  };
}

export function createUnencryptedConnection(roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '... (unencrypted)');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room (unencrypted)');
    }
  };
}
```

```css
label { display: block; margin-bottom: 10px; }
```

</Sandpack>

It is correct that `createConnection` is a dependency. However, this code is a bit fragile because someone could edit the `App` component to pass an inline function as the value of this prop. In that case, its value would be different every time the `App` component re-renders, so the Effect might re-synchronize too often. To avoid this, you can pass `isEncrypted` down instead:

<Sandpack>

```js App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isEncrypted, setIsEncrypted] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <label>
        <input
          type="checkbox"
          checked={isEncrypted}
          onChange={e => setIsEncrypted(e.target.checked)}
        />
        Enable encryption
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        isEncrypted={isEncrypted}
      />
    </>
  );
}
```

```js ChatRoom.js active
import { useState, useEffect } from 'react';
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';

export default function ChatRoom({ roomId, isEncrypted }) {
  useEffect(() => {
    const createConnection = isEncrypted ?
      createEncryptedConnection :
      createUnencryptedConnection;
    const connection = createConnection(roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, isEncrypted]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js chat.js
export function createEncryptedConnection(roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ 🔐 Connecting to "' + roomId + '... (encrypted)');
    },
    disconnect() {
      console.log('❌ 🔐 Disconnected from "' + roomId + '" room (encrypted)');
    }
  };
}

export function createUnencryptedConnection(roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '... (unencrypted)');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room (unencrypted)');
    }
  };
}
```

```css
label { display: block; margin-bottom: 10px; }
```

</Sandpack>

In this version, the `App` component passes a boolean prop instead of a function. Inside the Effect, you decide which function to use. Since both `createEncryptedConnection` and `createUnencryptedConnection` are declared outside the component, they aren't reactive, and don't need to be dependencies. You'll learn more about this in [Removing Effect Dependencies.](/learn/removing-effect-dependencies)

</Solution>

#### Populate a chain of select boxes {/*populate-a-chain-of-select-boxes*/}

In this example, there are two select boxes. One select box lets the user pick a planet. Another select box lets the user pick a place *on that planet.* The second box doesn't work yet. Your task is to make it show the places on the chosen planet.

Look at how the first select box works. It populates the `planetList` state with the result from the `"/planets"` API call. The currently selected planet's ID is kept in the `planetId` state variable. You need to find where to add some additional code so that the `placeList` state variable is populated with the result of the `"/planets/" + planetId + "/places"` API call.

If you implement this right, selecting a planet should populate the place list. Changing a planet should change the place list.

<Hint>

If you have two independent synchronization processes, you need to write two separate Effects.

</Hint>

<Sandpack>

```js App.js
import { useState, useEffect } from 'react';
import { fetchData } from './api.js';

export default function Page() {
  const [planetList, setPlanetList] = useState([])
  const [planetId, setPlanetId] = useState('');

  const [placeList, setPlaceList] = useState([]);
  const [placeId, setPlaceId] = useState('');

  useEffect(() => {
    let ignore = false;
    fetchData('/planets').then(result => {
      if (!ignore) {
        console.log('Fetched a list of planets.');
        setPlanetList(result);
        setPlanetId(result[0].id); // Select the first planet
      }
    });
    return () => {
      ignore = true;
    }
  }, []);

  return (
    <>
      <label>
        Pick a planet:{' '}
        <select value={planetId} onChange={e => {
          setPlanetId(e.target.value);
        }}>
          {planetList.map(planet =>
            <option key={planet.id} value={planet.id}>{planet.name}</option>
          )}
        </select>
      </label>
      <label>
        Pick a place:{' '}
        <select value={placeId} onChange={e => {
          setPlaceId(e.target.value);
        }}>
          {placeList.map(place =>
            <option key={place.id} value={place.id}>{place.name}</option>
          )}
        </select>
      </label>
      <hr />
      <p>You are going to: {placeId || '???'} on {planetId || '???'} </p>
    </>
  );
}
```

```js api.js hidden
export function fetchData(url) {
  if (url === '/planets') {
    return fetchPlanets();
  } else if (url.startsWith('/planets/')) {
    const match = url.match(/^\/planets\/([\w-]+)\/places(\/)?$/);
    if (!match || !match[1] || !match[1].length) {
      throw Error('Expected URL like "/planets/earth/places". Received: "' + url + '".');
    }
    return fetchPlaces(match[1]);
  } else throw Error('Expected URL like "/planets" or "/planets/earth/places". Received: "' + url + '".');
}

async function fetchPlanets() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve([{
        id: 'earth',
        name: 'Earth'
      }, {
        id: 'venus',
        name: 'Venus'
      }, {
        id: 'mars',
        name: 'Mars'        
      }]);
    }, 1000);
  });
}

async function fetchPlaces(planetId) {
  if (typeof planetId !== 'string') {
    throw Error(
      'fetchPlaces(planetId) expects a string argument. ' +
      'Instead received: ' + planetId + '.'
    );
  }
  return new Promise(resolve => {
    setTimeout(() => {
      if (planetId === 'earth') {
        resolve([{
          id: 'laos',
          name: 'Laos'
        }, {
          id: 'spain',
          name: 'Spain'
        }, {
          id: 'vietnam',
          name: 'Vietnam'        
        }]);
      } else if (planetId === 'venus') {
        resolve([{
          id: 'aurelia',
          name: 'Aurelia'
        }, {
          id: 'diana-chasma',
          name: 'Diana Chasma'
        }, {
          id: 'kumsong-vallis',
          name: 'Kŭmsŏng Vallis'        
        }]);
      } else if (planetId === 'mars') {
        resolve([{
          id: 'aluminum-city',
          name: 'Aluminum City'
        }, {
          id: 'new-new-york',
          name: 'New New York'
        }, {
          id: 'vishniac',
          name: 'Vishniac'
        }]);
      } else throw Error('Unknown planet ID: ' + planetId);
    }, 1000);
  });
}
```

```css
label { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

There are two independent synchronization processes:

- The first select box is synchronized to the remote list of planets.
- The second select box is synchronized to the remote list of places for the current `planetId`.

This is why it makes sense to describe them as two separate Effects. Here's an example of how you could do this:

<Sandpack>

```js App.js
import { useState, useEffect } from 'react';
import { fetchData } from './api.js';

export default function Page() {
  const [planetList, setPlanetList] = useState([])
  const [planetId, setPlanetId] = useState('');

  const [placeList, setPlaceList] = useState([]);
  const [placeId, setPlaceId] = useState('');

  useEffect(() => {
    let ignore = false;
    fetchData('/planets').then(result => {
      if (!ignore) {
        console.log('Fetched a list of planets.');
        setPlanetList(result);
        setPlanetId(result[0].id); // Select the first planet
      }
    });
    return () => {
      ignore = true;
    }
  }, []);

  useEffect(() => {
    if (planetId === '') {
      // Nothing is selected in the first box yet
      return;
    }

    let ignore = false;
    fetchData('/planets/' + planetId + '/places').then(result => {
      if (!ignore) {
        console.log('Fetched a list of places on "' + planetId + '".');
        setPlaceList(result);
        setPlaceId(result[0].id); // Select the first place
      }
    });
    return () => {
      ignore = true;
    }
  }, [planetId]);

  return (
    <>
      <label>
        Pick a planet:{' '}
        <select value={planetId} onChange={e => {
          setPlanetId(e.target.value);
        }}>
          {planetList.map(planet =>
            <option key={planet.id} value={planet.id}>{planet.name}</option>
          )}
        </select>
      </label>
      <label>
        Pick a place:{' '}
        <select value={placeId} onChange={e => {
          setPlaceId(e.target.value);
        }}>
          {placeList.map(place =>
            <option key={place.id} value={place.id}>{place.name}</option>
          )}
        </select>
      </label>
      <hr />
      <p>You are going to: {placeId || '???'} on {planetId || '???'} </p>
    </>
  );
}
```

```js api.js hidden
export function fetchData(url) {
  if (url === '/planets') {
    return fetchPlanets();
  } else if (url.startsWith('/planets/')) {
    const match = url.match(/^\/planets\/([\w-]+)\/places(\/)?$/);
    if (!match || !match[1] || !match[1].length) {
      throw Error('Expected URL like "/planets/earth/places". Received: "' + url + '".');
    }
    return fetchPlaces(match[1]);
  } else throw Error('Expected URL like "/planets" or "/planets/earth/places". Received: "' + url + '".');
}

async function fetchPlanets() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve([{
        id: 'earth',
        name: 'Earth'
      }, {
        id: 'venus',
        name: 'Venus'
      }, {
        id: 'mars',
        name: 'Mars'        
      }]);
    }, 1000);
  });
}

async function fetchPlaces(planetId) {
  if (typeof planetId !== 'string') {
    throw Error(
      'fetchPlaces(planetId) expects a string argument. ' +
      'Instead received: ' + planetId + '.'
    );
  }
  return new Promise(resolve => {
    setTimeout(() => {
      if (planetId === 'earth') {
        resolve([{
          id: 'laos',
          name: 'Laos'
        }, {
          id: 'spain',
          name: 'Spain'
        }, {
          id: 'vietnam',
          name: 'Vietnam'        
        }]);
      } else if (planetId === 'venus') {
        resolve([{
          id: 'aurelia',
          name: 'Aurelia'
        }, {
          id: 'diana-chasma',
          name: 'Diana Chasma'
        }, {
          id: 'kumsong-vallis',
          name: 'Kŭmsŏng Vallis'        
        }]);
      } else if (planetId === 'mars') {
        resolve([{
          id: 'aluminum-city',
          name: 'Aluminum City'
        }, {
          id: 'new-new-york',
          name: 'New New York'
        }, {
          id: 'vishniac',
          name: 'Vishniac'
        }]);
      } else throw Error('Unknown planet ID: ' + planetId);
    }, 1000);
  });
}
```

```css
label { display: block; margin-bottom: 10px; }
```

</Sandpack>

This code is a bit repetitive. However, that's not a good reason to combine it into a single Effect! If you did this, you'd have to combine both Effect's dependencies into one list, and then changing the planet would refetch the list of all planets. Effects are not a tool for code reuse.

Instead, to reduce repetition, you can extract some logic into a custom Hook like `useSelectOptions` below:

<Sandpack>

```js App.js
import { useState } from 'react';
import { useSelectOptions } from './useSelectOptions.js';

export default function Page() {
  const [
    planetList,
    planetId,
    setPlanetId
  ] = useSelectOptions('/planets');

  const [
    placeList,
    placeId,
    setPlaceId
  ] = useSelectOptions(planetId ? `/planets/${planetId}/places` : null);

  return (
    <>
      <label>
        Pick a planet:{' '}
        <select value={planetId} onChange={e => {
          setPlanetId(e.target.value);
        }}>
          {planetList?.map(planet =>
            <option key={planet.id} value={planet.id}>{planet.name}</option>
          )}
        </select>
      </label>
      <label>
        Pick a place:{' '}
        <select value={placeId} onChange={e => {
          setPlaceId(e.target.value);
        }}>
          {placeList?.map(place =>
            <option key={place.id} value={place.id}>{place.name}</option>
          )}
        </select>
      </label>
      <hr />
      <p>You are going to: {placeId || '...'} on {planetId || '...'} </p>
    </>
  );
}
```

```js useSelectOptions.js
import { useState, useEffect } from 'react';
import { fetchData } from './api.js';

export function useSelectOptions(url) {
  const [list, setList] = useState(null);
  const [selectedId, setSelectedId] = useState('');
  useEffect(() => {
    if (url === null) {
      return;
    }

    let ignore = false;
    fetchData(url).then(result => {
      if (!ignore) {
        setList(result);
        setSelectedId(result[0].id);
      }
    });
    return () => {
      ignore = true;
    }
  }, [url]);
  return [list, selectedId, setSelectedId];
}
```

```js api.js hidden
export function fetchData(url) {
  if (url === '/planets') {
    return fetchPlanets();
  } else if (url.startsWith('/planets/')) {
    const match = url.match(/^\/planets\/([\w-]+)\/places(\/)?$/);
    if (!match || !match[1] || !match[1].length) {
      throw Error('Expected URL like "/planets/earth/places". Received: "' + url + '".');
    }
    return fetchPlaces(match[1]);
  } else throw Error('Expected URL like "/planets" or "/planets/earth/places". Received: "' + url + '".');
}

async function fetchPlanets() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve([{
        id: 'earth',
        name: 'Earth'
      }, {
        id: 'venus',
        name: 'Venus'
      }, {
        id: 'mars',
        name: 'Mars'        
      }]);
    }, 1000);
  });
}

async function fetchPlaces(planetId) {
  if (typeof planetId !== 'string') {
    throw Error(
      'fetchPlaces(planetId) expects a string argument. ' +
      'Instead received: ' + planetId + '.'
    );
  }
  return new Promise(resolve => {
    setTimeout(() => {
      if (planetId === 'earth') {
        resolve([{
          id: 'laos',
          name: 'Laos'
        }, {
          id: 'spain',
          name: 'Spain'
        }, {
          id: 'vietnam',
          name: 'Vietnam'        
        }]);
      } else if (planetId === 'venus') {
        resolve([{
          id: 'aurelia',
          name: 'Aurelia'
        }, {
          id: 'diana-chasma',
          name: 'Diana Chasma'
        }, {
          id: 'kumsong-vallis',
          name: 'Kŭmsŏng Vallis'        
        }]);
      } else if (planetId === 'mars') {
        resolve([{
          id: 'aluminum-city',
          name: 'Aluminum City'
        }, {
          id: 'new-new-york',
          name: 'New New York'
        }, {
          id: 'vishniac',
          name: 'Vishniac'
        }]);
      } else throw Error('Unknown planet ID: ' + planetId);
    }, 1000);
  });
}
```

```css
label { display: block; margin-bottom: 10px; }
```

</Sandpack>

Check the `useSelectOptions.js` tab in the sandbox to see how it works. Ideally, most Effects in your application should eventually be replaced by custom Hooks, whether written by you or by the community. Custom Hooks hide the synchronization logic, so the calling component doesn't know about the Effect. As you keep working on your app, you'll develop a palette of Hooks to choose from, and eventually you won't need to write Effects in your components very often.

</Solution>

</Challenges>
