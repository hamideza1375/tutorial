---
title: Scaling Up with Reducer and Context(افزایش مقیاس با Reducer و Context)
---

<Intro>

Reducers let you consolidate a component's state update logic. Context lets you pass information deep down to other components. You can combine reducers and context together to manage state of a complex screen.
//////////////////
به عبارت دیگر، با استفاده از Reducer می‌توانید منطق به‌روزرسانی state یک کامپوننت را تلفیق کنید. با استفاده از Context می‌توانید اطلاعات را به کامپوننت‌های دیگر در درخت پایین‌تر منتقل کنید. شما می‌توانید Reducer و Context را با هم ترکیب کنید تا بتوانید state یک صفحه پیچیده را مدیریت کنید¹².

</Intro>

<YouWillLearn>

* How to combine a reducer with context
* How to avoid passing state and dispatch through props
* How to keep context and state logic in a separate file

//////////////
به عبارت دیگر، می‌توانید با استفاده از Reducer و Context، منطق به‌روزرسانی state یک کامپوننت را تلفیق کنید. همچنین، با استفاده از Context، می‌توانید اطلاعات را به کامپوننت‌های دیگر در درخت پایین‌تر منتقل کنید. شما می‌توانید Reducer و Context را با هم ترکیب کنید تا بتوانید state یک صفحه پیچیده را مدیریت کنید¹². همچنین، می‌توانید از روش‌های زیر استفاده کنید:
* چگونگی ترکیب Reducer با Context
* چگونگی جلوگیری از انتقال state و dispatch از طریق props
* چگونگی نگه‌داشتن منطق Context و state در یک فایل جداگانه

</YouWillLearn>

## Combining a reducer with context(ترکیب reducer با context) {/*combining-a-reducer-with-context*/}

In this example from [the introduction to reducers](/learn/extracting-state-logic-into-a-reducer), the state is managed by a reducer. The reducer function contains all of the state update logic and is declared at the bottom of this file:
/////////////////
به عبارت دیگر، در این مثال از [مقدمه‌ای بر Reducer‌ها](/learn/extracting-state-logic-into-a-reducer)، state توسط یک reducer مدیریت می‌شود. تابع reducer شامل تمام منطق به‌روزرسانی state است و در پایین این فایل اعلام شده است.

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId
    });
  }

  return (
    <>
      <h1>Day off in Kyoto</h1>
      <AddTask
        onAddTask={handleAddTask}
      />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js AddTask.js
import { useState } from 'react';

export default function AddTask({ onAddTask }) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        onAddTask(text);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  tasks,
  onChangeTask,
  onDeleteTask
}) {
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task
            task={task}
            onChange={onChangeTask}
            onDelete={onDeleteTask}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ task, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            onChange({
              ...task,
              text: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
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
        checked={task.done}
        onChange={e => {
          onChange({
            ...task,
            done: e.target.checked
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>
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

A reducer helps keep the event handlers short and concise. However, as your app grows, you might run into another difficulty. **Currently, the `tasks` state and the `dispatch` function are only available in the top-level `TaskApp` component.** To let other components read the list of tasks or change it, you have to explicitly [pass down](/learn/passing-props-to-a-component) the current state and the event handlers that change it as props.
/////////////////////
به عبارت دیگر، با استفاده از Reducer می‌توانید منطق به‌روزرسانی state یک کامپوننت را تلفیق کنید. با افزایش اندازه برنامه‌ی شما، ممکن است با مشکلات دیگری مواجه شوید. **در حال حاضر، state `tasks` و تابع `dispatch` فقط در کامپوننت `TaskApp` سطح بالا در دسترس هستند.** برای اینکه کامپوننت‌های دیگر بتوانند لیست تسک‌ها را بخوانند یا آن را تغییر دهند، باید به صورت صریح [props](/learn/passing-props-to-a-component) فعلی و event handlerهایی که آن را تغییر می‌دهند را به آن‌ها منتقل کنید.

For example, `TaskApp` passes a list of tasks and the event handlers to `TaskList`:
به عنوان مثال، `TaskApp` یک لیست از تسک‌ها و event handlerها را به `TaskList` منتقل می‌کند¹.

```js
<TaskList
  tasks={tasks}
  onChangeTask={handleChangeTask}
  onDeleteTask={handleDeleteTask}
/>
```

And `TaskList` passes the event handlers to `Task`:

```js
<Task
  task={task}
  onChange={onChangeTask}
  onDelete={onDeleteTask}
/>
```

In a small example like this, this works well, but if you have tens or hundreds of components in the middle, passing down all state and functions can be quite frustrating!
//////////////////
به عبارت دیگر، در یک مثال کوچک مانند این، این روش به خوبی کار می‌کند، اما اگر ده‌ها یا صد‌ها کامپوننت در میانه وجود داشته باشد، انتقال همه‌ی state و توابع می‌تواند بسیار سخت باشد¹².


This is why, as an alternative to passing them through props, you might want to put both the `tasks` state and the `dispatch` function [into context.](/learn/passing-data-deeply-with-context) **This way, any component below `TaskApp` in the tree can read the tasks and dispatch actions without the repetitive "prop drilling".**
/////////////////////
به عبارت دیگر، به جای انتقال state و توابع از طریق props، ممکن است بخواهید هر دوی state `tasks` و تابع `dispatch` را [در کانتکست قرار دهید](/learn/passing-data-deeply-with-context). **به این ترتیب، هر کامپوننتی که در درخت پایین‌تر از `TaskApp` قرار دارد، می‌تواند لیست تسک‌ها را بخواند و اقدامات را بدون نیاز به "prop drilling" تکراری، انجام دهد.**

Here is how you can combine a reducer with context:
به عبارت دیگر، اینجا نحوه‌ی ترکیب Reducer با Context را برای شما توضیح می‌دهیم¹².

1. **Create** the context.
2. **Put** state and dispatch into context.
3. **Use** context anywhere in the tree.

### Step 1: Create the context {/*step-1-create-the-context*/}

The `useReducer` Hook returns the current `tasks` and the `dispatch` function that lets you update them:
///////////////////
تابع `useReducer`، state فعلی `tasks` و تابع `dispatch` را که به شما اجازه می‌دهد آن‌ها را به‌روزرسانی کنید، برمی‌گرداند¹².


```js
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```

To pass them down the tree, you will [create](/learn/passing-data-deeply-with-context#step-2-use-the-context) two separate contexts:

- `TasksContext` provides the current list of tasks.
- `TasksDispatchContext` provides the function that lets components dispatch actions.

Export them from a separate file so that you can later import them from other files:
//////////////////////
به عبارت دیگر، برای انتقال آن‌ها در درخت، شما باید دو کانتکست جداگانه ایجاد کنید:

- `TasksContext` لیست فعلی تسک‌ها را فراهم می‌کند.
- `TasksDispatchContext` تابعی را فراهم می‌کند که به کامپوننت‌ها اجازه می‌دهد اقدامات را ارسال کنند.

آن‌ها را از یک فایل جداگانه صادر کنید تا بتوانید در آینده آن‌ها را از فایل‌های دیگر وارد کنید¹².

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId
    });
  }

  return (
    <>
      <h1>Day off in Kyoto</h1>
      <AddTask
        onAddTask={handleAddTask}
      />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js TasksContext.js active
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

```js AddTask.js
import { useState } from 'react';

export default function AddTask({ onAddTask }) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        onAddTask(text);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  tasks,
  onChangeTask,
  onDeleteTask
}) {
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task
            task={task}
            onChange={onChangeTask}
            onDelete={onDeleteTask}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ task, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            onChange({
              ...task,
              text: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
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
        checked={task.done}
        onChange={e => {
          onChange({
            ...task,
            done: e.target.checked
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>
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

Here, you're passing `null` as the default value to both contexts. The actual values will be provided by the `TaskApp` component.
//////////////////////////////////////
در اینجا، شما به عنوان مقدار پیش‌فرض به هر دو کانتکست `null` را ارسال کرده‌اید. مقادیر واقعی توسط کامپوننت `TaskApp` فراهم خواهند شد¹².


### Step 2: Put state and dispatch into context {/*step-2-put-state-and-dispatch-into-context*/}

Now you can import both contexts in your `TaskApp` component. Take the `tasks` and `dispatch` returned by `useReducer()` and [provide them](/learn/passing-data-deeply-with-context#step-3-provide-the-context) to the entire tree below:

////////////////
حالا می‌توانید هر دو کانتکست را در کامپوننت `TaskApp` خود وارد کنید. با استفاده از `useReducer()`، `tasks` و `dispatch` را برگردانده و آن‌ها را [به درخت پایین‌تر](/learn/passing-data-deeply-with-context#step-3-provide-the-context) ارائه دهید¹².

```js {4,7-8}
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
  // ...
  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        ...
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```

For now, you pass the information both via props and in context:
به عبارت دیگر، در حال حاضر، شما اطلاعات را هم از طریق props و هم در کانتکست انتقال می‌دهید¹².

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId
    });
  }

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        <h1>Day off in Kyoto</h1>
        <AddTask
          onAddTask={handleAddTask}
        />
        <TaskList
          tasks={tasks}
          onChangeTask={handleChangeTask}
          onDeleteTask={handleDeleteTask}
        />
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js TasksContext.js
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

```js AddTask.js
import { useState } from 'react';

export default function AddTask({ onAddTask }) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        onAddTask(text);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  tasks,
  onChangeTask,
  onDeleteTask
}) {
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task
            task={task}
            onChange={onChangeTask}
            onDelete={onDeleteTask}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ task, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            onChange({
              ...task,
              text: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
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
        checked={task.done}
        onChange={e => {
          onChange({
            ...task,
            done: e.target.checked
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>
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

In the next step, you will remove prop passing.
در مرحله بعدی، شما عبور prop را حذف خواهید کرد.

### Step 3: Use context anywhere in the tree {/*step-3-use-context-anywhere-in-the-tree*/}

Now you don't need to pass the list of tasks or the event handlers down the tree:
حالا شما نیازی به انتقال لیست تسک‌ها یا event handlerها از طریق props ندارید¹².

```js {4-5}
<TasksContext.Provider value={tasks}>
  <TasksDispatchContext.Provider value={dispatch}>
    <h1>Day off in Kyoto</h1>
    <AddTask />
    <TaskList />
  </TasksDispatchContext.Provider>
</TasksContext.Provider>
```

Instead, any component that needs the task list can read it from the `TaskContext`:
///////////////////////////
به عبارت دیگر، به جای انتقال لیست تسک‌ها یا event handlerها از طریق props، می‌توانید از کانتکست استفاده کنید. هر کامپوننتی که به لیست تسک‌ها نیاز دارد، می‌تواند آن را از `TaskContext` بخواند¹².

```js {2}
export default function TaskList() {
  const tasks = useContext(TasksContext);
  // ...
```

To update the task list, any component can read the `dispatch` function from context and call it:
////////////////
به عبارت دیگر، هر کامپوننتی که به لیست تسک‌ها نیاز دارد، می‌تواند تابع `dispatch` را از کانتکست بخواند و آن را فراخوانی کند¹².

```js {3,9-13}
export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useContext(TasksDispatchContext);
  // ...
  return (
    // ...
    <button onClick={() => {
      setText('');
      dispatch({
        type: 'added',
        id: nextId++,
        text: text,
      });
    }}>Add</button>
    // ...
```

**The `TaskApp` component does not pass any event handlers down, and the `TaskList` does not pass any event handlers to the `Task` component either.** Each component reads the context that it needs:
////////////////
به عبارت دیگر، هیچ کامپوننتی از `TaskApp` به پایین، event handlerی را انتقال نمی‌دهد و همچنین `TaskList` هم هیچ event handlerی را به `Task` منتقل نمی‌کند. هر کامپوننت، کانتکست مورد نیاز خود را می‌خواند¹².

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        <h1>Day off in Kyoto</h1>
        <AddTask />
        <TaskList />
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js TasksContext.js
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

```js AddTask.js
import { useState, useContext } from 'react';
import { TasksDispatchContext } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useContext(TasksDispatchContext);
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        dispatch({
          type: 'added',
          id: nextId++,
          text: text,
        }); 
      }}>Add</button>
    </>
  );
}

let nextId = 3;
```

```js TaskList.js active
import { useState, useContext } from 'react';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskList() {
  const tasks = useContext(TasksContext);
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useContext(TasksDispatchContext);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
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
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
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

**The state still "lives" in the top-level `TaskApp` component, managed with `useReducer`.** But its `tasks` and `dispatch` are now available to every component below in the tree by importing and using these contexts.
/////////////////////
به عبارت دیگر، **state هنوز در کامپوننت `TaskApp` سطح بالا "زندگی می‌کند" و با استفاده از `useReducer` مدیریت می‌شود.** اما `tasks` و `dispatch` آن‌ها اکنون با وارد کردن و استفاده از این کانتکست‌ها، برای هر کامپوننتی که در درخت پایین‌تر قرار دارد، در دسترس هستند¹².

## Moving all wiring into a single file {/*moving-all-wiring-into-a-single-file*/}

You don't have to do this, but you could further declutter the components by moving both reducer and context into a single file. Currently, `TasksContext.js` contains only two context declarations:
///////////////////
شما نیازی به انجام این کار ندارید، اما می‌توانید با انتقال هر دو Reducer و Context به یک فایل، کامپوننت‌ها را بیشتر تمیز کنید. در حال حاضر، `TasksContext.js` فقط دو اعلامیه کانتکست دارد¹².


```js
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

This file is about to get crowded! You'll move the reducer into that same file. Then you'll declare a new `TasksProvider` component in the same file. This component will tie all the pieces together:
//////////////
این فایل به زودی شلوغ خواهد شد! شما Reducer را به همان فایل منتقل خواهید کرد. سپس یک کامپوننت جدید به نام `TasksProvider` را در همان فایل اعلام خواهید کرد. این کامپوننت تمام قطعات را به هم متصل خواهد کرد¹².

1. It will manage the state with a reducer.
2. It will provide both contexts to components below.
3. It will [take `children` as a prop](/learn/passing-props-to-a-component#passing-jsx-as-children) so you can pass JSX to it.
////////////////////////////////
به عبارت دیگر، شما می‌توانید با استفاده از Reducer و Context، منطق به‌روزرسانی state یک کامپوننت را تلفیق کنید. با افزایش اندازه برنامه‌ی شما، ممکن است با مشکلات دیگری مواجه شوید. **در حال حاضر، state `tasks` و تابع `dispatch` فقط در کامپوننت `TaskApp` سطح بالا در دسترس هستند.** برای اینکه کامپوننت‌های دیگر بتوانند لیست تسک‌ها را بخوانند و آن را تغییر دهند، باید به صورت صریح [props](/learn/passing-props-to-a-component) فعلی و event handlerهایی که آن را تغییر می‌دهند را به آن‌ها منتقل کنید. به جای انتقال state و توابع از طریق props، ممکن است بخواهید هر دوی state `tasks` و تابع `dispatch` را [در کانتکست قرار دهید](/learn/passing-data-deeply-with-context). **به این ترتیب، هر کامپوننتی که در درخت پایین‌تر از `TaskApp` قرار دارد، می‌تواند لیست تسک‌ها را بخواند و اقدامات را بدون نیاز به "prop drilling" تکراری، انجام دهد.** برای ترکیب Reducer با Context، می‌توانید به شرح زیر عمل کنید:

1. کانتکست را ایجاد کنید.
2. state و dispatch را در کانتکست قرار دهید.
3. از کانتکست در هر نقطه‌ای از درخت استفاده کنید.

```js
export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```

**This removes all the complexity and wiring from your `TaskApp` component:**

///////////////
این کار تمام پیچیدگی‌ها و اتصالات را از کامپوننت `TaskApp` شما حذف می‌کند¹².

<Sandpack>

```js App.js
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksProvider } from './TasksContext.js';

export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}
```

```js TasksContext.js
import { createContext, useReducer } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js AddTask.js
import { useState, useContext } from 'react';
import { TasksDispatchContext } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useContext(TasksDispatchContext);
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        dispatch({
          type: 'added',
          id: nextId++,
          text: text,
        }); 
      }}>Add</button>
    </>
  );
}

let nextId = 3;
```

```js TaskList.js
import { useState, useContext } from 'react';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskList() {
  const tasks = useContext(TasksContext);
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useContext(TasksDispatchContext);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
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
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
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

You can also export functions that _use_ the context from `TasksContext.js`:
شما همچنین می‌توانید توابعی را که از کانتکست استفاده می‌کنند، از `TasksContext.js` صادر کنید¹².

```js
export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}
```

When a component needs to read context, it can do it through these functions:
وقتی که یک کامپوننت نیاز به خواندن کانتکست دارد، می‌تواند از توابع زیر استفاده کند:

```js
const tasks = useTasks();
const dispatch = useTasksDispatch();
```

This doesn't change the behavior in any way, but it lets you later split these contexts further or add some logic to these functions. **Now all of the context and reducer wiring is in `TasksContext.js`. This keeps the components clean and uncluttered, focused on what they display rather than where they get the data:**

////////////////////////
این متن انگلیسی که قسمتی از آموزش برنامه نویسی React است را به فارسی ترجمه می‌کنم: این کار به هیچ وجه رفتار را تغییر نمی‌دهد، اما به شما اجازه می‌دهد که در آینده این کانتکست‌ها را به بخش‌های کوچک‌تر تقسیم کنید یا برخی منطق را به این توابع اضافه کنید. **اکنون تمامی کانتکست و کابل‌های کاهنده در `TasksContext.js` قرار دارند. این باعث می‌شود که کامپوننت‌ها تمیز و بدون اشغال باشند و بر روی نمایش آنچه که نمایش داده می‌شود تمرکز کنند، نه آنچه که از کجا داده می‌گیرند:**.

<Sandpack>

```js App.js
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksProvider } from './TasksContext.js';

export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}
```

```js TasksContext.js
import { createContext, useContext, useReducer } from 'react';

const TasksContext = createContext(null);

const TasksDispatchContext = createContext(null);

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js AddTask.js
import { useState } from 'react';
import { useTasksDispatch } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useTasksDispatch();
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        dispatch({
          type: 'added',
          id: nextId++,
          text: text,
        }); 
      }}>Add</button>
    </>
  );
}

let nextId = 3;
```

```js TaskList.js active
import { useState } from 'react';
import { useTasks, useTasksDispatch } from './TasksContext.js';

export default function TaskList() {
  const tasks = useTasks();
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useTasksDispatch();
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
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
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
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

You can think of `TasksProvider` as a part of the screen that knows how to deal with tasks, `useTasks` as a way to read them, and `useTasksDispatch` as a way to update them from any component below in the tree.
/////////////////////////
می‌توانید `TasksProvider` را به عنوان بخشی از صفحه تصور کنید که نحوه کار با وظایف را می‌داند، `useTasks` را به عنوان یک راه برای خواندن آن‌ها و `useTasksDispatch` را به عنوان یک راه برای به‌روزرسانی آن‌ها از هر کامپوننتی در پایین درخت می‌توانید استفاده کنید.

<Note>

Functions like `useTasks` and `useTasksDispatch` are called *[Custom Hooks.](/learn/reusing-logic-with-custom-hooks)* Your function is considered a custom Hook if its name starts with `use`. This lets you use other Hooks, like `useContext`, inside it.
//////////////////////////
توابعی مانند `useTasks` و `useTasksDispatch` به عنوان *[Custom Hooks](/learn/reusing-logic-with-custom-hooks)* شناخته می‌شوند. تابع شما به عنوان یک Custom Hook در نظر گرفته می‌شود اگر نام آن با `use` شروع شود. این به شما اجازه می‌دهد که از دیگر Hooks مانند `useContext` درون آن استفاده کنید.

</Note>

As your app grows, you may have many context-reducer pairs like this. This is a powerful way to scale your app and [lift state up](/learn/sharing-state-between-components) without too much work whenever you want to access the data deep in the tree.
////////////////////////
هر چقدر که برنامه شما بزرگتر شود، ممکن است چندین جفت کانتکست-کاهنده مانند این داشته باشید. این روشی قدرتمند برای مقیاس‌پذیری برنامه شما و [انتقال وضعیت به بالا](/learn/sharing-state-between-components) بدون زیاده کاری هنگام دسترسی به داده‌های عمیق در درخت است.

<Recap>

- You can combine reducer with context to let any component read and update state above it.
- To provide state and the dispatch function to components below:
  1. Create two contexts (for state and for dispatch functions).
  2. Provide both contexts from the component that uses the reducer.
  3. Use either context from components that need to read them.
- You can further declutter the components by moving all wiring into one file.
  - You can export a component like `TasksProvider` that provides context.
  - You can also export custom Hooks like `useTasks` and `useTasksDispatch` to read it.
- You can have many context-reducer pairs like this in your app.

//////////////////////
می‌توانید کاهنده را با کانتکست ترکیب کنید تا هر کامپوننتی بتواند وضعیت را بالای خودش خوانده و به‌روزرسانی کند.

برای ارائه وضعیت و تابع کاهنده به کامپوننت‌های پایین‌تر:
  1. دو کانتکست (برای وضعیت و برای توابع کاهنده) ایجاد کنید.
  2. هر دو کانتکست را از کامپوننتی که از کاهنده استفاده می‌کند، ارائه دهید.
  3. از هر کانتکست در کامپوننت‌هایی که نیاز به خواندن آن‌ها دارند، استفاده کنید.

می‌توانید با جابجایی تمامی کابل‌ها به یک فایل، کامپوننت‌ها را بیشتر تمیز کنید.
  - می‌توانید یک کامپوننت مانند `TasksProvider` را که کانتکست را فراهم می‌کند، صادر کنید.
  - همچنین می‌توانید Custom Hooks مانند `useTasks` و `useTasksDispatch` را برای خواندن آن صادر کنید.

می‌توانید در برنامه خود چندین جفت کانتکست-کاهنده مانند این داشته باشید.

</Recap>

