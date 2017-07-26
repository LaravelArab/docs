# التوجيه

- [التوجيه الأساسي](#basic-routing)
- [وسائط التوجيه](#route-parameters)
    - [الوسائط المطلوبة](#required-parameters)
    - [الوسائط الإختيارية](#parameters-optional-parameters)
    - [قيود التعابير القياسية على الوسائط](#parameters-regular-expression-constraints)
- [التوجيه المُسمى](#named-routes)
- [مجموعات التوجيه](#route-groups)
    - [الوسيط](#route-group-middleware)
    - [المساحات](#route-group-namespaces)
    - [توجيه النطاقات الفرعية](#route-group-sub-domain-routing)
    - [بادئة التوجيه](#route-group-prefixes)
- [نموذج موجه الربط](#route-model-binding)
    - [الربط الضمني](#implicit-binding)
    - [الربط الصريح](#explicit-binding)
- [التحايل في طرق النماذج](#form-method-spoofing)
- [الوصول للتوجيه الحالي](#accessing-the-current-route)

<a name="basic-routing"></a>
## التوجيه الأساسي

أسهل أشكال التوجيه في لارافيل هو استخدام المُسمى بشكل مباشر في الرابط للوصول إليه، لارافيل توفر طريقة سهلة لتعريف التوجيهات:

    Route::get('foo', function () {
        return 'مرحباً بالعالم';
    });

#### ملفات التوجيه الإفتراضية


تُعرَّف جميع أوامر توجيه لارافيل في ملفات التوجيه الموجودة في المجلد `routes`. هذه الملفات يتم تحميلها بشكل تلقائي بواسطة لارافيل. الملف `routes/web.php` يُعرِّف التوجيهات الخاصة بواجهة الويب -أي الوصول للتوجيه عبر المُتصفح-. هذه التوجيهات تستخدم مجموعة الوسيط `web`، والتي من شأنها التحقق من حالة الجلسة والحماية عبر `CSRF`. التوجيهات في الملف `routes/api.php` تسخدم مجموعة الوسيط `api`.

لأغلب التطبيقات، ستبدأ بتعريف التوجيهات في الملف `routes/web.php`.

#### طرق التوجيه المتاحة

الموجه يُتيح لك تسجيل أي من طلبات الـ HTTP التالية:

    Route::get($uri, $callback);
    Route::post($uri, $callback);
    Route::put($uri, $callback);
    Route::patch($uri, $callback);
    Route::delete($uri, $callback);
    Route::options($uri, $callback);

أحياناً أنت بحاجة لتسجيل توجيه يرد على أكثر من نوع للطلبات، في هذه الحالة يُمكِنُك استخدام `match` لتحديد نوع طلب أو أكثر، أو تقوم بتسجيل موجه للرد على جميع الطلبات باستخدام الطريقة `any`:

    Route::match(['get', 'post'], '/', function () {
        //
    });

    Route::any('foo', function () {
        //
    });

#### حماية CSRF

تكون طرق الإرسال لأي نموذج HTML إما `POST` أو `PUT` أو `DELETE`، التوجيهات المُعرفة في `web` يجب أن تحتوي على حقل رمز CSRF ضمن عناصر النموذج، بخلاف هذا سيتم رفض الطلب. تستطيع أن تقرأ أكثر عن حماية CSRF في [CSRF توثيق](/docs/{{version}}/csrf):

    <form method="POST" action="/profile">
        {{ csrf_field() }}
        ...
    </form>

<a name="route-parameters"></a>
## وسائط التوجيه

<a name="required-parameters"></a>
### الوسائط المطلوبة

بالتأكيد أحياناً أنت بحاجة للوصول لبعض أجزاء الرابط من خلال الموجه. مثال: أنت بحاجة للوصول إلى مُعرف المُستخدم ID من الرابط. ستقوم بتعريف وسيط عبارة عن مُعرف المُستخدم في الموجه على النحو التالي:

    Route::get('user/{id}', function ($id) {
        return 'User '.$id;
    });

أنت بحاجة لتعريف العديد من الوسائط في التوجيه، وتكون جميعها مطلوبة لإتمام التوجيه، فستكون على النحو التالي:

    Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
        //
    });

وسائط التوجيه دائماً توضع بين الأقواس المعكوفة `{}` ويجب أن تتكون من حروف. وسائط التوجيه يجب ألا تحتوي على رمز `-` ولكن يمكنك استخدام الرمز `_`.

<a name="parameters-optional-parameters"></a>
### الوسائط الإختيارية

Occasionally you may need to specify a route parameter, but make the presence of that route parameter optional. You may do so by placing a `?` mark after the parameter name. Make sure to give the route's corresponding variable a default value:

    Route::get('user/{name?}', function ($name = null) {
        return $name;
    });

    Route::get('user/{name?}', function ($name = 'John') {
        return $name;
    });

<a name="parameters-regular-expression-constraints"></a>
### Regular Expression Constraints

You may constrain the format of your route parameters using the `where` method on a route instance. The `where` method accepts the name of the parameter and a regular expression defining how the parameter should be constrained:

    Route::get('user/{name}', function ($name) {
        //
    })->where('name', '[A-Za-z]+');

    Route::get('user/{id}', function ($id) {
        //
    })->where('id', '[0-9]+');

    Route::get('user/{id}/{name}', function ($id, $name) {
        //
    })->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

<a name="parameters-global-constraints"></a>
#### Global Constraints

If you would like a route parameter to always be constrained by a given regular expression, you may use the `pattern` method. You should define these patterns in the `boot` method of your `RouteServiceProvider`:

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::pattern('id', '[0-9]+');

        parent::boot();
    }

Once the pattern has been defined, it is automatically applied to all routes using that parameter name:

    Route::get('user/{id}', function ($id) {
        // Only executed if {id} is numeric...
    });

<a name="named-routes"></a>
## Named Routes

Named routes allow the convenient generation of URLs or redirects for specific routes. You may specify a name for a route by chaining the `name` method onto the route definition:

    Route::get('user/profile', function () {
        //
    })->name('profile');

You may also specify route names for controller actions:

    Route::get('user/profile', 'UserController@showProfile')->name('profile');

#### Generating URLs To Named Routes

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the global `route` function:

    // Generating URLs...
    $url = route('profile');

    // Generating Redirects...
    return redirect()->route('profile');

If the named route defines parameters, you may pass the parameters as the second argument to the `route` function. The given parameters will automatically be inserted into the URL in their correct positions:

    Route::get('user/{id}/profile', function ($id) {
        //
    })->name('profile');

    $url = route('profile', ['id' => 1]);

<a name="route-groups"></a>
## Route Groups

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `Route::group` method.

<a name="route-group-middleware"></a>
### Middleware

To assign middleware to all routes within a group, you may use the `middleware` key in the group attribute array. Middleware are executed in the order they are listed in the array:

    Route::group(['middleware' => 'auth'], function () {
        Route::get('/', function ()    {
            // Uses Auth Middleware
        });

        Route::get('user/profile', function () {
            // Uses Auth Middleware
        });
    });

<a name="route-group-namespaces"></a>
### Namespaces

Another common use-case for route groups is assigning the same PHP namespace to a group of controllers using the `namespace` parameter in the group array:

    Route::group(['namespace' => 'Admin'], function () {
        // Controllers Within The "App\Http\Controllers\Admin" Namespace
    });

Remember, by default, the `RouteServiceProvider` includes your route files within a namespace group, allowing you to register controller routes without specifying the full `App\Http\Controllers` namespace prefix. So, you only need to specify the portion of the namespace that comes after the base `App\Http\Controllers` namespace.

<a name="route-group-sub-domain-routing"></a>
### Sub-Domain Routing

Route groups may also be used to handle sub-domain routing. Sub-domains may be assigned route parameters just like route URIs, allowing you to capture a portion of the sub-domain for usage in your route or controller. The sub-domain may be specified using the `domain` key on the group attribute array:

    Route::group(['domain' => '{account}.myapp.com'], function () {
        Route::get('user/{id}', function ($account, $id) {
            //
        });
    });

<a name="route-group-prefixes"></a>
### Route Prefixes

The `prefix` group attribute may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

    Route::group(['prefix' => 'admin'], function () {
        Route::get('users', function ()    {
            // Matches The "/admin/users" URL
        });
    });

<a name="route-model-binding"></a>
## Route Model Binding

When injecting a model ID to a route or controller action, you will often query to retrieve the model that corresponds to that ID. Laravel route model binding provides a convenient way to automatically inject the model instances directly into your routes. For example, instead of injecting a user's ID, you can inject the entire `User` model instance that matches the given ID.

<a name="implicit-binding"></a>
### Implicit Binding

Laravel automatically resolves Eloquent models defined in routes or controller actions whose type-hinted variable names match a route segment name. For example:

    Route::get('api/users/{user}', function (App\User $user) {
        return $user->email;
    });

Since the `$user` variable is type-hinted as the `App\User` Eloquent model and the variable name matches the `{user}` URI segment, Laravel will automatically inject the model instance that has an ID matching the corresponding value from the request URI. If a matching model instance is not found in the database, a 404 HTTP response will automatically be generated.

#### Customizing The Key Name

If you would like model binding to use a database column other than `id` when retrieving a given model class, you may override the `getRouteKeyName` method on the Eloquent model:

    /**
     * Get the route key for the model.
     *
     * @return string
     */
    public function getRouteKeyName()
    {
        return 'slug';
    }

<a name="explicit-binding"></a>
### Explicit Binding

To register an explicit binding, use the router's `model` method to specify the class for a given parameter. You should define your explicit model bindings in the `boot` method of the `RouteServiceProvider` class:

    public function boot()
    {
        parent::boot();

        Route::model('user', App\User::class);
    }

Next, define a route that contains a `{user}` parameter:

    Route::get('profile/{user}', function (App\User $user) {
        //
    });

Since we have bound all `{user}` parameters to the `App\User` model, a `User` instance will be injected into the route. So, for example, a request to `profile/1` will inject the `User` instance from the database which has an ID of `1`.

If a matching model instance is not found in the database, a 404 HTTP response will be automatically generated.

#### Customizing The Resolution Logic

If you wish to use your own resolution logic, you may use the `Route::bind` method. The `Closure` you pass to the `bind` method will receive the value of the URI segment and should return the instance of the class that should be injected into the route:

    public function boot()
    {
        parent::boot();

        Route::bind('user', function ($value) {
            return App\User::where('name', $value)->first();
        });
    }

<a name="form-method-spoofing"></a>
## Form Method Spoofing

HTML forms do not support `PUT`, `PATCH` or `DELETE` actions. So, when defining `PUT`, `PATCH` or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form. The value sent with the `_method` field will be used as the HTTP request method:

    <form action="/foo/bar" method="POST">
        <input type="hidden" name="_method" value="PUT">
        <input type="hidden" name="_token" value="{{ csrf_token() }}">
    </form>

You may use the `method_field` helper to generate the `_method` input:

    {{ method_field('PUT') }}

<a name="accessing-the-current-route"></a>
## Accessing The Current Route

You may use the `current`, `currentRouteName`, and `currentRouteAction` methods on the `Route` facade to access information about the route handling the incoming request:

    $route = Route::current();

    $name = Route::currentRouteName();

    $action = Route::currentRouteAction();

Refer to the API documentation for both the [underlying class of the Route facade](https://laravel.com/api/{{version}}/Illuminate/Routing/Router.html) and [Route instance](https://laravel.com/api/{{version}}/Illuminate/Routing/Route.html) to review all accessible methods.
