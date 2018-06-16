# الإعداد

- [المقدمة](#introduction)
- [إعداد البيئة](#environment-configuration)
    - [استرجاع إعداد البيئة](#retrieving-environment-configuration)
    - [تحديد البيئة الحالية](#determining-the-current-environment)
- [الوصول إلى قيم الإعداد](#accessing-configuration-values)
- [التخزين المؤقت للإعداد](#configuration-caching)
- [نمط الصيانة](#maintenance-mode)

<a name="introduction"></a>
## المقدمة

يتم تخزين كافة ملفات الإعداد لإطار لارافيل في دليل `config`. يتم توثيق كل خيار، لذلك لا تتردد في إلقاء نظرة على الملفات لتُكَـوِّن فكرة على الخيارات المتاحة لك.

<a name="environment-configuration"></a>
## إعداد البيئة

غالبا ما يكون من المفيد الحصول على قيم إعداد مختلفة استنادا إلى البيئة التي يتم تشغيل التطبيق فيها. على سبيل المثال، قد ترغب باستخدام برنامج تشغيل ذاكرة التخزين المؤقتة محليا مختلفا عن ما تستخدمه في خادوم الإنتاج الخاص بك.

لجعل هذا مهمة سهلة للغاية، لارافيل يستخدم مكتبة PHP [DotEnv](https://github.com/vlucas/phpdotenv) من فانس لوكاس. عند تنصيب نسخة لارافيل جديدة، سيحتوي دليل الجذر الخاص بتطبيقك على ملف `.env.example`. إذا قمت بتنصيب لارافيل باستخدام الملحن (composer) سيتم تلقائيا إعادة تسيمة هذا الملف بـ`.env`. إن لم تستعمل الطرق الآنفة لتنصيب لارافيل يجب إعادة تسمية الملف يدويا. 

Your `.env` file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration. Furthermore, this would be a security risk in the event an intruder gain access to your source control repository, since any sensitive credentials would get exposed.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application. You may also create a `.env.testing` file. This file will override values from the `.env` file when running PHPUnit tests or executing Artisan commands with the `--env=testing` option.

> {tip} Any variable in your `.env` file can be overridden by external environment variables such as server-level or system-level environment variables.

<a name="retrieving-environment-configuration"></a>
### استرجاع إعداد البيئة

سيتم تحميل جميع المتغيرات المدرجة في هذا الملف في `$_ENV` المتغير فائق العالمية لـPHP عندما يتلقى تطبيقك طلبا. ومع ذلك، يمكنك استخدام مساعد `env` لاسترداد القيم من هذه المتغيرات في ملفات الإعداد. في الواقع، إذا قمت بمراجعة ملفات إعداد لارافيل، ستلاحظ العديد من الخيارات تستخدم هذا المساعد.

    'debug' => env('APP_DEBUG', false),

القيمة الثانية التي تم تمريرها إلى الدالة `env` هي «القيمة الإفتراضية». سيتم استخدام هذه القيمة في حالة عدم وجود متغير بيئة للمفتاح المُعطى.

<a name="determining-the-current-environment"></a>
### تحديد البيئة الحالية

يتم تحديد بيئة التطبيق الحالية عبر متغير `APP_ENV` من ملف `.env` الخاص بك. يمكنك الوصول إلى هذه القيمة عبر الطريقة `environment` في `App` [واجهة](/docs/{{version}}/facades) :

    $environment = App::environment();

يمكنك أيضا تمرير وسيطات إلى الطريقة `environment` للتحقق مما إذا كانت البيئة تطابق قيمة معينة. سترجع الطريقة `true` إذا تطابقت البيئة مع أي من القيم المعطاة:

    if (App::environment('local')) {
        // The environment is local
    }

    if (App::environment(['local', 'staging'])) {
        // The environment is either local OR staging...
    }

> {tip} The current application environment detection can be overriden by a server-level `APP_ENV` environment variable. This can be useful when you need to share the same application for different environment configurations, so you can set up a given host to match a given environment in your server's configurations.

<a name="accessing-configuration-values"></a>
## الوصول إلى قيم الإعداد

يمكنك الوصول بسهولة إلى قيم الإعداد باستخدام الدالة العالمية المساعدة `config` من أي مكان في التطبيق. يمكن الوصول إلى قيم الإعداد باستخدام بناء الجملة «نقطة»، والذي يتضمن اسم الملف والخيار الذي ترغب في الوصول إليه. قد يتم أيضا تحديد قيمة افتراضية وسيتم إرجاعها إذا كان خيار الإعداد غير موجود:

    $value = config('app.timezone');

لتعيين قيم الإعداد عند وقت التشغيل، قم بتمرير مصفوفة إلى المساعد `config`:

    config(['app.timezone' => 'America/Chicago']);

<a name="configuration-caching"></a>
## التخزين المؤقت للإعداد

لتسريع تطبيقك، يجب تخزين كافة ملفات الإعداد الخاص بك مؤقتا في ملف واحد باستخدام أمر Artisan `config:cache` هذا سوف يقوم بجمع كل خيارات الإعداد لتطبيقك في ملف واحد والتي سيتم تحميلها بسرعة من قبل الإطار.

يجب إعادة تشغيل الأمر `php artisan config:cache` كجزء من روتين تطوير الإنتاج الخاص بك. لا يجب تشغيل الأمر أثناء التطوير المحلي حيث إن خيارات الإعداد غالبا ما تحتاج إلى تغيير أثناء تطوير تطبيقك.

> {note} في حالة تنفيذ الأمر `config:cache` أثناء عملية التطوير، يجب أن تكون متأكدا من أنك تستدعي الدالة `env` من داخل ملفات الإعداد فقط.

<a name="maintenance-mode"></a>
## نمط الصيانة

عندما يكون التطبيق في نمط الصيانة، سيتم عرض طريقة عرض مخصصة لجميع الطلبات في تطبيقك. هذا يجعل من السهل «تعطيل» تطبيقك بينما يتم التحديث أو عند إجراء الصيانة. يتم تضمين الاختيار نمط الصيانة في كومة الوسيطة الافتراضية للتطبيق الخاص بك. إذا كان التطبيق في نمط الصيانة، سيتم طرح `MaintenanceModeException` مع رمز حالة 503.

لتمكين نمط الصيانة، قم بتنفيذ أمر Artisan `down`:

    php artisan down

يمكنك أيضا تقديم خيار `message` و `retry` إلى الأمر `down`. يتم استخدام قيمة  `message`  لعرض أو تسجيل رسالة مخصصة، في حين سيتم تعيين قيمة `retry` المحاولة بصفتها قيمة رأس `Retry-After` HTTP:

    php artisan down --message="Upgrading Database" --retry=60

لتعطيل وضع الصيانة، استخدم الأمر `up`:

    php artisan up

> {tip} يمكنك الوصول إلى القالب الافتراضي لاستجابات نمط الصيانة في `resources/views/errors/503.blade.php`، بإمكانك تعديل هذا العرض حسب الحاجة لتطبيقك.

#### نمط الصيانة وقوائم الإنتظار

بينما يكون تطبيقك في نمط الصيانة، لا يتم التعامل مع [صفوف الانتظار](/docs/{{version}}/queues). وسيستمر التعامل معها كالمعتاد بمجرد خروج التطبيق من نمط الصيانة.

#### بدائل لنمط الصيانة

بما أن وضع الصيانة يتطلب تَوَقُّـف تطبيقك لعدة ثوان، وبالنظر إلى البدائل مثل [Envoyer](https://envoyer.io) لإنجاز تطوير دون توقف مع لارافيل.
