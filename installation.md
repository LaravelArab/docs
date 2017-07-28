# التنصيب

- [التنصيب](#installation)
    - [متطلبات الخادم](#server-requirements)
    - [تنصيب لارفيل](#installing-laravel)
    - [الإعداد](#configuration)
- [إعداد خادم الويب](#web-server-configuration)
    - [عناوين انترنت جميلة](#pretty-urls)

<a name="installation"></a>
## التنصيب

> {video} هل انت متعلم بالمشاهدة ؟ لاراكاست يوفر  [مجانا,مقدمة شاملة للارافيل](https://laracasts.com/series/laravel-from-scratch-2017) للقادمين الجدد لاطار العمل. انها مكان عظيم لبدء رحلتك

<a name="server-requirements"></a>
### متطلبات الخادم

اطار العمل لارافيل لديه القليل من متطلبات النظام . طبعا , جميع هذه المتطلبات تصلح ل [لارافيل هومستيد](/docs/{{version}}/homestead) الآلة الإفتراضية, لذلك ينصح بشدة ان تستخدم هومستيد كبيئة تطوير لارفيل محلية خاصة بك.

ومع ذلك، إذا كنت لا تستخدم هومستيد، فستحتاج إلى التأكد من أن الخادم يتوافق مع المتطلبات التالية :

<div class="content-list" markdown="1">
- PHP >= 5.6.4
- OpenSSL PHP Extension
- PDO PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension
- XML PHP Extension
  </div>

<a name="installing-laravel"></a>
### تنصيب لارفيل

لارافيل يستخدم [كمبوزر](https://getcomposer.org)لإدارة تبعياته. لذلك,  قبل استخدام لارافيل, تأكد من أن لديك الملحن المثبتة على الجهاز الخاص بك

#### عن طريق مثبت لارافيل

أولا، قم بتحميل مثبت لارافيل باستخدام كمبوزر :

    composer global require "laravel/installer"
تأكد من وضع دليل`$HOME/.composer/vendor/bin` (أو الدليل المكافئ لنظام التشغيل الخاص بك) في $PATH يكون `لارافيل` التتنفيذي موجود في النظام الخاص بك

وبمجرد التنصيب، فإن الأمر `laravel new` سوف يقوم بعمل تثبيت لارافيل جديد في الدليل الذي قمت بتحديده. على سبيل المثال `laravel new blog` سوف يقوم بعمل دليل جديد باسم `blog` يحتوى تثبيت لارافيل جديد مع جميع تبعيات لارافيل مثبتة

    laravel new blog

#### بواسطة Composer Create-Project

بدلا من ذلك، يمكنك أيضا تثبيت لارافيل عن طريق تنفيذ `create-project` الأمر في  الطرفية :

    composer create-project --prefer-dist laravel/laravel blog

#### خادم التطوير المحلي

إذا كان لديك PHP مثبت محليا وكنت ترغب في استخدام PHP المدمج في خادم التطوير لخدمة التطبيق الخاص بك، يمكنك استخدام  `serve` أرتسن أمر . سيبدأ هذا الأمر خادم التطوير في `http://localhost:8000`

    php artisan serve

وبطبيعة الحال ،  تتوفر خيارات التطوير المحلية الأكثر قوة عبر  [هومستيد](/docs/{{version}}/homestead)  و [فاليت](/docs/{{version}}/valet)


<a name="configuration"></a>
### الإعداد

#### الدليل العام

After installing Laravel, you should configure your web server's document / web root to be the `public` directory. The `index.php` in this directory serves as the front controller for all HTTP requests entering your application.

#### إعداد الملفات

All of the configuration files for the Laravel framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

#### Directory Permissions

After installing Laravel, you may need to configure some permissions. Directories within the `storage` and the `bootstrap/cache` directories should be writable by your web server or Laravel will not run. If you are using the [Homestead](/docs/{{version}}/homestead) virtual machine, these permissions should already be set.

#### مفتاح التطبيق

The next thing you should do after installing Laravel is set your application key to a random string. If you installed Laravel via Composer or the Laravel installer, this key has already been set for you by the `php artisan key:generate` command.

Typically, this string should be 32 characters long. The key can be set in the `.env` environment file. If you have not renamed the `.env.example` file to `.env`, you should do that now. **If the application key is not set, your user sessions and other encrypted data will not be secure!**

#### إعداد إضافي

Laravel needs almost no other configuration out of the box. You are free to get started developing! However, you may wish to review the `config/app.php` file and its documentation. It contains several options such as `timezone` and `locale` that you may wish to change according to your application.

You may also want to configure a few additional components of Laravel, such as:

<div class="content-list" markdown="1">
- [الكاش](/docs/{{version}}/cache#configuration)
- [قاعدة البيانات](/docs/{{version}}/database#configuration)
- [الجلسة](/docs/{{version}}/session#configuration)
  </div>

<a name="web-server-configuration"></a>
## Web Server Configuration

<a name="pretty-urls"></a>
### عناوين انترنت جميلة

#### أباتشي

Laravel includes a `public/.htaccess` file that is used to provide URLs without the `index.php` front controller in the path. Before serving Laravel with Apache, be sure to enable the `mod_rewrite` module so the `.htaccess` file will be honored by the server.

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this alternative:

    Options +FollowSymLinks
    RewriteEngine On
    
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

#### إنجن إكس

إذا كنت تستخدم إنجن إكس , فإن التوجيه التالي في إعداد موقعك سيقوم بتوجية جميع الطلبات الى  وحدة التحكم الامامية : `index.php`

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

.طبعا, عند استخدام  [هومستيد](/docs/{{version}}/homestead) او [فاليت ](/docs/{{version}}/valet), سيتم إعداد عناوين الانترنت الجميلة تلقائيا
