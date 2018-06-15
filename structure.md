# بنية المجلد

- [مقدمة](#introduction)
- [المجلد الجذر](#the-root-directory)
    - [مجلد `app`](#the-root-app-directory)
    - [مجلد `bootstrap`](#the-bootstrap-directory)
    - [مجلد `config`](#the-config-directory)
    - [مجلد `database`](#the-database-directory)
    - [مجلد `public`](#the-public-directory)
    - [مجلد `resources`](#the-resources-directory)
    - [مجلد `routes`](#the-routes-directory)
    - [مجلد `storage`](#the-storage-directory)
    - [مجلد `tests`](#the-tests-directory)
    - [مجلد `vendor`](#the-vendor-directory)
- [مجلد التطبيق](#the-app-directory)
    - [مجلد `Console`](#the-console-directory)
    - [مجلد `Events`](#the-events-directory)
    - [مجلد `Exceptions`](#the-exceptions-directory)
    - [مجلد `Http`](#the-http-directory)
    - [مجلد `Jobs`](#the-jobs-directory)
    - [مجلد `Listeners`](#the-listeners-directory)
    - [مجلد `Mail`](#the-mail-directory)
    - [مجلد `Notifications`](#the-notifications-directory)
    - [مجلد `Policies`](#the-policies-directory)
    - [مجلد `Providers`](#the-providers-directory)

<a name="introduction"></a>
## مقدمة

بنية تطبيق لارافال المبدئية تهدف إلى توفير نقطة بداية عظيمة لكل من التطبيقات الكبيرة والصغيرة. بالطبع أنت حر في تنظيم تطبيقك كما   تشاء. لارافال لا يفرض تقريبا أي قيود على مكان وضع أي صنف (class) معين مادام Composer يستطيع تحميله آليا.

### أين هو مجلد Models ؟

عند البدء مع لارافال، العديد من المطورين يقعون في حيرة جراء عدم وجود مجلد `Models`. لكن عدم  وجود هذا المجلد أمر مقصود. وجدنا أن كلمة `النماذج - models` غامضة فهي تعني العديد من الأشياء المختلفة للعديد من الأشخاص المختلفين. بعض المطورين يشير بـ`نموذج - model` التطبيق إلى مجمل منطق العمل، بينما يشير آخرون بـ`النماذج - models` إلى الأصناف التي تتعامل مع قاعدة البيانات الارتباطية (relational database).

لهذا السبب، اخترنا وضع نماذج Eloquent في مجلد `app` مبدئيا، وتمكين المطوِّر من وضعها أينما يشاء.

<a name="the-root-directory"></a>
## المجلد الجذر

<a name="the-root-app-directory"></a>
#### مجلد App

ملجد `app`، كما تتوقع، يحوي الشفرة الرئيسة لتطبيقك. سنتَـطَـرَّق لهذا المجلد بالتفصيل قريبا. لكن -تقريبا- جميع الأصناف (classes) في تطبيقك ستكون داخل هذا المجلد.

<a name="the-bootstrap-directory"></a>
#### مجلد Bootstrap

مجلد `bootstrap` يَضُـمُّ الملفات المُـمَـهِدة لتشغيل إطار العمل وإعداد التحميل التلقائي. هذا المجلد يَحْـوي كذلك مجلد `cache` الذي يضُـمُّ بدوره الملفات التي أنشأها إطار العمل لتحسين الأداء مثل ملفات ذاكرة التخزين المؤقت (cache files) للمُوَجِّـه (route) وللخدمات (services).

<a name="the-config-directory"></a>
#### مجلد Config

مجلد `config`، كما يوحي اسمه، يحوي ملفات الإعدادت الخاصة بتطبيقك. من الجيد الاطلاع على هذه الملفات والتعرف على الخيارات المتاحة.

<a name="the-database-directory"></a>
#### مجلد Database

مجلد `database` يحوي ملفات الترحيل والبذور (migration and seeds) لقاعدة البيانات الخاصة بك. إذا شئت يمكنك استعمال المجلد لاحتواء قاعدة بيانات SQLite.

<a name="the-public-directory"></a>
#### مجلد Public

مجلد `public` يحوي ملف `index.php`، الذي يمثل نقطة دخول كل الطلبات إلى تطبيقك. هذا المجلد يحوي أيضا ممتلكاتك (assets) مثل الصور، ملفات جافاسكريبت وCSS.

<a name="the-resources-directory"></a>
#### مجلد Resources

مجلد `resources` يحوي ملفات العروض (views) والخامات (raw) الخاصة بك والممتلكات الغير مُجَمَّـعة (un-compiled assets) مثل LESS، SASS أو جافاسكريبت. هذا المجلد يحوي أيضا ملفات اللغة.

<a name="the-routes-directory"></a>
#### مجلد Routes

مجلد `routes` يحوي كل تعريفات التوجيه (route definitions) لتطبيقك. افتراضيا، بعض ملفات التوجيه مُدرَجة مع لارافال: `web.php` و`api.php` و`console.php` و`channels.php`.

ملف `web.php` يحتوي على الموجهات التي يضعها `RouteServiceProvider` في مجموعة الوسيط (middleware group) `web`، الذي يقدم حالة الجلسة، حماية CSRF، وتشفير الكعكات (cookies). إذا كان تطبيقك لا يوفر واجهة برمجة تطبيقات عديمة الحالة وRESTful، على الأرجح سيتم تعريف الموجهات الخاصة بك في ملف `web.php`.

ملف `api.php` يحوي الموجهات التي يضعها `RouteServiceProvider` في مجموعة الوسيط `api`، الذي يقدم معدَّل الحد. هذه الموجهات يتم إعدادها لتكون عديمة الحالة، لذا الطلبات عبر هذه الموجهات يتم إعدادها لتكون مُعَرَّفة عبر رموز (tokens) ولن يتم تمكينها من دخول حالة الجلسة.

يمكنك تعريف أوامر المعراض القائمة على الإغلاق (Closure based console commands) الخاص بك في ملف `console.php`. كل إغلاق مُقَيَّـد بمثيل أمر يمَكِّـن مقاربة سهلة من التعامل مع كل طرق مدخلات ومخرجات الأمر. بالرغم من أن هذا الملف لا يُعَرِّف طلبات HTTP، فهو يعرف نقاط الدخول القائمة على المعراض (موجهات) في تطبيقك.

يمكنك تسجيل كل قنوات بث الأحداث التي يدعمها تطبيقك في ملف `channels.php`.

<a name="the-storage-directory"></a>
#### مجلد Storage

مجلد `storage` يحوي قوالب Blade المترجمة، الجلسات القائمة على الملف، ملف ذاكرة التخزين المؤقتة (cache)، وملفات أخرى قام بإنشائها إطار العمل. هذا المجلد ينقسم إلى مجلدات `app` و`framework` و`logs`. يمكن استخدام مجلد `app` لحفظ أي ملفات منشأة من قبل تطبيقك. يمكن استخدام مجلد `framework` لحفظ الملفات المنشأة وملفات ذاكرة التخزين المؤقتة. وأخيرا، يحوي مجلد `logs` ملفات السجل الخاصة بتطبيقك.

يمكن استخدام مجلد `storage/app/public` لحفظ الملفات المنشأة من طرف المستخدمين، مثل الصور الرمزية للملف الشخصي، التي ينبغي أن تكون متاحة للجميع. يجب عليك إنشاء رابط رمزي في `public/storage` يشير إلى هذا المجلد. يمكنك إنشاء الرابط باستعمال الأمر `php artisan storage:link`.

<a name="the-tests-directory"></a>
#### مجلد Tests

مجلد `tests` يحوي اختباراتك الأوتوماتيكية. مثلا [PHPUnit](https://phpunit.de/) متوفر افتراضيا. كل صنف (class) اختبار يجب أن تبدأ بكلمة `Test`. يمكنك تشغيل اختباراتك عبر الأمر `phpunit` أو الأمر `php vendor/bin/phpunit`. 

<a name="the-vendor-directory"></a>
#### مجلد Vendor

مجلد `vendor` يحتوي تبعيات [composer](https://getcomposer.org) الخاص بك.

<a name="the-app-directory"></a>
## مجلد App

يضم مجلد `app` معظم (ملفات) تطبيقك. بشكل افتراضي، مساحة هذا المجلد هي `App` ويتم تحميلها آليا بواسطة composer باستعمال [معيار PSR-4 للتحميل الآلي](http://www.php-fig.org/psr/psr-4/). 

يحتوي مجلد `app` عدة مجلدات أخرى منها `Console` و`Http` و`Providers`. يمكنك اعتبار المجلدين `ِConsole` و`Http` موفرين لواجهة برمجة تطبيقات (API) داخل تطبيقك. كل من بروتوكول HTTP وCLI آليات للتفاعل مع تطبيقك، لكنهما لا يحويان منطق التطبيق. بعبارة أخرى، هما طريقان لإصدار الأموار بسهولة إلى تطبيقك. تحوي مجلد `Console` كل أوامر Artisan الخاصة بك، بينما يحوي مجلد `Http` المتحكمات الخاصة بك، الوسائط (middlewares)، والطلبات (requests).

عدة مجلدات أخرى يتم توليدها داخل مجلد `app` لكما قمت باستعمال أمر Artisan `make` لتوليد أصناف. مثلا مجلد `app/Jobs` لن يكون موجودا حتى يتم تنفيذ الأمر `make:job` لتوليد صنف مهمة (job class).

>{tip}العديد من الأصناف من مجلد `app` يتم توليدها بواسطة Artisan عبر الأوامر. لرؤية الأوامر المتاحة، قم يتنفيذ الأمر `php artisan list make` في الطرفية.

<a name="the-console-directory"></a>
#### مجلد Console

The `Console` directory contains all of the custom Artisan commands for your application. These commands may be generated using the `make:command` command. This directory also houses your console kernel, which is where your custom Artisan commands are registered and your [scheduled tasks](/docs/{{version}}/scheduling) are defined.

<a name="the-events-directory"></a>
#### مجلد Events

This directory does not exist by default, but will be created for you by the `event:generate` and `make:event` Artisan commands. The `Events` directory, as you might expect, houses [event classes](/docs/{{version}}/events). Events may be used to alert other parts of your application that a given action has occurred, providing a great deal of flexibility and decoupling.

<a name="the-exceptions-directory"></a>
#### مجلد Exceptions

The `Exceptions` directory contains your application's exception handler and is also a good place to place any exceptions thrown by your application. If you would like to customize how your exceptions are logged or rendered, you should modify the `Handler` class in this directory.

<a name="the-http-directory"></a>
#### مجلد Http

The `Http` directory contains your controllers, middleware, and form requests. Almost all of the logic to handle requests entering your application will be placed in this directory.

<a name="the-jobs-directory"></a>
#### مجلد Jobs

This directory does not exist by default, but will be created for you if you execute the `make:job` Artisan command. The `Jobs` directory houses the [queueable jobs](/docs/{{version}}/queues) for your application. Jobs may be queued by your application or run synchronously within the current request lifecycle. Jobs that run synchronously during the current request are sometimes referred to as "commands" since they are an implementation of the [command pattern](https://en.wikipedia.org/wiki/Command_pattern).

<a name="the-listeners-directory"></a>
#### مجلد Listeners

This directory does not exist by default, but will be created for you if you execute the `event:generate` or `make:listener` Artisan commands. The `Listeners` directory contains the classes that handle your [events](/docs/{{version}}/events). Event listeners receive an event instance and perform logic in response to the event being fired. For example, a `UserRegistered` event might be handled by a `SendWelcomeEmail` listener.

<a name="the-mail-directory"></a>
#### مجلد Mail

This directory does not exist by default, but will be created for you if you execute the `make:mail` Artisan command. The `Mail` directory contains all of your classes that represent emails sent by your application. Mail objects allow you to encapsulate all of the logic of building an email in a single, simple class that may be sent using the `Mail::send` method.

<a name="the-notifications-directory"></a>
#### مجلد Notifications

This directory does not exist by default, but will be created for you if you execute the `make:notification` Artisan command. The `Notifications` directory contains all of the "transactional" notifications that are sent by your application, such as simple notifications about events that happen within your application. Laravel's notification features abstracts sending notifications over a variety of drivers such as email, Slack, SMS, or stored in a database.

<a name="the-policies-directory"></a>
#### مجلد Policies

This directory does not exist by default, but will be created for you if you execute the `make:policy` Artisan command. The `Policies` directory contains the authorization policy classes for your application. Policies are used to determine if a user can perform a given action against a resource. For more information, check out the [authorization documentation](/docs/{{version}}/authorization).

<a name="the-providers-directory"></a>
#### مجلد Providers

The `Providers` directory contains all of the [service providers](/docs/{{version}}/providers) for your application. Service providers bootstrap your application by binding services in the service container, registering events, or performing any other tasks to prepare your application for incoming requests.

In a fresh Laravel application, this directory will already contain several providers. You are free to add your own providers to this directory as needed.
