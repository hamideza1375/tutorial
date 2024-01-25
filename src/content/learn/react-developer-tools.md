---
title: React Developer Tools
---

<Intro>

Use React Developer Tools to inspect React [components](/learn/your-first-component), edit [props](/learn/passing-props-to-a-component) and [state](/learn/state-a-components-memory), and identify performance problems.

///////////////

“از ابزار React Developer Tools برای بازرسی کامپوننت‌های React، ویرایش props و state و شناسایی مشکلات عملکرد استفاده کنید”

</Intro>

<YouWillLearn>

* نحوه نصب React Developer Tools

</YouWillLearn>

## Browser extension {/*browser-extension*/}

The easiest way to debug websites built with React is to install the React Developer Tools browser extension. It is available for several popular browsers:

////////////

“راه ساده‌ترین برای دیباگ کردن وب‌سایت‌های ساخته شده با React، نصب افزونه مرورگر React Developer Tools است. این افزونه برای چندین مرورگر محبوب در دسترس است”

* [Install for **Chrome**](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
* [Install for **Firefox**](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)
* [Install for **Edge**](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)

Now, if you visit a website **built with React,** you will see the _Components_ and _Profiler_ panels.

///////////////////

اگر به یک وب سایت ساخته شده با React مراجعه کنید، پنل‌های Components و Profiler را خواهید دید.

![React Developer Tools extension](/images/docs/react-devtools-extension.png)

### Safari and other browsers {/*safari-and-other-browsers*/}
For other browsers (for example, Safari), install the [`react-devtools`](https://www.npmjs.com/package/react-devtools) npm package:

//////////////
برای مرورگرهای دیگر (مانند Safari)، بسته npm react-devtools را نصب کنید.
```bash
# Yarn
yarn global add react-devtools

# Npm
npm install -g react-devtools
```

بعد از این، ابزارهای توسعه را از ترمینال باز کنید.

```bash
react-devtools
```

Then connect your website by adding the following `<script>` tag to the beginning of your website's `<head>`:

//////////////
سپس با اضافه کردن برچسب `<script>` زیر به ابتدای `<head>` وب سایت خود، آن را به ابزارهای توسعه متصل کنید:


```html {3}
<html>
  <head>
    <script src="http://localhost:8097"></script>
```

Reload your website in the browser now to view it in developer tools.

////////
برای مشاهده وب سایت خود در ابزارهای توسعه، آن را در مرورگر خود بازنشانی کنید.

![React Developer Tools standalone](/images/docs/react-devtools-standalone.png)

## Mobile (React Native) {/*mobile-react-native*/}
React Developer Tools can be used to inspect apps built with [React Native](https://reactnative.dev/) as well.

The easiest way to use React Developer Tools is to install it globally:

/////////////
React Developer Tools را می توان برای بررسی برنامه های ساخته شده با React Native نیز استفاده کرد. ساده ترین راه برای استفاده از React Developer Tools نصب آن به صورت سراسری است:
```bash
# Yarn
yarn global add react-devtools

# Npm
npm install -g react-devtools
```

ابزارهای توسعه را از ترمینال باز کنید.

```bash
react-devtools
```

It should connect to any local React Native app that's running.
//////////////
باید به هر برنامه محلی React Native که در حال اجرا است متصل شود.

> Try reloading the app if developer tools doesn't connect after a few seconds.
////////
> اگر ابزارهای توسعه دهنده پس از چند ثانیه وصل نشدند، برنامه را دوباره بارگیری کنید.

[Learn more about debugging React Native.](https://reactnative.dev/docs/debugging)
