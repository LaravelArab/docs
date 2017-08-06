# لارافيل Valet

- [مقدمة](#introduction)
    - [Valet أو Homestead](#valet-or-homestead)
- [تثبيت](#installation)
    - [الترقية](#upgrading)
- [استضافة المواقع](#serving-sites)
    - [أمر "Park"](#the-park-command)
    - [أمر "Link"](#the-link-command)
    - [تأمين المواقع باستخدام TLS](#securing-sites)
- [مشاركة المواقع](#sharing-sites)
- [تعريفات Valet المخصصة](#custom-valet-drivers)
    - [التعريفات المحلية](#local-drivers)
- [أوامر Valet الأخرى](#other-valet-commands)

<a name="introduction"></a>
## مقدمة

Valet هو بيئة لارافل للتطوير لمستخدمي الماك بأقل الموارد. دون Vagrant، دون ملف `/etc/hosts`. حتى أنه يمكنك مشاركة مواقعك مع العامة باستخدام الأنابيب المحلية. _طبعا يعجبنا نحن أيضا_.

يقوم لارافيل Valet بإعداد جهاز الماك الخاص بك ليقوم بتشغيل [Nginx](https://www.nginx.com/) دائما في الخلفية عند تشغيل جهازك. ثم باستخدام [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq) يُحَـوِّل Valet كل الطلبات على النِّطاق `*.dev` للإشارة إلى المواقع المُثَبَّـة على جهازك المحلي.

بعبارة أخرى، تطوير لارافيل على بيئة سريعة جدا تستخدم 7 ميغابايت من الذاكرة العشوائية على أكثر تقدير. Valet ليس بديلا كاملا عن Vagrant أو Homestead، ولكنه بديل جيد إذا كنت تريد بداية أساسية ومرنة، وكنت تفضل السرعة العالية أو تستخدم جهازا بذاكرة عشوائية محدودة.

يكون Valet مُجَهَّـزا لدعم الإضافات التالية، وغير محدود بها:

<div class="content-list" markdown="1">
- [Laravel](https://laravel.com)
- [Lumen](https://lumen.laravel.com)
- [Bedrock](https://roots.io/bedrock/)
- [CakePHP 3](https://cakephp.org)
- [Craft](https://craftcms.com)
- [Jigsaw](http://jigsaw.tighten.co)
- [Slim](https://www.slimframework.com)
- [Statamic](https://statamic.com)
- Static HTML
- [Symfony](https://symfony.com)
- [WordPress](https://wordpress.org)
- [Zend](https://framework.zend.com)
</div>

ومع ذلك، ربما تريد تعزيز Valet بتعريفاتك المخصصة.

<a name="valet-or-homestead"></a>
### Valet أو Homestead

كما تعلمون، لارافيل تقدم لكم [Homestead](/docs/{{version}}/homestead)، بيئة تطوير محلية أخرى. Homestead وValet يختلفان فيما يتعلق بجمهورهما المقصود ونهجهما في التطوير المحلي. تقدم Homestead نظام Ubuntu كاملا على جهاز افتراضي مع إعدادات Nginx آلية. Homestead هو خيار رائع إذا كنت تريد بيئة تطوير لينكس افتراضية كاملة تعمل على ويندوز ولينكس.

Valet تدعم الماك فقط، وتتطلب منك تثبيت PHP وقاعدة بيانات مباشرة في جهازك المحلي. ويمكن تحقيق ذلك بسهولة باستخدام [Homebrew](http://brew.sh/) مع الأمر `brew install php71` و`brew install mysql`. توفر Valet بيئة تطوير محلية سريعة مع الحد الأدنى لاستهلاك الموارد، لذلك فهو رائع للمطورين الذين يحتاجون فقط إلى PHP وMySQL ولا يحتاجون إلى بيئة افتراضية متكاملة.

كل من Valet وHomestead خيار رائع لتكوين بيئة تطوير كاملة للارافيل. اختيارك أحدهما يعتمد على ذوقك الشخصي واحتياجات فريقك.

<a name="installation"></a>
## تثبيت

**Valet يتطلب نظام ماك و [Homebrew](http://brew.sh/). قبل التثبيت، يجب التأكد من عدم وجود برامج أخرى مثل Apache أو Nginx ملزمة لمنفذ الجهاز المحلي 80.**

<div class="content-list" markdown="1">

- تثبيت أو تحديث [Homebrew](http://brew.sh/) إلى الإصدار الأحدث باستخدام `brew update`.
- تثبيت PHP 7.1 باستخدام Homebrew من خلال `brew install homebrew/php/php71`.
- تثبيت Valet باستخدام Composer من خلال `composer global require laravel/valet`. تأكد من أن المسار التالي `~/.composer/vendor/bin` مسجل في نظام المسارات "PATH" الخاص بك.
- قم بتشغيل الأمر `valet install`. لإعداد وتثبيت Valet وDnsMasq وتسجيل Valet's daemon لكي يبدأ عند تشغيل نظامك.
</div>

عند تثبيت Valet، حاول رصد أي نطاق `*.dev` على الطرفية الخاصة بك من خلال أمر مثل `ping foobar.dev`. إذا تم تثبيت Valet بشكل صحيح، فسترى هذا النطاق مستجيبا على `127.0.0.1`.

ستبدأ خدمات Valet تلقائيا كل مرة تقوم فيها بتشغيل جهازك. ليست هناك حاجة لتشغيل `valet start` أو `valet install` مرة أخرى بمجرد الإنتهاء من تثبيت Valet الأولي.

#### استخدام نطاق آخر

افتراضيا، Valet يخدم المشاريع الخاصة بك باستخدام `.dev` TLD. إذا كنت ترغب في استخدام نطاق آخر، يمكنك القيام بذلك باستخدام أمر `valet domain tld-name`.

فمثلا، إذا كنت ترغب في استخدام `.app` بدلا من `.dev`، قم بتشغيل `valet domain app` و Valet ستبدأ خدمة المشاريع الخاصة بك في `*.app` تلقائيا.

#### قاعدة البيانات

إذا كنت بحاجة إلى قاعدة بيانات، جرب MySQL بتشغيل `brew install mysql` على الطرفية الخاصة بك. بمجرد انتهاء تثبيت MySQL، يمكنك البدء بتشغيلها باستخدام الأمر `brew services start mysql`. بعد ذلك يمكنك الاتصال بقاعدة البيانات في `127.0.0.1` باستخدام اسم المستخدم `root` وكلمة سر فارغة.

<a name="upgrading"></a>
### الترقية

يمكنك تحديث تثبيت Valet باستخدام الأمر `composer global update` في الطرفية الخاصة بك. بعد الترقية, فمن الممارسة الجيدة تشغيل الأمر `valet install` لكي يستطيع Valet عمل ترقيات إضافية لملفات الإعدادات الخاصة بك إذا لزم الأمر.

#### الترقية ل Valet 2.0

Valet 2.0 انتقلنا من خدمة الويب الأساسي من على Caddy إلى Nginx. قبل الترقية إلى هذا الإصدار يجب تشغيل الأوامر التالية لوقف وإلغاء تثبيت Caddy daemon:

    valet stop
    valet uninstall

بعد ذلك، يجب عليك الترقية إلى أحدث إصدار من Valet. اعتمادا على كيفية تثبيتك ل Valet، ويتم ذلك عادة من خلال Git أو Composer. إذا قمت بتثبيت Valet عبر Composer, يجب عليك استخدام الأمر التالي للتحديث إلى أحدث إصدار رئيسي:

    composer global require laravel/valet

حالما يتم تحميل الرمز المصدري ل Valet, يجب عليك تشغيل الأمر `install`:

    valet install
    valet restart

بعد الترقية, فقد يكون من الضروري إعادة وضع حجز على مواقعك أو إعادة ربطها.

<a name="serving-sites"></a>
## استضافة المواقع

Once Valet is installed, you're ready to start serving sites. Valet provides two commands to help you serve your Laravel sites: `park` and `link`.

<a name="the-park-command"></a>
**أمر "Park"**

<div class="content-list" markdown="1">
- Create a new directory on your Mac by running something like `mkdir ~/Sites`. Next, `cd ~/Sites` and run `valet park`. This command will register your current working directory as a path that Valet should search for sites.
- Next, create a new Laravel site within this directory: `laravel new blog`.
- Open `http://blog.dev` in your browser.
</div>

**That's all there is to it.** Now, any Laravel project you create within your "parked" directory will automatically be served using the `http://folder-name.dev` convention.

<a name="the-link-command"></a>
**أمر "Link"**

The `link` command may also be used to serve your Laravel sites. This command is useful if you want to serve a single site in a directory and not the entire directory.

<div class="content-list" markdown="1">
- To use the command, navigate to one of your projects and run `valet link app-name` in your terminal. Valet will create a symbolic link in `~/.valet/Sites` which points to your current working directory.
- After running the `link` command, you can access the site in your browser at `http://app-name.dev`.
</div>

To see a listing of all of your linked directories, run the `valet links` command. You may use `valet unlink app-name` to destroy the symbolic link.

> {tip} You can use `valet link` to serve the same project from multiple (sub)domains. To add a subdomain or another domain to your project run `valet link subdomain.app-name` from the project folder.

<a name="securing-sites"></a>
**تأمين المواقع باستخدام TLS**

By default, Valet serves sites over plain HTTP. However, if you would like to serve a site over encrypted TLS using HTTP/2, use the `secure` command. For example, if your site is being served by Valet on the `laravel.dev` domain, you should run the following command to secure it:

    valet secure laravel

To "unsecure" a site and revert back to serving its traffic over plain HTTP, use the `unsecure` command. Like the `secure` command, this command accepts the host name that you wish to unsecure:

    valet unsecure laravel

<a name="sharing-sites"></a>
## مشاركة المواقع

Valet even includes a command to share your local sites with the world. No additional software installation is required once Valet is installed.

To share a site, navigate to the site's directory in your terminal and run the `valet share` command. A publicly accessible URL will be inserted into your clipboard and is ready to paste directly into your browser. That's it.

To stop sharing your site, hit `Control + C` to cancel the process.

> {note} `valet share` does not currently support sharing sites that have been secured using the `valet secure` command.

<a name="custom-valet-drivers"></a>
## تعريفات Valet المخصصة

You can write your own Valet "driver" to serve PHP applications running on another framework or CMS that is not natively supported by Valet. When you install Valet, a `~/.valet/Drivers` directory is created which contains a `SampleValetDriver.php` file. This file contains a sample driver implementation to demonstrate how to write a custom driver. Writing a driver only requires you to implement three methods: `serves`, `isStaticFile`, and `frontControllerPath`.

All three methods receive the `$sitePath`, `$siteName`, and `$uri` values as their arguments. The `$sitePath` is the fully qualified path to the site being served on your machine, such as `/Users/Lisa/Sites/my-project`. The `$siteName` is the "host" / "site name" portion of the domain (`my-project`). The `$uri` is the incoming request URI (`/foo/bar`).

Once you have completed your custom Valet driver, place it in the `~/.valet/Drivers` directory using the `FrameworkValetDriver.php` naming convention. For example, if you are writing a custom valet driver for WordPress, your file name should be `WordPressValetDriver.php`.

Let's take a look at a sample implementation of each method your custom Valet driver should implement.

#### The `serves` Method

The `serves` method should return `true` if your driver should handle the incoming request. Otherwise, the method should return `false`. So, within this method you should attempt to determine if the given `$sitePath` contains a project of the type you are trying to serve.

For example, let's pretend we are writing a `WordPressValetDriver`. Our serve method might look something like this:

    /**
     * Determine if the driver serves the request.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return bool
     */
    public function serves($sitePath, $siteName, $uri)
    {
        return is_dir($sitePath.'/wp-admin');
    }

#### The `isStaticFile` Method

The `isStaticFile` should determine if the incoming request is for a file that is "static", such as an image or a stylesheet. If the file is static, the method should return the fully qualified path to the static file on disk. If the incoming request is not for a static file, the method should return `false`:

    /**
     * Determine if the incoming request is for a static file.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string|false
     */
    public function isStaticFile($sitePath, $siteName, $uri)
    {
        if (file_exists($staticFilePath = $sitePath.'/public/'.$uri)) {
            return $staticFilePath;
        }

        return false;
    }

> {note} The `isStaticFile` method will only be called if the `serves` method returns `true` for the incoming request and the request URI is not `/`.

#### The `frontControllerPath` Method

The `frontControllerPath` method should return the fully qualified path to your application's "front controller", which is typically your "index.php" file or equivalent:

    /**
     * Get the fully resolved path to the application's front controller.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string
     */
    public function frontControllerPath($sitePath, $siteName, $uri)
    {
        return $sitePath.'/public/index.php';
    }

<a name="local-drivers"></a>
### التعريفات المحلية

If you would like to define a custom Valet driver for a single application, create a `LocalValetDriver.php` in the application's root directory. Your custom driver may extend the base `ValetDriver` class or extend an existing application specific driver such as the `LaravelValetDriver`:

    class LocalValetDriver extends LaravelValetDriver
    {
        /**
         * Determine if the driver serves the request.
         *
         * @param  string  $sitePath
         * @param  string  $siteName
         * @param  string  $uri
         * @return bool
         */
        public function serves($sitePath, $siteName, $uri)
        {
            return true;
        }

        /**
         * Get the fully resolved path to the application's front controller.
         *
         * @param  string  $sitePath
         * @param  string  $siteName
         * @param  string  $uri
         * @return string
         */
        public function frontControllerPath($sitePath, $siteName, $uri)
        {
            return $sitePath.'/public_html/index.php';
        }
    }

<a name="other-valet-commands"></a>
## أوامر Valet الأخرى

Command  | Description
------------- | -------------
`valet forget` | Run this command from a "parked" directory to remove it from the parked directory list.
`valet paths` | View all of your "parked" paths.
`valet restart` | Restart the Valet daemon.
`valet start` | Start the Valet daemon.
`valet stop` | Stop the Valet daemon.
`valet uninstall` | Uninstall the Valet daemon entirely.
