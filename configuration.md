# الإعداد

- [المقدمة](#introduction)
- [إعداد البيئة](#environment-configuration)
    - [تحديد البيئة الحالية](#determining-the-current-environment)
- [الوصول إلى قيم الإعداد](#accessing-configuration-values)
- [التخزين المؤقت للإعداد](#configuration-caching)
- [نمط الصيانة](#maintenance-mode)

<a name="introduction"></a>
## المقدمة

يتم تخزين كافة ملفات الإعداد لإطار لارافيل في دليل `config`. يتم توثيق كل خيار، لذلك لا تتردد في النظر للملفات واخذ فكرة عن الخيارات المتاحة لك.

<a name="environment-configuration"></a>
## إعداد البيئة

غالبا ما يكون من المفيد الحصول على قيم إعداد مختلفة استنادا إلى البيئة التي يتم تشغيل التطبيق فيها. على سبيل المثال، قد ترغب باستخدام  برنامج تشغيل ذاكرة التخزين المؤقت محلي مختلف عن ما تستخدم  في خادوم الإنتاج الخاص بك

لجعل هذا مربط الفرس ، لارافيل يستخدم مكتبة  [دوت انف](https://github.com/vlucas/phpdotenv) من فانس لوكاس.عند تنصيب نسخة لارافيل جديدة، سوف يحتوي دليل الجذر الخاص بتطبيقك على ملف `.env.example`  اذا قمت بتنصيب لارافيل باستخدام الملحن سوف يتم تلقائيا اعادة تسيمة هذا الملف الى  `.env` وإلا، يجب إعادة تسمية الملف يدويا . 

> {tip} 
> بامكانك ايضا عمل ملف  `.env.testing`. هذا الملف سوف يتجاوز القيم من ملف `.env` عند تشغيل وحدة اختبار بي اتش بي او تنفيذ امر ارتسن مع الخيار `--env=testing` .

#### استرجاع إعداد البيئة

سيتم تحميل جميع المتغيرات المدرجة في هذا الملف في `$_ENV` بي اتش بي المتغير فائق العالمية عندما يتلقى التطبيق الخاص بك طلبا. ومع ذلك، يمكنك استخدام مساعد `env` لاسترداد القيم من هذه المتغيرات في ملفات الإعداد . في الواقع، إذا قمت بمراجعة ملفات إعداد لارافيل، ستلاحظ العديد من الخيارات تستخدم هذا المساعد .

    'debug' => env('APP_DEBUG', false),

القيمة الثانية التي تم تمريرها إلى الدالة `env` هي "القيمة الافتراضية". سيتم استخدام هذه القيمة في حالة عدم وجود متغير بيئة للمفتاح المعطى.

يجب ألا يضاف ملف `.env`  لنظام إدارة الشيفرة المصدرية  الخاص بتطبيقك ، حيث أن كل مطور / خادوم يستخدم التطبيق الخاص بك قد يتطلب إعداد بيئة مختلفة .

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application.

<a name="determining-the-current-environment"></a>
### تحديد البيئة الحالية

The current application environment is determined via the `APP_ENV` variable from your `.env` file. You may access this value via the `environment` method on the `App` [facade](/docs/{{version}}/facades):

    $environment = App::environment();

You may also pass arguments to the `environment` method to check if the environment matches a given value. The method will return `true` if the environment matches any of the given values:

    if (App::environment('local')) {
        // The environment is local
    }
    
    if (App::environment('local', 'staging')) {
        // The environment is either local OR staging...
    }

<a name="accessing-configuration-values"></a>
## الوصول إلى قيم الإعداد

You may easily access your configuration values using the global `config` helper function from anywhere in your application. The configuration values may be accessed using "dot" syntax, which includes the name of the file and option you wish to access. A default value may also be specified and will be returned if the configuration option does not exist:

    $value = config('app.timezone');

To set configuration values at runtime, pass an array to the `config` helper:

    config(['app.timezone' => 'America/Chicago']);

<a name="configuration-caching"></a>
## التخزين المؤقت للإعداد

To give your application a speed boost, you should cache all of your configuration files into a single file using the `config:cache` Artisan command. This will combine all of the configuration options for your application into a single file which will be loaded quickly by the framework.

You should typically run the `php artisan config:cache` command as part of your production deployment routine. The command should not be run during local development as configuration options will frequently need to be changed during the course of your application's development.

> {note} If you execute the `config:cache` command during your deployment process, you should be sure that you are only calling the `env` function from within your configuration files.

<a name="maintenance-mode"></a>
## نمط الصيانة

When your application is in maintenance mode, a custom view will be displayed for all requests into your application. This makes it easy to "disable" your application while it is updating or when you are performing maintenance. A maintenance mode check is included in the default middleware stack for your application. If the application is in maintenance mode, a `MaintenanceModeException` will be thrown with a status code of 503.

To enable maintenance mode, simply execute the `down` Artisan command:

    php artisan down

You may also provide `message` and `retry` options to the `down` command. The `message` value may be used to display or log a custom message, while the `retry` value will be set as the `Retry-After` HTTP header's value:

    php artisan down --message="Upgrading Database" --retry=60

To disable maintenance mode, use the `up` command:

    php artisan up
#### القالب المسؤول عن نمط الصيانة

The default template for maintenance mode responses is located in `resources/views/errors/503.blade.php`. You are free to modify this view as needed for your application.

#### نمط الصيانة وقوائم الإنتظار

While your application is in maintenance mode, no [queued jobs](/docs/{{version}}/queues) will be handled. The jobs will continue to be handled as normal once the application is out of maintenance mode.

#### بدائل لنمط الصيانة

Since maintenance mode requires your application to have several seconds of downtime, consider alternatives like [Envoyer](https://envoyer.io) to accomplish zero-downtime deployment with Laravel.
