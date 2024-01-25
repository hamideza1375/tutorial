---
title: راه اندازی ویرایشگر
---

<Intro>

  در این بخش آموزش‌های مفیدی درباره ویرایشگر مناسب برای کدنویسی و رفع باگ‌ها آمده است. اگر برای اولین بار است که قصد دارید ویرایشگری را تنظیم کنید یا به دنبال بهبود ویرایشگر فعلی خود هستید، چند پیشنهاد داریم1

</Intro>

<YouWillLearn>

* محبوب ترین ویرایشگر ها و ide ها چه هستند
* چگونه کد خود را به صورت خودکار قالب بندی کنیم

</YouWillLearn>

## ویرایشگر شما {/*your-editor*/}

[VS Code] (https://code.visualstudio.com/) یکی از محبوب ترین ویرایشگرهایی است که امروزه استفاده می شود. این یک بازار بزرگ از برنامه های افزودنی دارد و به خوبی با سرویس های محبوبی مانند GitHub ادغام می شود. بسیاری از ویژگی های ذکر شده در زیر را می توان به عنوان افزونه به VS Code نیز اضافه کرد و آن را بسیار قابل تنظیم می کند!

سایر ویرایشگرهای متن محبوب مورد استفاده در جامعه React عبارتند از:

* [WebStorm] (https://www.jetbrains.com/webstorm/) یک محیط توسعه یکپارچه است که به طور خاص برای جاوا اسکریپت طراحی شده است.
* [Sublime Text](https://www.sublimetext.com/) دارای پشتیبانی از JSX و TypeScript، [برجسته کردن نحو] (https://stackoverflow.com/a/70960574/458193) و تکمیل خودکار داخلی است.
* [Vim](https://www.vim.org/) یک ویرایشگر متن با قابلیت تنظیم بسیار بالا است که برای ایجاد و تغییر هر نوع متنی بسیار کارآمد ساخته شده است. در اکثر سیستم های یونیکس و با Apple OS X به عنوان "vi" گنجانده شده است.

## ویژگی های ویرایشگر متن توصیه شده {/*recommended-text-editor-features*/}

بعضی از ویرایشگرها این ویژگی‌ها را به صورت پیش‌فرض دارند، اما برخی دیگر نیاز به نصب افزونه دارند. برای اطمینان از پشتیبانی ویرایشگر مورد نظر خود، بهتر است بررسی کنید که چه پشتیبانی‌هایی ارائه می‌دهد1.

### Linting {/*linting*/}

کد لینترها به شما کمک می‌کنند تا با نوشتن کد، مشکلات آن را پیدا کنید و آن‌ها را در ابتدای کار برطرف کنید. ESLint یک لینتر محبوب و منبع باز برای جاوااسکریپت است1.

* [ESLint را با پیکربندی توصیه شده برای React نصب کنید](https://www.npmjs.com/package/eslint-config-react-app) (مطمئن شوید که [Node نصب شده است!](https://nodejs.org/en/download/current/))
* [ شما باید افزونه‌ی ESLint را در VSCode نصب کنید. برای این کار، به بخش Extensions در VSCode بروید و ESLint را جستجو کنید. سپس بر روی گزینه‌ی Install کلیک کنید123.](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

**Make sure that you've enabled all the [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks) rules for your project.** They are essential and catch the most severe bugs early. The recommended [`eslint-config-react-app`](https://www.npmjs.com/package/eslint-config-react-app) preset already includes them.

////////////////////

**برای اطمینان از فعال بودن همه‌ی قوانین `eslint-plugin-react-hooks` در پروژه‌ی خود، می‌توانید از دستور زیر استفاده کنید:

```bash
npm install eslint-plugin-react-hooks --save-dev
```

**همچنین، برای استفاده از تمامی قوانین `eslint-plugin-react-hooks`، می‌توانید از پیش‌تنظیم شده `eslint-config-react-app` استفاده کنید که این قوانین را شامل می‌شود¹²³..

### قالب بندی {/*formatting*/}

The last thing you want to do when sharing your code with another contributor is get into an discussion about [tabs vs spaces](https://www.google.com/search?q=tabs+vs+spaces)! Fortunately, [Prettier](https://prettier.io/) will clean up your code by reformatting it to conform to preset, configurable rules. Run Prettier, and all your tabs will be converted to spaces—and your indentation, quotes, etc will also all be changed to conform to the configuration. In the ideal setup, Prettier will run when you save your file, quickly making these edits for you.

You can install the [Prettier extension in VSCode](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) by following these steps:

1. Launch VS Code
2. Use Quick Open (press Ctrl/Cmd+P)
3. Paste in `ext install esbenp.prettier-vscode`
4. Press Enter

//////////////////////

به نظر می‌رسد که شما در مورد نحوه‌ی استفاده از ابزار Prettier برای فرمت‌بندی کد خود صحبت می‌کنید. Prettier یک ابزار قدرتمند برای فرمت‌بندی کد است که با استفاده از قوانین پیش‌فرض و قابل تنظیم، کد شما را به صورتی فرمت‌بندی می‌کند که با آن قابل خواندن و تغییر دادن است. برای استفاده از Prettier در VSCode، شما باید ابتدا پلاگین Prettier را در VSCode نصب کنید. برای این کار، به بخش Extensions در VSCode بروید و Prettier را جستجو کنید. سپس بر روی گزینه‌ی Install کلیک کنید123.

برای تنظیمات Prettier، شما می‌توانید از یک فایل پیکربندی استفاده کنید. این فایل می‌تواند به صورت یکی از فرمت‌های JSON، YAML، JS، یا TOML باشد. در این فایل، شما می‌توانید تنظیمات مختلفی را برای Prettier تعریف کنید. برای مثال، می‌توانید تنظیماتی برای تعیین تعداد فاصله‌هایی که باید بین دو کلمه قرار بگیرد، تعیین کنید12.

در این متن، شما همچنین به توصیه‌ی استفاده از eslint-plugin-react-hooks برای گرفتن اطمینان از فعال بودن همه‌ی قوانین آن در پروژه‌ی خود اشاره کرده‌اید. برای نصب این پلاگین، می‌توانید از دستور زیر استفاده کنید:

npm install eslint-plugin-react-hooks --save-dev

همچنین، برای استفاده از تمامی قوانین eslint-plugin-react-hooks، می‌توانید از پیش‌تنظیم شده eslint-config-react-app استفاده کنید که این قوانین را شامل می‌شود123.

1. VS Code را اجرا کنید
2. از Quick Open استفاده کنید (Ctrl/Cmd+P را فشار دهید)
3. در «ext install esbenp.prettier-vscode» جای‌گذاری کنید
4. Enter را فشار دهید


#### قالب بندی در ذخیره {/*formatting-on-save*/}

در حالت ایده آل، باید کد خود را در هر ذخیره فرمت کنید. VS Code تنظیماتی برای این کار دارد!

1. در VS Code، "CTRL/CMD + SHIFT + P" را فشار دهید.
2. "settings" را تایپ کنید
3. Enter را بزنید
4. در نوار جستجو، "format on save" را تایپ کنید.
5. مطمئن شوید که گزینه "format on save" تیک خورده است!

> If your ESLint preset has formatting rules, they may conflict with Prettier. We recommend disabling all formatting rules in your ESLint preset using [`eslint-config-prettier`](https://github.com/prettier/eslint-config-prettier) so that ESLint is *only* used for catching logical mistakes. If you want to enforce that files are formatted before a pull request is merged, use [`prettier --check`](https://prettier.io/docs/en/cli.html#--check) for your continuous integration.

////////////////////

<YouWillLearn>

اگر از پیش تنظیم شده ESLint شما دارای قوانین قالب بندی باشد، ممکن است با Prettier در تضاد باشند. توصیه می‌کنیم با استفاده از eslint-config-prettier تمام قوانین قالب‌بندی را در پیش‌تنظیم ESLint خود غیرفعال کنید تا ESLint فقط برای تشخیص اشتباهات منطقی استفاده شود. اگر می‌خواهید فرمت‌بندی فایل‌ها را قبل از ادغام درخواست pull انجام دهید، از چک بهتر - برای یکپارچگی مداوم خود استفاده کنید.

</YouWillLearn>