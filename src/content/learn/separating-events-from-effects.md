---
title: 'Separating Events from Effects(جداسازی رویدادها از افکت ها)'
---

<Intro>

Event handlers only re-run when you perform the same interaction again. Unlike event handlers, Effects re-synchronize if some value they read, like a prop or a state variable, is different from what it was during the last render. Sometimes, you also want a mix of both behaviors: an Effect that re-runs in response to some values but not others. This page will teach you how to do that.
//////////////////
ترجمه: Event handlers فقط زمانی مجدداً اجرا می‌شوند که شما باز هم همان تعامل را انجام دهید. برخلاف event handlers، Effects در صورتی که برخی از مقادیری که خوانده‌اند، مانند یک prop یا یک متغیر state، با آنچه در طول آخرین رندر بود متفاوت باشد، مجدداً همگام‌سازی می‌شوند. گاهی اوقات، شما همچنین می‌خواهید ترکیبی از هر دو رفتار داشته باشید: یک Effect که در پاسخ به برخی مقادیر مجدداً اجرا می‌شود، اما به برخی دیگر نه. این صفحه به شما چگونگی انجام این کار را آموزش می‌دهد.

</Intro>

<YouWillLearn>

- How to choose between an event handler and an Effect
- Why Effects are reactive, and event handlers are not
- What to do when you want a part of your Effect's code to not be reactive
- What Effect Events are, and how to extract them from your Effects
- How to read the latest props and state from Effects using Effect Events

///////////////////
- چگونه بین event handler و Effect انتخاب کنید
- چرا Effects واکنش‌گر هستند، ولی event handlers نه
- چه کاری باید انجام دهید وقتی می‌خواهید بخشی از کد Effect شما واکنش‌گر نباشد
- Effect Events چیستند و چگونه آن‌ها را از Effect های خود استخراج کنید
- چگونه می‌توانید از Effect Events برای خواندن آخرین props و state استفاده کنید

</YouWillLearn>

## Choosing between event handlers and Effects {/*choosing-between-event-handlers-and-effects*/}

First, let's recap the difference between event handlers and Effects.

Imagine you're implementing a chat room component. Your requirements look like this:

1. Your component should automatically connect to the selected chat room.
1. When you click the "Send" button, it should send a message to the chat.

Let's say you've already implemented the code for them, but you're not sure where to put it. Should you use event handlers or Effects? Every time you need to answer this question, consider [*why* the code needs to run.](/learn/synchronizing-with-effects#what-are-effects-and-how-are-they-different-from-events)
////////////////
ترجمه: ابتدا، بیایید تفاوت بین event handlers و Effects را به یاد بیاوریم.

تصور کنید که شما در حال پیاده‌سازی یک کامپوننت اتاق چت هستید. نیازمندی‌های شما به این صورت هستند:

1. کامپوننت شما باید به طور خودکار به اتاق چت انتخاب شده متصل شود.
1. هنگامی که روی دکمه "ارسال" کلیک می‌کنید، باید یک پیام به چت ارسال شود.

بگذارید بگوییم که شما قبلاً کد آن‌ها را پیاده‌سازی کرده‌اید، اما مطمئن نیستید کجا باید آن‌ها را قرار دهید. آیا باید از event handlers یا Effects استفاده کنید؟ هر بار که برای پاسخ به این سوال نیاز دارید، [*چرا* کد باید اجرا شود را در نظر بگیرید.](/learn/synchronizing-with-effects#what-are-effects-and-how-are-they-different-from-events)

### Event handlers run in response to specific interactions {/*event-handlers-run-in-response-to-specific-interactions*/}

From the user's perspective, sending a message should happen *because* the particular "Send" button was clicked. The user will get rather upset if you send their message at any other time or for any other reason. This is why sending a message should be an event handler. Event handlers let you handle specific interactions:
///////////////
از دیدگاه کاربر، ارسال پیام باید *به دلیل* کلیک روی دکمه "ارسال" مشخص شود. کاربر بسیار ناراحت خواهد شد اگر پیامش را در هر زمان یا به هر دلیل دیگری ارسال کنید. به همین دلیل، ارسال پیام باید یک event handler باشد. Event handlers به شما اجازه می‌دهند تا بازتاب تعاملات خاصی را کنترل کنید:

```js {4-6}
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');
  // ...
  function handleSendClick() {
    sendMessage(message);
  }
  // ...
  return (
    <>
      <input value={message} onChange={e => setMessage(e.target.value)} />
      <button onClick={handleSendClick}>Send</button>;
    </>
  );
}
```

With an event handler, you can be sure that `sendMessage(message)` will *only* run if the user presses the button.
/////////////////
با استفاده از یک event handler، می‌توانید مطمئن شوید که `sendMessage(message)` فقط در صورتی اجرا می‌شود که کاربر دکمه را فشار دهد.

### Effects run whenever synchronization is needed {/*effects-run-whenever-synchronization-is-needed*/}

Recall that you also need to keep the component connected to the chat room. Where does that code go?

The *reason* to run this code is not some particular interaction. It doesn't matter why or how the user navigated to the chat room screen. Now that they're looking at it and could interact with it, the component needs to stay connected to the selected chat server. Even if the chat room component was the initial screen of your app, and the user has not performed any interactions at all, you would *still* need to connect. This is why it's an Effect:
///////////////
به یاد داشته باشید که شما همچنین باید کامپوننت را به اتاق گفتگو متصل نگه دارید. آن کد کجا می رود؟
- برای نگه داشتن کامپوننت خود به اتاق چت متصل، می‌توانید از Effect استفاده کنید؟

دلیل اجرای این کد، یک تعامل خاص خاصی نیست. مهم نیست که کاربر چگونه و چرا به صفحه اتاق چت رفته است. حال که آنها به آن نگاه می کنند و می توانند با آن تعامل داشته باشند، این کامپوننت باید به سرور چت انتخاب شده متصل بماند. حتی اگر کامپوننت اتاق چت صفحه اول برنامه شما بود و کاربر هیچ تعاملی انجام نداده باشد، شما هنوز هم باید به آن متصل شوید. به همین دلیل، این یک Effect است.


```js {3-9}
function ChatRoom({ roomId }) {
  // ...
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

With this code, you can be sure that there is always an active connection to the currently selected chat server, *regardless* of the specific interactions performed by the user. Whether the user has only opened your app, selected a different room, or navigated to another screen and back, your Effect ensures that the component will *remain synchronized* with the currently selected room, and will [re-connect whenever it's necessary.](/learn/lifecycle-of-reactive-effects#why-synchronization-may-need-to-happen-more-than-once)
//////////////////////
با این کد، می توانید اطمینان حاصل کنید که همیشه یک اتصال فعال به سرور چت انتخاب شده وجود دارد، *بدون در نظر گرفتن* تعاملات خاصی که توسط کاربر انجام شده است. برای مثال، مهم نیست که کاربر فقط برنامه شما را باز کرده باشد، یک اتاق متفاوت را انتخاب کرده باشد یا به صفحه دیگری رفته و به عقب برگشته باشد، Effect شما تضمین می کند که کامپوننت با اتاق انتخاب شده همگام می شود و هر زمان که لازم باشد، مجدداً به آن متصل می شود ².

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection, sendMessage } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  function handleSendClick() {
    sendMessage(message);
  }

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input value={message} onChange={e => setMessage(e.target.value)} />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
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
export function sendMessage(message) {
  console.log('🔵 You sent: ' + message);
}

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
input, select { margin-right: 20px; }
```

</Sandpack>

## Reactive values and reactive logic {/*reactive-values-and-reactive-logic*/}

Intuitively, you could say that event handlers are always triggered "manually", for example by clicking a button. Effects, on the other hand, are "automatic": they run and re-run as often as it's needed to stay synchronized.

There is a more precise way to think about this.

Props, state, and variables declared inside your component's body are called <CodeStep step={2}>reactive values</CodeStep>. In this example, `serverUrl` is not a reactive value, but `roomId` and `message` are. They participate in the rendering data flow:

//////////////////////
به طور غیر رسمی، می توانید بگویید که event handlers همیشه به صورت "دستی" فعال می شوند، به عنوان مثال با کلیک روی یک دکمه. از طرف دیگر، Effects "خودکار" هستند: همیشه و هر زمان که لازم باشد، اجرا و اجرا مجدد می شوند تا همگام باشند.

راه دقیق تری برای فکر کردن در این مورد وجود دارد.

Props، state و متغیرهایی که در بدنه کامپوننت شما تعریف شده اند، به نام <CodeStep step={2}>مقادیر واکنشی</CodeStep> شناخته می شوند. در این مثال، `serverUrl` یک مقدار واکنشی نیست، اما `roomId` و `message` هستند. آنها در جریان داده های رندرینگ شرکت می کنند.



```js [[2, 3, "roomId"], [2, 4, "message"]]
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  // ...
}
```

Reactive values like these can change due to a re-render. For example, the user may edit the `message` or choose a different `roomId` in a dropdown. Event handlers and Effects respond to changes differently:

- **Logic inside event handlers is *not reactive.*** It will not run again unless the user performs the same interaction (e.g. a click) again. Event handlers can read reactive values without "reacting" to their changes.
- **Logic inside Effects is *reactive.*** If your Effect reads a reactive value, [you have to specify it as a dependency.](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) Then, if a re-render causes that value to change, React will re-run your Effect's logic with the new value.

Let's revisit the previous example to illustrate this difference.
/////////////////////
مقادیر واکنشی مانند این می توانند به دلیل یک بازسازی تغییر کنند. به عنوان مثال، کاربر ممکن است پیام را ویرایش کند یا در یک کشوی کشویی `roomId` متفاوتی را انتخاب کند. event handlers و Effects به تغییرات به شکل های مختلفی پاسخ می دهند:

- **منطق داخل event handlers *واکنشی نیست*.** مگر اینکه کاربر تعامل مشابهی (مانند کلیک) دوباره انجام دهد، دوباره اجرا نخواهد شد. event handlers می توانند مقادیر واکنشی را بدون "واکنش" به تغییرات آنها بخوانند.
- **منطق داخل Effects *واکنشی است*.** اگر Effect شما یک مقدار واکنشی را بخواند، [شما باید آن را به عنوان یک وابستگی مشخص کنید.](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) سپس، اگر یک بازسازی باعث تغییر آن مقدار شود، React منطق Effect شما را با مقدار جدید مجدداً اجرا خواهد کرد.

بیایید به مثال قبلی برگردیم تا این تفاوت را نشان دهیم.


### Logic inside event handlers is not reactive {/*logic-inside-event-handlers-is-not-reactive*/}

Take a look at this line of code. Should this logic be reactive or not?
به این خط کد نگاهی بیندازید. آیا این منطق باید واکنشی باشد یا خیر؟

```js [[2, 2, "message"]]
    // ...
    sendMessage(message);
    // ...
```

From the user's perspective, **a change to the `message` does _not_ mean that they want to send a message.** It only means that the user is typing. In other words, the logic that sends a message should not be reactive. It should not run again only because the <CodeStep step={2}>reactive value</CodeStep> has changed. That's why it belongs in the event handler:
////////////////
از دیدگاه کاربر، **تغییر در `message` به معنای ارسال پیام نیست**. فقط به این معنی است که کاربر در حال تایپ است. به عبارت دیگر، منطقی که پیام را ارسال می کند، باید واکنشی نباشد. فقط به دلیل تغییر در <CodeStep step={2}>مقدار واکنشی</CodeStep>، دوباره اجرا نشود. به همین دلیل، منطق مربوط به ارسال پیام در event handler قرار دارد.

```js {2}
  function handleSendClick() {
    sendMessage(message);
  }
```

Event handlers aren't reactive, so `sendMessage(message)` will only run when the user clicks the Send button.
////////////
کنترل‌کننده‌های رویداد واکنش‌پذیر نیستند، بنابراین «sendMessage(message)» تنها زمانی اجرا می‌شود که کاربر روی دکمه ارسال کلیک کند.

### Logic inside Effects is reactive {/*logic-inside-effects-is-reactive*/}

Now let's return to these lines:
حال به این سطور برگردیم:

```js [[2, 2, "roomId"]]
    // ...
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    // ...
```

From the user's perspective, **a change to the `roomId` *does* mean that they want to connect to a different room.** In other words, the logic for connecting to the room should be reactive. You *want* these lines of code to "keep up" with the <CodeStep step={2}>reactive value</CodeStep>, and to run again if that value is different. That's why it belongs in an Effect:
//////////////
از دیدگاه کاربر، **تغییر در `roomId` به معنای اتصال به یک اتاق متفاوت است.** به عبارت دیگر، منطق اتصال به اتاق باید واکنشی باشد. شما می خواهید این خطوط کد با <CodeStep step={2}>مقدار واکنشی</CodeStep> "همراهی کنند" و در صورت تغییر مجدداً اجرا شوند. به همین دلیل، این منطق در یک Effect قرار دارد.

```js {2-3}
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect()
    };
  }, [roomId]);
```

Effects are reactive, so `createConnection(serverUrl, roomId)` and `connection.connect()` will run for every distinct value of `roomId`. Your Effect keeps the chat connection synchronized to the currently selected room.
//////////////
اثرات واکنشی هستند، بنابراین `createConnection(serverUrl, roomId)` و `connection.connect()` برای هر مقدار متمایز از `roomId` اجرا خواهند شد. Effect شما اتصال چت را با اتاق انتخاب شده همگام نگه می دارد ¹.

## Extracting non-reactive logic out of Effects {/*extracting-non-reactive-logic-out-of-effects*/}

Things get more tricky when you want to mix reactive logic with non-reactive logic.

For example, imagine that you want to show a notification when the user connects to the chat. You read the current theme (dark or light) from the props so that you can show the notification in the correct color:
///////////////////
وقتی می خواهید منطق واکنشی را با منطق غیر واکنشی ترکیب کنید، کارها پیچیده تر می شوند.

به عنوان مثال، تصور کنید که می خواهید هنگام اتصال کاربر به چت، یک اعلان نمایش دهید. شما تم فعلی (تاریک یا روشن) را از props می خوانید تا بتوانید اعلان را با رنگ درست نشان دهید:


```js {1,4-6}
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    // ...
```

However, `theme` is a reactive value (it can change as a result of re-rendering), and [every reactive value read by an Effect must be declared as its dependency.](/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency) Now you have to specify `theme` as a dependency of your Effect:
///////////////
با این حال، `theme` یک <CodeStep step={2}>مقدار واکنشی</CodeStep> است (می تواند به دلیل بازسازی تغییر کند) و [هر مقدار واکنشی که توسط یک Effect خوانده شده است، باید به عنوان وابستگی آن مشخص شود.](/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency) حالا باید `theme` را به عنوان یک وابستگی Effect خود مشخص کنید ².

```js {5,11}
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => {
      connection.disconnect()
    };
  }, [roomId, theme]); // ✅ All dependencies declared
  // ...
```

Play with this example and see if you can spot the problem with this user experience:
با این مثال بازی کنید و ببینید آیا می توانید مشکل را در این تجربه کاربری تشخیص دهید:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, theme]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

```js notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label { display: block; margin-top: 10px; }
```

</Sandpack>

When the `roomId` changes, the chat re-connects as you would expect. But since `theme` is also a dependency, the chat *also* re-connects every time you switch between the dark and the light theme. That's not great!

In other words, you *don't* want this line to be reactive, even though it is inside an Effect (which is reactive):
///////////////////////
زمانی که `roomId` تغییر می کند، چت به طور معمول مجدداً متصل می شود. اما از آنجا که `theme` همچنین یک وابستگی است، چت *همچنین* هر بار که بین تم تاریک و روشن تغییر می کنید، مجدداً متصل می شود. این خوب نیست!

به عبارت دیگر، شما *نمی خواهید* این خط را واکنشی کنید، با این حال در یک Effect (که واکنشی است) قرار دارد:

```js
      // ...
      showNotification('Connected!', theme);
      // ...
```

You need a way to separate this non-reactive logic from the reactive Effect around it.
شما به راهی برای جدا کردن این منطق غیر واکنشی از افکت واکنشی اطراف آن نیاز دارید.

### Declaring an Effect Event {/*declaring-an-effect-event*/}

<Wip>

This section describes an **experimental API that has not yet been released** in a stable version of React.
/////////////
این بخش **API آزمایشی را توصیف می کند که هنوز** در نسخه پایدار React منتشر نشده است.

</Wip>

Use a special Hook called [`useEffectEvent`](/reference/react/experimental_useEffectEvent) to extract this non-reactive logic out of your Effect:
////////////
از Hook خاصی به نام `استفاده از اثر` استفاده کنید تا این منطق غیر واکنشی را از Effect خود استخراج کنید ¹.

```js {1,4-6}
import { useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });
  // ...
```

Here, `onConnected` is called an *Effect Event.* It's a part of your Effect logic, but it behaves a lot more like an event handler. The logic inside it is not reactive, and it always "sees" the latest values of your props and state.
////////////
در اینجا، `onConnected` یک *رویداد اثر* نامیده می شود. این یک بخش از منطق Effect شماست، اما بسیار شبیه به یک event handler عمل می کند. منطق داخل آن واکنشی نیست و همیشه آخرین مقادیر props و state شما را "می بیند" ¹.

Now you can call the `onConnected` Effect Event from inside your Effect:
اکنون می‌توانید رویداد «onConnected» را از داخل افکت خود فراخوانی کنید:

```js {2-4,9,13}
function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      onConnected();
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```

This solves the problem. Note that you had to *remove* `onConnected` from the list of your Effect's dependencies. **Effect Events are not reactive and must be omitted from dependencies.**

Verify that the new behavior works as you would expect:
///////////
این مشکل را حل می کند. توجه کنید که باید `onConnected` را از لیست وابستگی های Effect خود *حذف* کنید. **رویدادهای Effect واکنشی نیستند و باید از وابستگی ها حذف شوند.**

بررسی کنید که رفتار جدید به دلخواه شما کار می کند:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      onConnected();
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

```js notifications.js hidden
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label { display: block; margin-top: 10px; }
```

</Sandpack>

You can think of Effect Events as being very similar to event handlers. The main difference is that event handlers run in response to a user interactions, whereas Effect Events are triggered by you from Effects. Effect Events let you "break the chain" between the reactivity of Effects and code that should not be reactive.
//////////////
می توانید به Effect Events به عنوان چیزی بسیار شبیه به event handlers فکر کنید. تفاوت اصلی این است که event handlers در پاسخ به تعاملات کاربر اجرا می شوند، در حالی که Effect Events توسط شما از Effects فعال می شوند. Effect Events به شما اجازه می دهند که "زنجیر" بین واکنشگری Effects و کدی که نباید واکنشگری باشد، را شکست دهید ¹.

### Reading latest props and state with Effect Events {/*reading-latest-props-and-state-with-effect-events*/}

<Wip>

This section describes an **experimental API that has not yet been released** in a stable version of React.
///////////
این بخش، یک API **آزمایشی** را توصیف می کند که هنوز در نسخه پایدار React منتشر نشده است ¹.

</Wip>

Effect Events let you fix many patterns where you might be tempted to suppress the dependency linter.
///////////
Effect Events به شما امکان می دهد الگوهای بسیاری را اصلاح کنید که در آنها ممکن است وسوسه شوید که لایه وابستگی را سرکوب کنید.

For example, say you have an Effect to log the page visits:
به عنوان مثال، بگویید که یک افکت برای ثبت بازدیدهای صفحه دارید:

```js
function Page() {
  useEffect(() => {
    logVisit();
  }, []);
  // ...
}
```

Later, you add multiple routes to your site. Now your `Page` component receives a `url` prop with the current path. You want to pass the `url` as a part of your `logVisit` call, but the dependency linter complains:
//////////////
ترجمه کن: بعداً، چندین مسیر به سایت شما اضافه می شود. حالا کامپوننت `Page` شما یک `url` prop با مسیر فعلی دریافت می کند. شما می خواهید `url` را به عنوان بخشی از تماس `logVisit` خود ارسال کنید، اما وابستگی linter شکایت می کند ¹.

```js {1,3}
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, []); // 🔴 React Hook useEffect has a missing dependency: 'url'
  // ...
}
```

Think about what you want the code to do. You *want* to log a separate visit for different URLs since each URL represents a different page. In other words, this `logVisit` call *should* be reactive with respect to the `url`. This is why, in this case, it makes sense to follow the dependency linter, and add `url` as a dependency:
////////////
به طوری که می خواهید کد چه کاری انجام دهد، شما *می خواهید* برای URL های مختلف بازدید جداگانه ایجاد کنید زیرا هر URL صفحه متفاوتی را نشان می دهد. به عبارت دیگر، این تماس `logVisit` *باید* با توجه به `url` واکنشی باشد. به همین دلیل، در این مورد، منطقی است که وابستگی linter را دنبال کرده و `url` را به عنوان یک وابستگی اضافه کنید ².

```js {4}
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, [url]); // ✅ All dependencies declared
  // ...
}
```

Now let's say you want to include the number of items in the shopping cart together with every page visit:
//////////////
حال فرض کنید می خواهید تعداد کالاهای موجود در سبد خرید را با هر بازدید از صفحه وارد کنید:

```js {2-3,6}
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  useEffect(() => {
    logVisit(url, numberOfItems);
  }, [url]); // 🔴 React Hook useEffect has a missing dependency: 'numberOfItems'
  // ...
}
```

You used `numberOfItems` inside the Effect, so the linter asks you to add it as a dependency. However, you *don't* want the `logVisit` call to be reactive with respect to `numberOfItems`. If the user puts something into the shopping cart, and the `numberOfItems` changes, this *does not mean* that the user visited the page again. In other words, *visiting the page* is, in some sense, an "event". It happens at a precise moment in time.
/////////////////
شما از `numberOfItems` در داخل Effect استفاده کرده اید، بنابراین linter از شما خواسته است که آن را به عنوان یک وابستگی اضافه کنید. با این حال، شما *نمی خواهید* تماس `logVisit` را با توجه به `numberOfItems` واکنشی کنید. اگر کاربر چیزی را به سبد خرید اضافه کند و `numberOfItems` تغییر کند، این *به معنای* بازدید کاربر از صفحه نیست. به عبارت دیگر، *بازدید از صفحه* به معنایی، یک "رویداد" است. در یک لحظه دقیق اتفاق می افتد.

Split the code in two parts:
کد را به دو قسمت تقسیم کنید:

```js {5-7,10}
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    onVisit(url);
  }, [url]); // ✅ All dependencies declared
  // ...
}
```

Here, `onVisit` is an Effect Event. The code inside it isn't reactive. This is why you can use `numberOfItems` (or any other reactive value!) without worrying that it will cause the surrounding code to re-execute on changes.
////////////
در اینجا، `onVisit` یک *رویداد اثر* است. کد داخل آن واکنشی نیست. به همین دلیل می توانید از `numberOfItems` (یا هر مقدار واکنشی دیگری!) استفاده کنید بدون اینکه نگران بازنشانی کد اطراف آن در پاسخ به تغییرات باشید ¹.

On the other hand, the Effect itself remains reactive. Code inside the Effect uses the `url` prop, so the Effect will re-run after every re-render with a different `url`. This, in turn, will call the `onVisit` Effect Event.
/////////////
از سوی دیگر، Effect خود واکنشی باقی می ماند. کد داخل Effect از prop `url` استفاده می کند، بنابراین Effect پس از هر بازسازی با `url` متفاوت مجدداً اجرا خواهد شد. این، به نوبه خود، رویداد اثر `onVisit` را فراخوانی خواهد کرد ¹.

As a result, you will call `logVisit` for every change to the `url`, and always read the latest `numberOfItems`. However, if `numberOfItems` changes on its own, this will not cause any of the code to re-run.
////////////////////////
به عنوان نتیجه، شما برای هر تغییر در `url`، `logVisit` را فراخوانی خواهید کرد و همیشه آخرین `numberOfItems` را خواهید خواند. با این حال، اگر `numberOfItems` به تنهایی تغییر کند، این هیچ یک از کد را مجدداً اجرا نخواهد کرد.

<Note>

You might be wondering if you could call `onVisit()` with no arguments, and read the `url` inside it:
////////
ممکن است تعجب کنید که آیا می‌توانید «onVisit()» را بدون آرگومان فراخوانی کنید و «url» داخل آن را بخوانید:

```js {2,6}
  const onVisit = useEffectEvent(() => {
    logVisit(url, numberOfItems);
  });

  useEffect(() => {
    onVisit();
  }, [url]);
```

This would work, but it's better to pass this `url` to the Effect Event explicitly. **By passing `url` as an argument to your Effect Event, you are saying that visiting a page with a different `url` constitutes a separate "event" from the user's perspective.** The `visitedUrl` is a *part* of the "event" that happened:
/////////////
این روش کار می کند، اما بهتر است `url` را به صورت صریح به عنوان یک آرگومان به رویداد Effect منتقل کنید. **با انتقال `url` به عنوان یک آرگومان به رویداد Effect خود، شما می گویید که بازدید از یک صفحه با `url` متفاوت، یک "رویداد" جداگانه از دیدگاه کاربر است.** `visitedUrl` بخشی از "رویداد" است که رخ داده است:

```js {1-2,6}
  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    onVisit(url);
  }, [url]);
```

Since your Effect Event explicitly "asks" for the `visitedUrl`, now you can't accidentally remove `url` from the Effect's dependencies. If you remove the `url` dependency (causing distinct page visits to be counted as one), the linter will warn you about it. You want `onVisit` to be reactive with regards to the `url`, so instead of reading the `url` inside (where it wouldn't be reactive), you pass it *from* your Effect.

This becomes especially important if there is some asynchronous logic inside the Effect:
/////////////////
با این حال، `visitedUrl` را به صورت صریح از Effect Event خود "خواسته" کرده اید، حالا نمی توانید به طور تصادفی `url` را از وابستگی های Effect حذف کنید. اگر وابستگی `url` را حذف کنید (باعث شمارش بازدیدهای متمایز به عنوان یکی می شود)، linter به شما هشدار خواهد داد. شما می خواهید `onVisit` با توجه به `url` واکنشی باشد، بنابراین به جای خواندن `url` در داخل (جایی که واکنشی نخواهد بود)، آن را *از* Effect خود منتقل می کنید.

این به خصوص در صورت وجود منطق ناهمزمان در داخل Effect مهم است:

```js {6,8}
  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    setTimeout(() => {
      onVisit(url);
    }, 5000); // Delay logging visits
  }, [url]);
```

Here, `url` inside `onVisit` corresponds to the *latest* `url` (which could have already changed), but `visitedUrl` corresponds to the `url` that originally caused this Effect (and this `onVisit` call) to run.
//////////////////////////
در اینجا، `url` در داخل `onVisit` با *آخرین* `url` (که ممکن است قبلاً تغییر کرده باشد) مطابقت دارد، اما `visitedUrl` با `url` اصلی که باعث اجرای این Effect (و این `onVisit` call) شده است، مطابقت دارد ².

</Note>

<DeepDive>

#### Is it okay to suppress the dependency linter instead? {/*is-it-okay-to-suppress-the-dependency-linter-instead*/}

In the existing codebases, you may sometimes see the lint rule suppressed like this:
در پایگاه های کد موجود، ممکن است گاهی اوقات قانون lint را به این صورت مشاهده کنید:

```js {7-9}
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  useEffect(() => {
    logVisit(url, numberOfItems);
    // 🔴 Avoid suppressing the linter like this:
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [url]);
  // ...
}
```

After `useEffectEvent` becomes a stable part of React, we recommend **never suppressing the linter**.
////////////////
بعد از اینکه `استفاده از اثر` به عنوان یک بخش پایدار از React شد، ما توصیه می کنیم **هرگز لینتر را سرکوب نکنید** ¹.

The first downside of suppressing the rule is that React will no longer warn you when your Effect needs to "react" to a new reactive dependency you've introduced to your code. In the earlier example, you added `url` to the dependencies *because* React reminded you to do it. You will no longer get such reminders for any future edits to that Effect if you disable the linter. This leads to bugs.
/////////////////////
اولین مشکلی که با سرکوب کردن این قانون مواجه خواهید شد، این است که React دیگر شما را هشدار نمی دهد که Effect شما باید به یک وابستگی واکنشی جدیدی که به کد خود اضافه کرده اید "واکنش" نشان دهد. در مثال قبل، شما `url` را به وابستگی ها اضافه کردید *زیرا* React به شما یادآوری کرد. اگر لینتر را غیرفعال کنید، برای هیچ ویرایش آینده ای از آن Effect هشداری دریافت نخواهید کرد و این باعث بروز باگ خواهد شد.

Here is an example of a confusing bug caused by suppressing the linter. In this example, the `handleMove` function is supposed to read the current `canMove` state variable value in order to decide whether the dot should follow the cursor. However, `canMove` is always `true` inside `handleMove`.
////////////////////
اینجا یک مثال از باگ گیج کننده ای است که توسط سرکوب کردن لینتر ایجاد شده است. در این مثال، تابع `handleMove` باید مقدار متغیر وضعیت فعلی `canMove` را بخواند تا تصمیم بگیرد آیا نقطه باید دنبال موشواره برود یا خیر. با این حال، `canMove` همیشه `true` در داخل `handleMove` است.

Can you see why?
می توانید ببینید چرا؟

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


The problem with this code is in suppressing the dependency linter. If you remove the suppression, you'll see that this Effect should depend on the `handleMove` function. This makes sense: `handleMove` is declared inside the component body, which makes it a reactive value. Every reactive value must be specified as a dependency, or it can potentially get stale over time!

The author of the original code has "lied" to React by saying that the Effect does not depend (`[]`) on any reactive values. This is why React did not re-synchronize the Effect after `canMove` has changed (and `handleMove` with it). Because React did not re-synchronize the Effect, the `handleMove` attached as a listener is the `handleMove` function created during the initial render. During the initial render, `canMove` was `true`, which is why `handleMove` from the initial render will forever see that value.

**If you never suppress the linter, you will never see problems with stale values.**

With `useEffectEvent`, there is no need to "lie" to the linter, and the code works as you would expect:
//////////////////////
مشکل با این کد در سرکوب کردن لینتر وابستگی است. اگر سرکوب را بردارید، خواهید دید که این Effect باید به تابع `handleMove` وابستگی داشته باشد. این منطقی است: `handleMove` در بدنه کامپوننت تعریف شده است، که آن را یک مقدار واکنشی می کند. هر مقدار واکنشی باید به عنوان یک وابستگی مشخص شود، در غیر این صورت ممکن است در طول زمان قدیمی شود!

نویسنده کد اصلی با گفتن اینکه Effect به هیچ مقدار واکنشی وابسته نیست (`[]`)، به React دروغ گفته است. به همین دلیل React پس از تغییر `canMove` (و `handleMove` با آن) Effect را مجدداً همگام نکرد. در طول بازسازی اولیه، `canMove` `true` بود، به همین دلیل `handleMove` از بازسازی اولیه همیشه آن مقدار را خواهد دید.

**اگر هرگز لینتر را سرکوب نکنید، با مقادیر قدیمی مواجه نخواهید شد.**

با `useEffectEvent`، نیازی به "دروغ گفتن" به لینتر نیست و کد به دلخواه شما کار می کند:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  const onMove = useEffectEvent(e => {
    if (canMove) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
  });

  useEffect(() => {
    window.addEventListener('pointermove', onMove);
    return () => window.removeEventListener('pointermove', onMove);
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

This doesn't mean that `useEffectEvent` is *always* the correct solution. You should only apply it to the lines of code that you don't want to be reactive. In the above sandbox, you didn't want the Effect's code to be reactive with regards to `canMove`. That's why it made sense to extract an Effect Event.

Read [Removing Effect Dependencies](/learn/removing-effect-dependencies) for other correct alternatives to suppressing the linter.
////////////////
این به این معنی نیست که `useEffectEvent` همیشه راه حل درستی است. شما باید فقط آن را برای خطوط کدی که نمی خواهید واکنشی باشند، اعمال کنید. در مثال بالا، شما نمی خواستید کد Effect با توجه به `canMove` واکنشی باشد. به همین دلیل، استخراج یک رویداد Effect منطقی بود.

برای راه حل های درست دیگر برای سرکوب کردن لینتر، [Removing Effect Dependencies](/learn/removing-effect-dependencies) را بخوانید.

</DeepDive>

### Limitations of Effect Events {/*limitations-of-effect-events*/}

<Wip>

This section describes an **experimental API that has not yet been released** in a stable version of React.

</Wip>

Effect Events are very limited in how you can use them:

* **Only call them from inside Effects.**
* **Never pass them to other components or Hooks.**

///////////////
ترجمه: رویدادهای اثر بسیار محدود در استفاده از آنها هستند:

* **فقط از داخل اثرها فراخوانی کنید.**
* **هرگز آنها را به کامپوننت ها یا هوک های دیگر منتقل نکنید.**¹

For example, don't declare and pass an Effect Event like this:
به عنوان مثال، یک رویداد Effect مانند این را اعلان و ارسال نکنید:

```js {4-6,8}
function Timer() {
  const [count, setCount] = useState(0);

  const onTick = useEffectEvent(() => {
    setCount(count + 1);
  });

  useTimer(onTick, 1000); // 🔴 Avoid: Passing Effect Events

  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  useEffect(() => {
    const id = setInterval(() => {
      callback();
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay, callback]); // Need to specify "callback" in dependencies
}
```

Instead, always declare Effect Events directly next to the Effects that use them:
در عوض، همیشه رویدادهای افکت را مستقیماً در کنار افکت هایی که از آنها استفاده می کنند، اعلام کنید:

```js {10-12,16,21}
function Timer() {
  const [count, setCount] = useState(0);
  useTimer(() => {
    setCount(count + 1);
  }, 1000);
  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  const onTick = useEffectEvent(() => {
    callback();
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick(); // ✅ Good: Only called locally inside an Effect
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay]); // No need to specify "onTick" (an Effect Event) as a dependency
}
```

Effect Events are non-reactive "pieces" of your Effect code. They should be next to the Effect using them.
/////////////////////
ترجمه: "رویدادهای اثر"، قطعاتی غیر واکنشی از کد افکت شما هستند. آنها باید در کنار افکت با استفاده از "رویدادهای اثر" باشند¹.

<Recap>

- Event handlers run in response to specific interactions.
- Effects run whenever synchronization is needed.
- Logic inside event handlers is not reactive.
- Logic inside Effects is reactive.
- You can move non-reactive logic from Effects into Effect Events.
- Only call Effect Events from inside Effects.
- Don't pass Effect Events to other components or Hooks.

/////////////////////
ترجمه: - رویدادهای اثر در پاسخ به تعاملات خاصی اجرا می‌شوند.
- افکت‌ها هر زمانی که همگام‌سازی لازم است، اجرا می‌شوند.
- منطق داخل رویدادهای تعاملی واکنشی نیست.
- منطق داخل افکت‌ها واکنشی است.
- می‌توانید منطق غیر واکنشی را از افکت‌ها به رویدادهای اثر منتقل کنید.
- فقط از داخل افکت‌ها رویدادهای اثر را فراخوانی کنید.
- رویدادهای اثر را به کامپوننت‌ها یا هوک‌های دیگر منتقل نکنید.¹

</Recap>

<Challenges>

#### Fix a variable that doesn't update {/*fix-a-variable-that-doesnt-update*/}

This `Timer` component keeps a `count` state variable which increases every second. The value by which it's increasing is stored in the `increment` state variable. You can control the `increment` variable with the plus and minus buttons.

However, no matter how many times you click the plus button, the counter is still incremented by one every second. What's wrong with this code? Why is `increment` always equal to `1` inside the Effect's code? Find the mistake and fix it.

<Hint>

To fix this code, it's enough to follow the rules.

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Every second, increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

```css
button { margin: 10px; }
```

</Sandpack>

<Solution>

As usual, when you're looking for bugs in Effects, start by searching for linter suppressions.

If you remove the suppression comment, React will tell you that this Effect's code depends on `increment`, but you "lied" to React by claiming that this Effect does not depend on any reactive values (`[]`). Add `increment` to the dependency array:
///////////////
ترجمه: همانطور که معمول است، هنگامی که به دنبال باگ‌ها در افکت‌ها هستید، با جستجوی سرکوب کننده لینتر شروع کنید.

اگر شرایط سرکوب را بردارید، React به شما خواهد گفت که کد این افکت به `increment` وابسته است، اما شما با ادعای اینکه این افکت به هیچ مقدار واکنشی (`[]`) وابسته نیست، به React دروغ گفته‌اید. `increment` را به آرایه وابستگی اضافه کنید:²

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
  }, [increment]);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Every second, increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

```css
button { margin: 10px; }
```

</Sandpack>

Now, when `increment` changes, React will re-synchronize your Effect, which will restart the interval.
///////////////
حالا، هنگامی که `increment` تغییر می‌کند، React افکت شما را مجدداً همگام می‌کند و باعث شروع دوباره فاصله زمانی می‌شود¹.

</Solution>

#### Fix a freezing counter {/*fix-a-freezing-counter*/}

This `Timer` component keeps a `count` state variable which increases every second. The value by which it's increasing is stored in the `increment` state variable, which you can control it with the plus and minus buttons. For example, try pressing the plus button nine times, and notice that the `count` now increases each second by ten rather than by one.

There is a small issue with this user interface. You might notice that if you keep pressing the plus or minus buttons faster than once per second, the timer itself seems to pause. It only resumes after a second passes since the last time you've pressed either button. Find why this is happening, and fix the issue so that the timer ticks on *every* second without interruptions.

/////////////////
این کامپوننت `Timer` یک متغیر وضعیت `count` را نگه می‌دارد که هر ثانیه افزایش می‌یابد. مقداری که افزایش می‌یابد در متغیر وضعیت `increment` ذخیره شده است که می‌توانید با دکمه‌های پلاس و منهایی که در دسترس هستند، آن را کنترل کنید. به عنوان مثال، تلاش کنید نه بار دکمه پلاس را فشار دهید و توجه کنید که `count` هر ثانیه به جای یک، ده واحد افزایش می‌یابد.

یک مشکل کوچک در این رابط کاربری وجود دارد. ممکن است متوجه شوید که اگر دکمه‌های پلاس یا منهایی را سریع‌تر از یک بار در ثانیه فشار دهید، تایمر به نظر می‌رسد متوقف می‌شود. فقط پس از گذشت یک ثانیه از زمانی که آخرین بار دکمه‌ای را فشار داده‌اید، ادامه می‌یابد. دلیل این اتفاق را پیدا کنید و مشکل را برطرف کنید تا تایمر بدون وقفه هر ثانیه برای شما تیک بزند.⁵

<Hint>

It seems like the Effect which sets up the timer "reacts" to the `increment` value. Does the line that uses the current `increment` value in order to call `setCount` really need to be reactive?
/////////
ترجمه: به نظر می‌رسد که افکتی که تایمر را راه‌اندازی می‌کند، به مقدار `increment` واکنش نشان می‌دهد. آیا خطی که برای فراخوانی `setCount` از مقدار فعلی `increment` استفاده می‌کند، واکنشی باید داشته باشد؟

</Hint>

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
  }, [increment]);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Every second, increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

```css
button { margin: 10px; }
```

</Sandpack>

<Solution>

The issue is that the code inside the Effect uses the `increment` state variable. Since it's a dependency of your Effect, every change to `increment` causes the Effect to re-synchronize, which causes the interval to clear. If you keep clearing the interval every time before it has a chance to fire, it will appear as if the timer has stalled.

To solve the issue, extract an `onTick` Effect Event from the Effect:
//////////////
ترجمه: مشکل این است که کد داخل افکت از متغیر وضعیت `increment` استفاده می‌کند. از آنجایی که این یک وابستگی افکت شماست، هر تغییر در `increment` باعث می‌شود که افکت همگام‌سازی شود، که باعث پاک شدن فاصله زمانی می‌شود. اگر هر بار قبل از اینکه فاصله زمانی فعال شود، آن را پاک کنید، به نظر می‌رسد که تایمر متوقف شده است.

برای حل مشکل، یک "رویداد اثر" با نام `onTick` را از افکت استخراج کنید:¹

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick();
    }, 1000);
    return () => {
      clearInterval(id);
    };
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Every second, increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```


```css
button { margin: 10px; }
```

</Sandpack>

Since `onTick` is an Effect Event, the code inside it isn't reactive. The change to `increment` does not trigger any Effects.
//////////
ترجمه: از آنجایی که `onTick` یک "رویداد اثر" است، کد داخل آن واکنشی نشان نمی‌دهد. تغییر در `increment` هیچ اثری را فعال نمی‌کند.¹

</Solution>

#### Fix a non-adjustable delay {/*fix-a-non-adjustable-delay*/}

In this example, you can customize the interval delay. It's stored in a `delay` state variable which is updated by two buttons. However, even if you press the "plus 100 ms" button until the `delay` is 1000 milliseconds (that is, a second), you'll notice that the timer still increments very fast (every 100 ms). It's as if your changes to the `delay` are ignored. Find and fix the bug.
//////////////
ترجمه: در این مثال، می‌توانید تأخیر فاصله زمانی را سفارشی کنید. آن را در یک متغیر وضعیت به نام `delay` ذخیره می‌کنید که توسط دو دکمه به‌روزرسانی می‌شود. با این حال، حتی اگر دکمه "plus 100 ms" را تا زمانی که `delay` 1000 میلی‌ثانیه (یعنی یک ثانیه) باشد، فشار دهید، متوجه خواهید شد که تایمر همچنان بسیار سریع افزایش می‌یابد (هر 100 میلی‌ثانیه). به نظر می‌رسد که تغییرات شما در `delay` نادیده گرفته شده‌اند. باگ را پیدا کنید و آن را برطرف کنید.⁴

<Hint>

Code inside Effect Events is not reactive. Are there cases in which you would _want_ the `setInterval` call to re-run?
////////
ترجمه: کد داخل "رویدادهای اثر" واکنشی نشان نمی‌دهد. آیا مواردی وجود دارد که شما _می‌خواهید_ فراخوانی `setInterval` دوباره اجرا شود؟¹

</Hint>

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);
  const [delay, setDelay] = useState(100);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  });

  const onMount = useEffectEvent(() => {
    return setInterval(() => {
      onTick();
    }, delay);
  });

  useEffect(() => {
    const id = onMount();
    return () => {
      clearInterval(id);
    }
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
      <p>
        Increment delay:
        <button disabled={delay === 100} onClick={() => {
          setDelay(d => d - 100);
        }}>–100 ms</button>
        <b>{delay} ms</b>
        <button onClick={() => {
          setDelay(d => d + 100);
        }}>+100 ms</button>
      </p>
    </>
  );
}
```


```css
button { margin: 10px; }
```

</Sandpack>

<Solution>

The problem with the above example is that it extracted an Effect Event called `onMount` without considering what the code should actually be doing. You should only extract Effect Events for a specific reason: when you want to make a part of your code non-reactive. However, the `setInterval` call *should* be reactive with respect to the `delay` state variable. If the `delay` changes, you want to set up the interval from scratch! To fix this code, pull all the reactive code back inside the Effect:
/////////////
مشکل با مثال بالا این است که یک "رویداد اثر" به نام `onMount` استخراج شده است، بدون در نظر گرفتن اینکه کد واقعاً چه کاری باید انجام دهد. شما فقط باید "رویدادهای اثر" را به دلیل خاصیت خاصی استخراج کنید: زمانی که می‌خواهید یک بخش از کد خود را غیر واکنشی کنید. با این حال، فراخوانی `setInterval` با توجه به متغیر وضعیت `delay` باید واکنشی باشد. اگر `delay` تغییر کند، می‌خواهید فاصله زمانی را از ابتدا تنظیم کنید! برای رفع این کد، کل کد واکنشی را دوباره داخل افکت بیاورید:¹

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);
  const [delay, setDelay] = useState(100);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick();
    }, delay);
    return () => {
      clearInterval(id);
    }
  }, [delay]);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
      <p>
        Increment delay:
        <button disabled={delay === 100} onClick={() => {
          setDelay(d => d - 100);
        }}>–100 ms</button>
        <b>{delay} ms</b>
        <button onClick={() => {
          setDelay(d => d + 100);
        }}>+100 ms</button>
      </p>
    </>
  );
}
```

```css
button { margin: 10px; }
```

</Sandpack>

In general, you should be suspicious of functions like `onMount` that focus on the *timing* rather than the *purpose* of a piece of code. It may feel "more descriptive" at first but it obscures your intent. As a rule of thumb, Effect Events should correspond to something that happens from the *user's* perspective. For example, `onMessage`, `onTick`, `onVisit`, or `onConnected` are good Effect Event names. Code inside them would likely not need to be reactive. On the other hand, `onMount`, `onUpdate`, `onUnmount`, or `onAfterRender` are so generic that it's easy to accidentally put code that *should* be reactive into them. This is why you should name your Effect Events after *what the user thinks has happened,* not when some code happened to run.
///////////////
بطور کلی، باید از توابعی مانند `onMount` که بر روی *زمان* به جای *هدف* یک قطعه کد تمرکز دارند، مشکوک باشید. در ابتدا ممکن است احساس "توصیفات بیشتر" شود، اما این احساس هدف شما را مبهم می‌کند. به عنوان یک قاعده کلی، "رویدادهای اثر" باید با چیزی که از دیدگاه *کاربر* اتفاق می‌افتد، مطابقت داشته باشند. به عنوان مثال، `onMessage`، `onTick`، `onVisit` یا `onConnected` نام‌های خوبی برای "رویدادهای اثر" هستند. کد داخل آنها احتمالاً نیازی به واکنش نشان نمی‌دهد. از سوی دیگر، `onMount`، `onUpdate`، `onUnmount` یا `onAfterRender` به گونه‌ای عمومی هستند که آسان است که به طور تصادفی کدی را که *باید* واکنشی باشد، در آن قرار دهید. به همین دلیل باید "رویدادهای اثر" خود را بر اساس *آنچه کاربر فکر می‌کند که اتفاق افتاده است* نامگذاری کنید، نه زمانی که بعضی از کد اجرا شده است.¹

</Solution>

#### Fix a delayed notification {/*fix-a-delayed-notification*/}

When you join a chat room, this component shows a notification. However, it doesn't show the notification immediately. Instead, the notification is artificially delayed by two seconds so that the user has a chance to look around the UI.

This almost works, but there is a bug. Try changing the dropdown from "general" to "travel" and then to "music" very quickly. If you do it fast enough, you will see two notifications (as expected!) but they will *both* say "Welcome to music".

Fix it so that when you switch from "general" to "travel" and then to "music" very quickly, you see two notifications, the first one being "Welcome to travel" and the second one being "Welcome to music". (For an additional challenge, assuming you've *already* made the notifications show the correct rooms, change the code so that only the latter notification is displayed.)
//////////////////
وقتی به یک اتاق چت می‌پیوندید، این کامپوننت یک اعلان نشان می‌دهد. با این حال، این اعلان به صورت فوری نشان داده نمی‌شود. به جای آن، اعلان به صورت مصنوعی به مدت دو ثانیه تأخیر داده می‌شود تا کاربر فرصتی برای نگاه کردن به رابط کاربری داشته باشد.

این تقریباً کار می‌کند، اما یک باگ وجود دارد. سعی کنید در سریعی از "عمومی" به "سفر" و سپس به "موسیقی" تغییر دهید. اگر به اندازه کافی سریع انجام دهید، دو اعلان (همانطور که انتظار می‌رود!) را خواهید دید، اما هر دوی آنها "به موسیقی خوش آمدید" خواهند گفت.

برای رفع مشکل، آن را به گونه‌ای تغییر دهید که هنگامی که از "عمومی" به "سفر" و سپس به "موسیقی" بسیار سریع تغییر می‌دهید، دو اعلان را مشاهده کنید، اولین آنها "به سفر خوش آمدید" و دومین آنها "به موسیقی خوش آمدید" باشد. (برای چالش بیشتر، با فرض اینکه شما *قبلاً* اعلان‌ها را برای اتاق‌های درست نشان داده‌اید، کد را به گونه‌ای تغییر دهید که فقط آخرین اعلان نمایش داده شود.)¹

<Hint>

Your Effect knows which room it connected to. Is there any information that you might want to pass to your Effect Event?
/////////
افکت شما می‌داند به کدام اتاق متصل شده است. آیا اطلاعاتی وجود دارد که ممکن است بخواهید به "رویداد اثر" خود منتقل کنید؟¹

</Hint>

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Welcome to ' + roomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      setTimeout(() => {
        onConnected();
      }, 2000);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

```js notifications.js hidden
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label { display: block; margin-top: 10px; }
```

</Sandpack>

<Solution>

Inside your Effect Event, `roomId` is the value *at the time Effect Event was called.*

Your Effect Event is called with a two second delay. If you're quickly switching from the travel to the music room, by the time the travel room's notification shows, `roomId` is already `"music"`. This is why both notifications say "Welcome to music".

To fix the issue, instead of reading the *latest* `roomId` inside the Effect Event, make it a parameter of your Effect Event, like `connectedRoomId` below. Then pass `roomId` from your Effect by calling `onConnected(roomId)`:
//////////////
در داخل "رویداد اثر" شما، `roomId` مقداری است *در زمانی که رویداد اثر فراخوانی شده است*.

"رویداد اثر" شما با تأخیر دو ثانیه فراخوانی می‌شود. اگر شما به سرعت از اتاق سفر به اتاق موسیقی تغییر دهید، در زمانی که اعلان اتاق سفر نمایش داده می‌شود، `roomId` قبلاً `"music"` شده است. به همین دلیل هر دو اعلان "به موسیقی خوش آمدید" را می‌بینید.

برای رفع مشکل، به جای خواندن `roomId` *آخرین* در داخل "رویداد اثر"، آن را به عنوان پارامتری از "رویداد اثر" خود بردارید، مانند `connectedRoomId` در زیر. سپس با فراخوانی `onConnected(roomId)`، `roomId` را از افکت خود عبور دهید:¹

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(connectedRoomId => {
    showNotification('Welcome to ' + connectedRoomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      setTimeout(() => {
        onConnected(roomId);
      }, 2000);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

```js notifications.js hidden
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label { display: block; margin-top: 10px; }
```

</Sandpack>

The Effect that had `roomId` set to `"travel"` (so it connected to the `"travel"` room) will show the notification for `"travel"`. The Effect that had `roomId` set to `"music"` (so it connected to the `"music"` room) will show the notification for `"music"`. In other words, `connectedRoomId` comes from your Effect (which is reactive), while `theme` always uses the latest value.

To solve the additional challenge, save the notification timeout ID and clear it in the cleanup function of your Effect:
////////////////
"افکت" که `roomId` را به `"travel"` تنظیم کرده بود (بنابراین به اتاق `"travel"` متصل شده بود) اعلان را برای `"travel"` نشان خواهد داد. "افکت" که `roomId` را به `"music"` تنظیم کرده بود (بنابراین به اتاق `"music"` متصل شده بود) اعلان را برای `"music"` نشان خواهد داد. به عبارت دیگر، `connectedRoomId` از "افکت" شما (که واکنشی است) می‌آید، در حالی که `theme` همیشه از آخرین مقدار استفاده می‌کند.

برای حل چالش اضافی، شناسه توقف اعلان را ذخیره کرده و آن را در تابع پاک‌سازی "افکت" خود پاک کنید:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(connectedRoomId => {
    showNotification('Welcome to ' + connectedRoomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    let notificationTimeoutId;
    connection.on('connected', () => {
      notificationTimeoutId = setTimeout(() => {
        onConnected(roomId);
      }, 2000);
    });
    connection.connect();
    return () => {
      connection.disconnect();
      if (notificationTimeoutId !== undefined) {
        clearTimeout(notificationTimeoutId);
      }
    };
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

```js notifications.js hidden
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label { display: block; margin-top: 10px; }
```

</Sandpack>

This ensures that already scheduled (but not yet displayed) notifications get cancelled when you change rooms.
//////////
این اطمینان داده می‌شود که اعلان‌های قبلاً برنامه‌ریزی شده (اما هنوز نشان داده نشده) هنگام تغییر اتاق‌ها لغو می‌شوند.¹

</Solution>

</Challenges>
