# التنصيب

- [التنصيب](#installation)
    - [متطلبات الخادوم](#server-requirements)
    - [تـنصيب لارفيل](#installing-laravel)
    - [الإعداد](#configuration)
- [إعداد خادوم الويب](#web-server-configuration)
    - [مسارات جميلة](#pretty-urls)

<a name="installation"></a>
## التنصيب

> {video} لاراكاست يوفر [مقدمة شاملة للارافيل مجانا](http://laravelfromscratch.com/) للمستعملين الجدد لإطار العمل. إنه مكان عظيم لانطلاق رحلتك.

<a name="server-requirements"></a>
### متطلبات الخادوم

متطلبات النظام لإطار العمل لارافيل قليلة. ولكن جميع هذه المتطلبات تصلح للآلة الإفتراضية [لارافيل هومستيد](/docs/{{version}}/homestead)، لذلك من المؤكد استخدام هومستيد بيئةَ تطوير لارفيل المحلية لديك.

وعلى كل حال، إذا كنت لا تستخدم هومستيد، فستحتاج إلى التأكد من توفر خادومك على المتطلبات الآتية:

<div class="content-list" markdown="1">
- PHP >= 7.1.3
- OpenSSL PHP Extension
- PDO PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension
- XML PHP Extension
- Ctype PHP Extension
- JSON PHP Extension
</div>

<a name="installing-laravel"></a>
### تنصيب لارفيل

يستخدم لارافيل [Composer](https://getcomposer.org) لإدارة معتمداته. لذا، قبل استخدام لارافيل، تأكد من تثبيت Composer على جهازك.

#### باستعمال مُثَبِّـت لارافيل

أولا، قم بتحميل مثبت لارافيل باستخدام Composer:

    composer global require "laravel/installer"

تأكد من وضع دليل composer (أو الدليل المكافئ لنظام التشغيل الخاص بك) `vendor bin` في `$PATH` ليتمكن نظامك من العثور على الأمر التنفيذي laravel. يوجد هذا الدليل في مواقع مختلفة بناءً على نظام التشغيل الخاص بك؛ ومع ذلك، تتضمن بعض المواقع الشائعة:

<div class="content-list" markdown="1">
- macOS: `$HOME/.composer/vendor/bin`
- GNU / Linux Distributions: `$HOME/.config/composer/vendor/bin`
</div>

بمجرد التثبيت ، سيؤدي الأمر `laravel new` إلى إنشاء تثبيت Laravel جديد في الدليل الذي تحدده. على سبيل المثال ، سيعمل `laravel new blog` على إنشاء دليل باسم` blog` يحتوي على تثبيت Laravel جديد مع جميع تبعيات Laravel المثبتة بالفعل:

    laravel new blog

#### بواسطة Composer Create-Project

بدلا من ذلك، يمكنك أيضا تثبيت لارافيل عن طريق تنفيذ الأمر `create-project` في الطرفية:

    composer create-project --prefer-dist laravel/laravel blog

#### خادوم التطوير المحلي

إذا كنت تملك PHP مثبتا محليا وكنت ترغب في استخدام خادوم التطوير المُدْمج في PHP لخدمة تطبيقك، يمكنك استخدام الأمر `artisan serve`. سيبدأ هذا الأمر في تشغيل خادوم التطوير على `http://localhost:8000`:

    php artisan serve

وبطبيعة الحال، خيارات التطوير المحلية الأكثر قوة مُتَاحة عبر [هومستيد](/docs/{{version}}/homestead) و[فاليت](/docs/{{version}}/valet).

<a name="configuration"></a>
### الإعداد

#### الدليل العام

بعد تثبيت لارافيل، يجب عليك إعداد جذر الويب / وثيقة خادوم الويب الخاص بك ليكون الدليل `public`. يعمل `index.php` في هذا الدليل كمتحكم أمامي لجميع طلبات HTTP التي تدخل تطبيقك.

#### إعداد الملفات

يتم تخزين كافة ملفات الإعداد لإطار لارافيل في دليل `config`. يتم توثيق كل خيار، لذلك لا تـتردد في إلقاء نظرة على ملفات الإعداد لتكون على دراية بالخيارات المتاحة.

#### تصريحات الدليل

بعد تثبيت لارافيل، قد تحتاج إلى إعداد بعض التصريحات. الدلائل داخل `storage` ودليل `bootstrap/cache` يجب أن تكون قابلة للكتابة من قبل خادوم الويب الخاص بك وإلا فلن يتم تشغيل لارافيل. إذا كنت تستخدم الآلة الإفتراضية [هومستيد](/docs/{{version}}/homestead)، تكون جميع هذه التصريحات مُفَعَّلة.

#### مفتاح التطبيق

 بعد تثبيت لارافيل يجب تعيين مفتاح تطبيقك الذي يُمَثِّـل نصا عشوائيا. إذا قمت بتثبيت لارافيل عبر Composer أو مثبت لارافيل، يتم تعيين هذا المفتاح تلقائيا عبر أمر `php artisan key:generate`.

عادة، يجب أن يكون هذا النص متكونا من 32 حرفا. يمكن تعيين المفتاح في ملف البيئة `.env`. إذا لم تكن قد قمت بتسمية الملف `.env.example` بـ `.env`، فعليك إجراء ذلك الآن. **إذا لم يتم تعيين مفتاح التطبيق، جلسات المستخدم الخاصة بك وغيرها من البيانات المشفرة لن تكون آمنة** !

#### إعداد إضافي

لارافيل لا يحتاج تقريبا أي إعداد آخر. تستطيع الآن البدء بالتطوير! ومع ذلك، قد ترغب في مراجعة ملف `config/app.php` ووثائقه الذي يحتوي على عدة خيارات مثل `timezone` و`locale` التي قد ترغب في تغييرها وفقا لتطبيقك.

قد تحتاج أيضا إلى إعداد بعض مكونات إضافية من لارافيل، مثل:

<div class="content-list" markdown="1">
- [ذاكرة التخزين المؤقتة](/docs/{{version}}/cache#configuration)
- [قاعدة البيانات](/docs/{{version}}/database#configuration)
- [الجلسة](/docs/{{version}}/session#configuration)
</div>

<a name="web-server-configuration"></a>
## Web Server Configuration

<a name="pretty-urls"></a>
### Pretty URLs

#### Apache

Laravel includes a `public/.htaccess` file that is used to provide URLs without the `index.php` front controller in the path. Before serving Laravel with Apache, be sure to enable the `mod_rewrite` module so the `.htaccess` file will be honored by the server.

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this alternative:

    Options +FollowSymLinks -Indexes
    RewriteEngine On

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

#### Nginx

If you are using Nginx, the following directive in your site configuration will direct all requests to the `index.php` front controller:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

Of course, when using [Homestead](/docs/{{version}}/homestead) or [Valet](/docs/{{version}}/valet), pretty URLs will be automatically configured.
