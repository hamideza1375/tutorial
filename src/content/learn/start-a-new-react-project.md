---
title: Start a New React Project
---

<Intro>

یک ویرایشگر به درستی پیکربندی شده می‌تواند کد را برای خواندن واضح‌تر و نوشتن را سریع‌تر کند. حتی می تواند به شما کمک کند هنگام نوشتن اشکالات را پیدا کنید! اگر این اولین باری است که یک ویرایشگر راه اندازی می کنید یا به دنبال تنظیم ویرایشگر فعلی خود هستید، چند توصیه داریم.

</Intro>

<Note>

**برای توسعه محلی باید Node.js را نصب کنید. شما همچنین می توانید انتخاب کنید که از Node.js در تولید استفاده کنید، اما مجبور نیستید. بسیاری از فریم ورک های React از صادرات به پوشه ایستا HTML/CSS/JS پشتیبانی می کنند. [Node.js](https://nodejs.org/en/) 

</Note>

## فریمورک‌های React درجه حرفه‌ای تولیدی {/*production-grade-react-frameworks*/}

### Next.js {/*nextjs*/}

**[Next.js](https://nextjs.org/) Next.js یک چارچوب React تمام پشته است. همه کاره است و به شما امکان می دهد برنامه های React را با هر اندازه ای ایجاد کنید - از یک وبلاگ عمدتاً ثابت گرفته تا یک برنامه پویا پیچیده. برای ایجاد یک پروژه Next.js جدید، در ترمینال خود اجرا کنید:

<TerminalBlock>
npx create-next-app@latest
</TerminalBlock>

اگر با Next.js تازه کار هستید، آموزش Next.js را بررسی کنید. [Next.js tutorial.](https://nextjs.org/learn/foundations/about-nextjs)

Next.js is maintained by [Vercel](https://vercel.com/). You can [deploy a Next.js app](https://nextjs.org/docs/app/building-your-application/deploying) to any Node.js or serverless hosting, or to your own server. Next.js also supports a [static export](https://nextjs.org/docs/pages/building-your-application/deploying/static-exports) which doesn't require a server.

Next.js توسط Vercel نگهداری می شود. می‌توانید یک برنامه Next.js را در هر Node.js یا میزبانی بدون سرور یا سرور خود مستقر کنید. Next.js همچنین از یک صادرات ثابت که به سرور نیاز ندارد پشتیبانی می کند.

### Remix {/*remix*/}

**[Remix](https://remix.run/) یک چارچوب React تمام پشته با مسیریابی تودرتو است. این به شما امکان می دهد برنامه خود را به بخش های تو در تو تقسیم کنید که می توانند داده ها را به صورت موازی بارگیری کنند و در پاسخ به اقدامات کاربر به روز شوند. برای ایجاد یک پروژه Remix جدید، اجرا کنید:
<TerminalBlock>
npx create-remix
</TerminalBlock>

اگر تازه وارد Remix هستید، آموزش وبلاگ Remix  [blog tutorial](https://remix.run/docs/en/main/tutorials/blog) (short)(کوتاه) و آموزش برنامه (طولانی) [app tutorial](https://remix.run/docs/en/main/tutorials/jokes) (long). را بررسی کنید.

ریمیکس توسط Shopify [Shopify](https://www.shopify.com/) نگهداری می شود.[pick your deployment target](https://remix.run/docs/en/main/guides/deployment) هنگامی که یک پروژه Remix ایجاد می کنید، باید هدف استقرار خود را انتخاب کنید. می توانید با استفاده از یک آداپتور یا نوشتن یک آداپتور [adapter](https://remix.run/docs/en/main/other-api/adapter)، یک برنامه Remix را در هر Node.js یا میزبانی بدون  سرور مستقر کنید.

### Gatsby {/*gatsby*/}

**[Gatsby](https://www.gatsbyjs.com/) Gatsby یک چارچوب React برای وب‌سایت‌های سریع با CMS است. اکوسیستم پلاگین‌های غنی و لایه داده GraphQL آن، ادغام محتوا، API و خدمات را در یک وب‌سایت ساده می‌کند. برای ایجاد یک پروژه جدید Gatsby، دستور زیر را اجرا کنید:


<TerminalBlock>
npx create-gatsby
</TerminalBlock>

If you're new to Gatsby, check out the [Gatsby tutorial.](https://www.gatsbyjs.com/docs/tutorial/)

Gatsby is maintained by [Netlify](https://www.netlify.com/). You can [deploy a fully static Gatsby site](https://www.gatsbyjs.com/docs/how-to/previews-deploys-hosting) to any static hosting. If you opt into using server-only features, make sure your hosting provider supports them for Gatsby.

اگر تازه وارد گتسبی هستید، آموزش گتسبی را بررسی کنید.

گتسبی توسط نتلیفای نگهداری می شود. شما می توانید یک سایت کاملاً ثابت گتسبی را در هر میزبانی ایستا مستقر کنید. اگر استفاده از ویژگی های فقط سرور را انتخاب می کنید، مطمئن شوید که ارائه دهنده هاست شما از آنها برای گتسبی پشتیبانی می کند.

### Expo (for native apps) {/*expo*/}

**[Expo](https://expo.dev/) یک چارچوب React است که به شما امکان می‌دهد برنامه‌های جهانی Android، iOS و وب را با رابط‌های کاربری واقعی بومی ایجاد کنید.** یک SDK برای [React Native] ارائه می‌کند (https: //reactnative.dev/) که استفاده از قسمت های اصلی را آسان تر می کند. برای ایجاد یک پروژه Expo جدید، اجرا کنید:

<TerminalBlock>
npx create-expo-app
</TerminalBlock>

اگر تازه وارد Expo هستید، [آموزش اکسپو] (https://docs.expo.dev/tutorial/introduction/) را بررسی کنید.

فریمورک expo توسط [Expo (شرکت)] (https://expo.dev/about) نگهداری می شود. ساخت اپلیکیشن با Expo رایگان است و می‌توانید بدون محدودیت آن‌ها را به فروشگاه‌های اپلیکیشن گوگل و اپل ارسال کنید. Expo علاوه بر این، خدمات ابری پولی را ارائه می دهد.

<DeepDive>

#### آیا می توانم از React بدون فریمورک استفاده کنم؟ {/*can-i-use-react-without-a-framework*/}

قطعاً می‌توانید بدون framework از React استفاده کنید - اینطوری [از React برای بخشی از صفحه خود استفاده کنید.](/learn/add-react-to-an-existing-project#using-react-for-a- بخشی از صفحه موجود شما) ** با این حال، اگر یک برنامه جدید یا یک سایت کاملاً با React ایجاد می کنید، توصیه می کنیم از یک framework استفاده کنید.**

Here's why.
متنی که انتخاب کردید، در مورد استفاده از React بدون فریم‌ورک است. این متن به شما توضیح می‌دهد که چرا استفاده از فریم‌ورک‌های React می‌تواند مواردی مانند routing، data fetching و سرعت برنامه را بهبود بخشد. همچنین به شما پیشنهاد می‌دهد که از فریم‌ورک‌های React استفاده کنید، زیرا این فریم‌ورک‌ها این مشکلات را به طور پیش‌فرض حل می‌کنند و نیازی به تغییرات اضافی از سوی شما ندارند. در ضمن، اگر تمایل دارید که ستاپ خودتان را بسازید، همچنان می‌توانید از React و React-DOM استفاده کرده و سایر ابزارهای مورد نیاز خود را به آن اضافه کنید.

اگر هنوز متقاعد نشده‌اید، یا برنامه شما دارای محدودیت‌های غیرعادی است که این چارچوب‌ها به خوبی اجرا نمی‌کنند و می‌خواهید تنظیمات سفارشی خود را اجرا کنید، نمی‌توانیم مانع شما شویم - این کار را انجام دهید! «react» و «react-dom» را از npm بگیرید، فرآیند ساخت سفارشی خود را با بسته‌ای مانند [Vite](https://vitejs.dev/) یا [Parcel] (https://parceljs.org/) راه‌اندازی کنید. و ابزارهای دیگری را در صورت نیاز برای مسیریابی، تولید استاتیک یا رندر سمت سرور و موارد دیگر اضافه کنید.

</DeepDive>

## فریمورک‌های پیشتاز React(Bleeding-edge) {/*bleeding-edge-react-frameworks*/}

همانطور که نحوه ادامه بهبود React را بررسی کردیم، متوجه شدیم که ادغام کردن React با چارچوب‌ها (مخصوصاً با فناوری‌های مسیریابی، بسته‌بندی و سرور) بزرگترین فرصت ما برای کمک به کاربران React برای ساخت برنامه‌های بهتر است. تیم Next.js موافقت کرده است که با ما در تحقیق، توسعه، ادغام و آزمایش ویژگی‌های React با یکپارچه‌سازی و آزمون ویژگی‌های جدید ری‌اکت بدون وابستگی به چارچوب مانند اجزای سرور ری‌اکت همکاری کند مانند [React Server Components.] (/blog/2023/03/22/react-labs-what) همکاری کند. -we-have-been-working-on-mars-2023#react-server-components)
 

این ویژگی‌ها هر روز به آماده شدن برای تولید نزدیک‌تر می‌شوند و ما با سایر توسعه‌دهندگان باندلر و فریم‌ورک در مورد یکپارچه‌سازی آن‌ها مذاکره کرده‌ایم. امید ما این است که در یک یا دو سال دیگر، تمامی فریمورک های فهرست شده در این صفحه از این ویژگی ها پشتیبانی کامل داشته باشند. (اگر نویسنده چارچوبی هستید که علاقه مند به همکاری با ما برای آزمایش این ویژگی ها هستید، لطفاً به ما اطلاع دهید!)

### Next.js (App Router) {/*nextjs-app-router*/}

**[Next.js's App Router](https://nextjs.org/docs) طراحی مجدد API های Next.js با هدف تحقق دیدگاه معماری تمام پشته تیم React است.** به شما امکان می دهد داده ها را به صورت ناهمزمان واکشی کنید. کامپوننت هایی که روی سرور یا حتی در حین ساخت اجرا می شوند.

رابط برنامه Next.js یک بازطراحی از API های Next.js است که هدف آن تحقق دادن دیدگاه معماری full-stack تیم React است. این به شما اجازه می دهد تا داده ها را در کامپوننت های ناهمزمان در سمت سرور یا حتی در زمان ساخت بگیرید.

Next.js توسط [Vercel] (https://vercel.com/) نگهداری می شود. می‌توانید در هر Node.js یا میزبانی بدون سرور، یا در سرور خودتان، [یک برنامه Next.js] (https://nextjs.org/docs/app/building-your-application/deploying) را اجرا کنید. Next.js همچنین از [Static export] (https://nextjs.org/docs/app/building-your-application/deploying/static-exports) پشتیبانی می‌کند که به سرور نیاز ندارد.


<DeepDive>

#### کدام ویژگی‌ها به دیدگاه معماری تمام‌پلتفرم(full-stack) تیم React اشاره دارند؟ {/*which-features-make-up-the-react-teams-full-stack-architecture-vision*/}

رابط باندلر اپ روتر Next.js به طور کامل مشخصات رسمی کامپوننت‌های سرور React را اجرا می‌کند. این امکان را به شما می‌دهد که کامپوننت‌های ساختاری، فقط سمت سرور، و تعاملی را در یک درخت React تنها ترکیب کنید.
به عنوان مثال، می‌توانید یک کامپوننت سروری React را به عنوان یک تابع ناهمزمان بنویسید که از پایگاه داده یا از یک فایل بخواند. سپس می‌توانید داده را از آن به کامپوننت‌های تعاملی خود منتقل کنید.

باندلر App Router Next.js [مشخصات React Server Components] رسمی را به طور کامل اجرا می کند (https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md). این به شما امکان می دهد اجزای زمان ساخت، فقط سرور و تعاملی را در یک درخت React ترکیب کنید.

برای مثال، می‌توانید یک کامپوننت React فقط سرور را به‌عنوان یک تابع «async» بنویسید که از یک پایگاه داده یا از یک فایل می‌خواند. سپس می توانید داده ها را از آن به اجزای تعاملی خود منتقل کنید:

```js
// This component runs *only* on the server (or during the build).
async function Talks({ confId }) {
  // 1. You're on the server, so you can talk to your data layer. API endpoint not required.
  const talks = await db.Talks.findAll({ confId });

  // 2. Add any amount of rendering logic. It won't make your JavaScript bundle larger.
  const videos = talks.map(talk => talk.video);

  // 3. Pass the data down to the components that will run in the browser.
  return <SearchableVideoList videos={videos} />;
}
```

Next.js's App Router also integrates [data fetching with Suspense](/blog/2022/03/29/react-v18#suspense-in-data-frameworks). This lets you specify a loading state (like a skeleton placeholder) for different parts of your user interface directly in your React tree:

///////////

App Router در Next.js، امکاناتی را برای مدیریت روت‌ها و نمایش صفحات در برنامه‌های React فراهم می‌کند . با استفاده از Suspense، می‌توانید بخش‌های مختلف رابط کاربری خود را به صورت مستقیم در درخت React خود مشخص کنید و برای آن‌ها حالت بارگذاری (مانند placeholder skeleton) را مشخص کنید . برای نمایش حالت بارگذاری، می‌توانید از فایل loading.js استفاده کنید که به شما کمک می‌کند تا Loading UI مناسبی با React Suspense ایجاد کنید. با این روش، می‌توانید حالت بارگذاری فوری را از سمت سرور نشان دهید در حالی که محتوای یک بخش از یک مسیر بارگذاری می‌شود. محتوای جدید به صورت خودکار جایگزین محتوای قبلی می‌شود.

```js
<Suspense fallback={<TalksLoading />}>
  <Talks confId={conf.id} />
</Suspense>
```

Server Components and Suspense are React features rather than Next.js features. However, adopting them at the framework level requires buy-in and non-trivial implementation work. At the moment, the Next.js App Router is the most complete implementation. The React team is working with bundler developers to make these features easier to implement in the next generation of frameworks.

///////////////

Server Components و Suspense ویژگی های React هستند و نه ویژگی های Next.js. با این حال، به کارگیری آنها در سطح فریم‌ورک، نیاز به خرید و پیاده‌سازی غیر معمول دارد. در حال حاضر، App Router در Next.js، کامل‌ترین پیاده‌سازی است. تیم React با توسعه دهندگان باندلرها همکاری می‌کند تا این ویژگی‌ها را در نسل بعدی فریم‌ورک‌ها، آسان‌تر قابل پیاده‌سازی کند.

</DeepDive>
