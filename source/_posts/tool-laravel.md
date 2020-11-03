---
title: Laravel5.6 - 前端速览
date: 2020-03-23 13:54:18
tags: tool
categories: 工具
---

查看项目的laravel版本号：
```bash
docker-compose exec XXX bash # 进入容器中
php artisan --version        # 查看版本号
```
参考资料：[Laravel 5.6 中文文档](https://learnku.com/docs/laravel/5.6)
<!--more-->

## 基础
### 文件目录说明
- **app**：应用程序的核心代码
  - **Broadcasting**：应用程序的所有广播频道类，可以通过`make:channel`命令生成目录
  - **Console**：所有自定义的Artisan命令，可以通过`make:command`命令生成目录
  - **Events**：所有自定义的事件类，用来提醒应用其他部分发生了特定的操作，可以通过 `make:event`命令生成目录
  - **Exceptions**：应用的异常处理器，如果想自定义记录或者渲染异常的方式，可以修改此目录下的Handler类
  - **Http**：进入应用的请求处理逻辑，包括控制器、中间件和表单请求
  - **Jobs**：应用中的队列任务，应用任务可以被推送到队列或者在当前请求的生命周期内同步运行，可以通过`make:job`命令生成目录
  - **Listeners**：事件监听器，用于接收事件实例并执行响应该事件被触发的逻辑，可以通过`make:listener`命令生成目录
  - **Mail**：应用所有的邮件发送类，邮件对象允许将构建邮件的逻辑封装在可以使用`Mail::send`方法的地方，可以通过`make:mail`命令生成目录
  - **Notifications**：应用发送的所有「事务性」通知，可以通过各种驱动（例如邮件、Slack、短信）发送通知或是存储在数据库中。
  - **Policies**：应用的授权策略类，用来决定一个用户是否有权限去操作指定资源，可以通过`make:policy`命令生成目录
  - **Providers**：应用的所有服务提供器，通过在服务容器中绑定服务、注册事件以及执行其他任务来为即将到来的请求做准备来启动应用
  - **Rules**：应用自定义验证规则对象，意在将复杂的验证逻辑封装在一个简单的对象中，可以通过`make:rule`命令生成目录
- **Bootstrap**：引导框架并配置自动加载的文件，包括框架生成的用来提升性能文件的cache目录
- **config**：应用程序所有的配置文件
- **database**：数据填充和迁移文件
- **public**：入口文件index.php及一些资源文件（如图片、JavaScript 和 CSS）
- **resource**：视图和未编译的资源文件（如 LESS、SASS 或 JavaScript）
  - **assets/js/app.js**：引导和配置 Vue，Axios，jQuery 以及其他的 JavaScript 依赖
  - **assets/sass/app.scss**：引入包含SASS变量的文件，并加载 Bootstrap
  - **lang**：为支持国际化所需要的不同语言的文件夹
- **router**：应用的所有路由定义
  - **web.php**：包含RouteServiceProvider放置在web中间件组中的路由，提供会话状态、CSRF防护和cookie加密
  - **api.php**：包含RouteServiceProvider放置在api中间件组中的路由，它提供了频率限制
  - **console.php**：定义所有基于闭包的控制台命令的文件
  - **channels.php**：注册应用支持的所有事件广播渠道的文件
- **storage**：编译的Blade模板、基于文件的会话和文件缓存以及框架生成的其他文件
  - **app**：应用生成的任何文件
    - **public**：存储用户生成的文件，比如需要公开访问的用户头像
  - **framework**：框架生成的文件和缓存
  - **logs**：应用日志文件
- **tests**：Laravel内置的[PHPUnit](https://phpunit.de/)自动化测试文件
- **vendor**：Composer依赖包
- **.env**：本地的环境配置文件，不同服务器配置不同，不能提交到git，若没有可从根目录复制`.env.example`并修改
- **package.json**：指定 JavaScript 依赖
- **composer.json**：指定 PHP 依赖
- **webpack.mix.js**：文件是所有资源编译的入口点，Mix任务可以一起被链式调用，以精确定义资源的编译方式

### Laravel请求周期
- **开始**
  - `public/index.php`文件是所有对Laravel应用程序的请求的入口点,所有的请求都是经由Web服务器（Apache/Nginx）通过配置引导到这个文件。
  - `index.php`文件不包含太多的代码，却是加载框架的起点，该文件加载Composer生成定义的自动加载器，然后从`bootstrap/app.php`脚本中检索Laravel应用程序的实例。
- **HTTP / 控制内核**
  - 根据进入应用程序的请求类型来将传入的请求发送到HTTP内核或控制台内核，这两个内核是用来作为所有请求都要通过的中心位置。
  - 位于app/Http/Kernel.php中的HTTP内核定义了在执行请求之前运行的bootstrappers数组，这个数组负责在实际处理请求之前完成配置错误处理、配置日志记录、检测应用环境以及执行其他需要完成的任务
  - HTTP内核还定义了所有请求被应用程序处理之前必须经过的HTTP中间件的列表，这些中间件处理HTTP会话的读写、确定应用程序是否处于维护模式、验证CSRF令牌等
- **服务提供器**
  - 最重要的内核引导操作之一是加载应用程序的服务提供器
  - 所有服务提供器都在config/app.php配置文件的providers数组中配置
  - 服务提供器负责引导所有框架的各种组件，如数据库、队列、验证和路由组件，框架提供的每个功能都由服务提供器来引导并配置
- **分配请求**
  - 一旦引导了应用程序且注册所有服务提供器，Request请求就会被转交给路由器来进行调度，路由器将请求发送到路由或控制器或任何运行于路由的特定中间件
- **聚焦服务提供器**
  - 创建应用程序实例、注册服务提供器，并将请求交给被引导的应用程序


## 前端
### 概述
- **Laravel预置**：[Bootstrap](https://getbootstrap.com/) + [Vue](https://cn.vuejs.org/) + [NPM](https://www.npmjs.com/)
- **移除前端脚手架**：`php artisan preset none`
- **编译**：
  - `npm run watch`和`npm run dev`会处理`webpack.mix.js`中的指令，编译`resources/assets/sass/app.scss`和`resources/assets/js/app.js`文件，编译后的文件放在public目录下
  - `npm run watch`会监控资源文件并实时更改，`npm run dev`不会主动更新
  - 在某些环境中，文件更改时Webpack不会更新，请考虑使用`npm run watch-poll`命令
- **使用react**：`php artisan preset react`命令将移除 Vue 脚手架并替换为 React 脚手架，组件实例也会相应替换

### 视图
视图包含应用程序的 HTML，并且将控制器 / 应用程序逻辑与演示逻辑分开。视图文件存放于 resources/views 目录下。一个简单的视图如下所示：

```html
<!-- 此视图文件位置：resources/views/greeting.blade.php -->
<html>
    <body>
        <h1>Hello, {{ $name }}</h1>
    </body>
</html>

```

在 routes/web.php 文件中定义 web 界面的视图路由，view 函数的第一个参数对应着 resources/views 目录中视图文件的名称，第二个参数可以使用数组将数据传递到视图：

```php
Route::get('/', function () {
    return view('greeting', ['name' => 'James']);
});
```

视图文件也可以嵌套在 resources/views 目录的子目录中。「点」符号可以用来引用嵌套视图。例如，如果你的视图存储在 resources/views/admin/profile.blade.php，则可以这样引用它：

```php
return view('admin.profile', $data);
```

### Laravel Mix编辑资源
Laravel Mix用于使用几个常见的CSS和JavaScript预处理器为应用定义 Webpack构建步骤，可以通过简单链式调用来定义资源的编译。

```php
let mix = require('laravel-mix');

mix.js('resources/assets/js/app.js', 'public/js')
    .less('resources/assets/less/app.less', 'public/css')
    .extract(['vue', 'vue-router', 'vuex', 'axios']) // 提取依赖库，数组中的依赖或模块将会被提取到vendor.js文件中
    .version(); // 自动为所有编译文件名附加唯一哈希值，实现缓存清除功能
    
// BrowserSync会自动监控文件修改并将修改注入浏览器而无需手动刷新，可通过调用mix.browserSync()方法启用
mix.browserSync({
    // proxy: 'http://192.168.31.233:8109'
});

if (!mix.inProduction()) {
    mix.sourceMaps();  // 本地调试时开启资源映射
}
```

加载js文件时使用`mix`函数能自动确定被哈希的文件名称，且务必按照以下文件顺序加载，以防JavaScript报错

```php
<script src="{{ mix('/js/manifest.js') }}"></script>
<script src="{{ mix('/js/vendor.js') }}"></script>
<script src="{{ mix('/js/app.js')  }}"></script>
```

如果需要修改插件配置项，可以将一个对象作为第三个参数传入

```php
mix.less('resources/assets/less/app.less', 'public/css', {
    strictMath: true
});
```

如果想合并压缩任意数量的JavaScript或css文件，可以使用scripts或styles方法

```php
mix.styles([
    'public/css/vendor/normalize.css',
    'public/css/vendor/videojs.css'
    ], 'public/css/all.css')
    .scripts([
        'public/js/admin.js',
        'public/js/dashboard.js'
    ], 'public/js/all.js');
```

使用`copy`和`copyDirectory`方法将文件和目录复制到新位置

```php
mix.copy('node_modules/foo/bar.css', 'public/css/bar.css');
mix.copyDirectory('assets/img', 'public/img');
```

默认情况下，Mix会自动显示是否编译成功的操作系统通知，但某些场景下（如生产环境）可能希望禁止通知，可通过`disableNotifications`方法停用

```php
mix.disableNotifications();
```

如果使用的是react，Mix会自动安装React必要的Babel插件，只需替换`mix.js()`为`mix.react()`

```php
mix.react('resources/assets/js/app.jsx', 'public/js');
```

Mix提供了一个webpackConfig方法来合并任何Webpack配置以覆盖默认配置。

> 如果想完全自定义Webpack配置，可将node_modules/laravel-mix/setup/webpack.config.js文件复制到项目根目录，在package.json文件中将所有`--config`的值指向新配置文件。如果后续Mix版本有更新时，还需要手动合并webpack.config.js到自定义文件中。

```php
mix.webpackConfig({
    resolve: {
        modules: [
            path.resolve(__dirname, 'vendor/laravel/spark/resources/assets/js')
        ]
    }
});
```



### 多语言国际化
语言文件默认都放都在`resources/lang`文件夹中，相应的语言文件存在相应的文件夹下，所有的语言文件都返回一个键值对

```php
// resources/lang/en/messages.php
<?php

return [
    'welcome' => 'Welcome to our application'
];
```

Laravel 也提供支持使用字符串的『默认』翻译作为关键字来定义翻译字符串

```json
<!--resources/lang/es.json-->
{
    "I love programming.": "Me encanta programar."
}
```

在`config/app.php`配置文件中可以根据需要修改当前语言设置

```php
'locale' => 'en', // 当前语言
'fallback_locale' => 'en', // 备用语言，在当前语言不包含给定翻译字符串时被使用
```

可以灵活使用`App Facade`的`setLocale`、`getLocale`及`isLocale`方法

```php
Route::get('welcome/{locale}', function ($locale) {
    // 动态修改当前语言
    App::setLocale($locale);
});
```

```php
// 获取当前语言
$locale = App::getLocale();

// 检查语言环境是否为给定值
if (App::isLocale('en')) {
}
```

可以使用辅助函数`__`从语言文件中检索，`__`方法接受翻译字符串的文件名和键值作为其第一个参数

```php
echo __('messages.welcome');
echo __('I love programming.');
```

可以在视图文件中打印翻译字符串
```php
// 使用{{  }}语法
{{ __('messages.welcome') }}

// 使用@lang指令
@lang('messages.welcome')
```

如果需要，也可以在翻译字符串中定义占位符

```php
echo __('messages.welcome', ['name' => 'dayle']);
```

```php
'welcome' => 'Welcome, :name',
// 如果占位符中包含了首字母大写或者全体大写，翻译的内容也会做相应处理
'welcome' => 'Welcome, :NAME', // Welcome, DAYLE
'goodbye' => 'Goodbye, :Name', // Goodbye, Dayle
```

不同语言对于复数有不同的规则。使用管道符`|`，可以区分字符串的单复数形式

```php
// 基本用法
'apples' => 'There is one apple|There are many apples',
// 为多个数字范围指定翻译字符串
'apples' => '{0} There are none|[1,19] There are some|[20,*] There are many',
// 也可以在复数字符串中插入占位符，trans_choice方法第三个参数所传递数组将会替换占位符
'minutes_ago' => '{1} :value minute ago|[2,*] :value minutes ago',
```

```php
echo trans_choice('messages.apples', 10);
echo trans_choice('time.minutes_ago', 5, ['value' => 5]);
```

**重写扩展包的语言文件**：部分扩展包可能会附带自己的语言文件,可以通过在`resources/lang/vendor/{package}/{locale}`放置文件来重写它们，而不是直接修改扩展包的核心文件。



## Blade模板引擎
Blade是Laravel提供的一个模板引擎，并且不限制使用原生PHP代码，所有Blade视图文件都将被编译成原生PHP代码并缓存起来。Blade视图文件使用`.blade.php`作为文件扩展名，被存放在`resources/views`目录中。
### **模板继承**
- `@section`指令定义了视图的一部分内容，可以使用`@endsection`或`@show`收尾
  - `@endsection`指令只定义一个区块
  - `@show`指令定义并立即生成该区块
  - `@parent`指令会被替换为「主」布局@section间的内容
- `@yield`指令用来显示指定部分的内容
- `@extends`指令为子视图指定应该「继承」的布局

***实例：***
```html
<!-- 父布局文件保存于 resources/views/layouts/app.blade.php -->
<html>
    <head>
        <title>应用程序名称 - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            这是主布局的侧边栏。
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

```php
<!-- 子文件保存于 resources/views/layouts/child.blade.php -->

@extends('layouts.app')

@section('title', 'Page Title')

@section('sidebar')
    @parent

    <p>这将追加到主布局的侧边栏。</p>
@endsection

@section('content')
    <p>这是主体内容。</p>
@endsection
```

### **Components & Slots**
- `{{ $slot }}`变量将包含注入到组件的内容
- `@component`构建内容组件，使用`@endcomponent`收尾
- 需要向组件传递数据时，可以给`@component`指令的第二个参数传入一个数组，数组里的数据将在组件模板以变量的形式生效

***实例：***
```html
<!-- 组件文件存放于 /resources/views/alert.blade.php -->
<div class="alert alert-danger">
    <div class="alert-title">{{ $title }}</div>
    {{ $slot }}
</div>
```

```php
<!-- 引用组件的文件 -->
@component('alert')
    @slot('title')
        Forbidden
    @endslot
    你没有权限访问这个资源！
@endcomponent

@component('alert', ['title' => 'Forbidden'])
@endcomponent
```

> 可自定义创建@alert别名的组件，详见本文“自定义组件别名”部分



### **显示数据**

```php
// 路由定义
Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

```html
<!-- 默认情况下，Blade的{{}}会自动调用PHP的htmlspecialchars函数防止 XSS 攻击 -->
Hello, {{ $name }}.

<!-- 不想调用PHP的htmlspecialchars函数转义的话，可以使用以下语法 -->
Hello, {!! $name !!}.

<!-- 渲染json，可以使用@json指令替代PHP的手动json_encode -->
<script>
    var app = @json($array);
</script>

<!-- 使用@符告诉Blade渲染引擎保持这个表达式不变。渲染后，引擎会把@符移除，但{{ name }}表达式保留 -->
Hello, @{{ name }}.

<!-- 如果模板中一大部分需要显示JS变量，可用@verbatim指令包住 HTML，就不用在每个Blade echo语句前加@符 -->
@verbatim
    <div class="container">
        Hello, {{ name }}.
    </div>
@endverbatim
```



### **流程控制**

**条件指令**

```php
@if (count($records) === 1)
    我有一条记录！
@elseif (count($records) > 1)
    我有好几条记录！
@else
   我没有记录！
@endif
```

```php
@unless (Auth::check())
    未登陆 // 当Auth::check()为false 
@endunless
```

```php
@isset($records)
    // $records 已定义且不为 null
@endisset
```

```php
@empty($records)
    // $records 为空
@endempty
```

```php
// 使用`@auth`和`@guest`快速认证当前用户
@auth
   // 通过认证
@endauth

@guest
    // 未通过认证
@endguest

// 还可以在指令中指定认证看守器（Guard）
@auth('admin')
    // 通过认证
@endauth

@guest('admin')
    // 未通过认证
@endguest
```

```php
// 指令检查 section 是否有内容
@hasSection('navigation')
    @yield('navigation')
@endif
```

```php
@switch($i)
    @case(1)
        First case...
        @break

    @case(2)
        Second case...
        @break

    @default
        Default case...
@endswitch
```

**循环指令**

支持for、foreach、forelse、while，与PHP中对应的函数相同。

```php
@for ($i = 0; $i < 10; $i++)
    目前的值为 {{ $i }}
@endfor

@foreach ($users as $user)
    <p>此用户为 {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>没有用户</p>
@endforelse

@while (true)
    <p>死循环了</p>
@endwhile
```

```php
// 可以使用@break结束循环或@continue跳过当前迭代
@foreach ($users as $user)
    @if ($user->type == 1)
        @continue
    @endif

    <li>{{ $user->name }}</li>

    @if ($user->number == 5)
        @break
    @endif
@endforeach

// 还可以使用一行代码包含指令声明的条件
@foreach ($users as $user)
    @continue($user->type == 1)

    <li>{{ $user->name }}</li>

    @break($user->number == 5)
@endforeach
```

循环时，可以在循环内使用 $loop 变量获取一些有用的信息。
- `$loop->index`: 当前循环迭代的索引（从 0 开始）。
- `$loop->iteration`: 当前循环迭代 （从 1 开始）。
- `$loop->remaining`: 循环中剩余迭代数量。
- `$loop->count`: 迭代中的数组项目总数。
- `$loop->first`: 当前迭代是否是循环中的首次迭代。
- `$loop->last`: 当前迭代是否是循环中的最后一次迭代。
- `$loop->depth`: 当前循环的嵌套级别。
- `$loop->parent`: 在嵌套循环中，父循环的变量。

```php
@foreach ($users as $user)
    @if ($loop->first)
        这是第一个迭代。
    @endif

    @if ($loop->last)
        这是最后一个迭代。
    @endif

    <p>This is user {{ $user->id }}</p>
@endforeach
```


### 引入子视图

可以使用Blade的`@include`引入一个已存在的视图，所有在父视图的可用变量在被引入的视图中都是可用的。

```php
<div>
    @include('shared.errors')
    <form>
        <!-- 表单内容 -->
    </form>
    
    <!--也可以向引入的视图传递额外的数组数据-->
    @include('view.name', ['some' => 'data'])
    
    <!--如果想引入一个可能存在或可能不存在的视图，可使用@includeIf指令-->
    @includeIf('view.name', ['some' => 'data'])
    
    <!--如果要根据给定的布尔条件 @include 视图，可以使用 @includeWhen 指令-->
    @includeWhen($boolean, 'view.name', ['some' => 'data'])

    <!--要包含来自给定数组视图的第一个视图，可以使用 includeFirst 指令：-->
    @includeFirst(['custom.admin', 'admin'], ['some' => 'data'])
</div>

```


### 为集合渲染视图

```php
@each('view.name', $jobs, 'job', 'view.empty')
```

- **第一个参数**：对数组或集合中的每个元素进行渲染的部分视图
- **第二个参数**：要迭代的数组或集合
- **第三个参数**：将被分配给视图中当前迭代的变量名称
- **第四个参数**：选填，当需要迭代的数组为空时，将会使用这个参数提供的视图来渲染

> 通过@each渲染的视图不会从父视图继承变量。如果子视图需要这些变量，则应该使用 @foreach 和 @include

**堆栈**

Blade 可以被推送到在其他视图或布局中的其他位置渲染的命名堆栈。

```php
@push('scripts')
    <script src="/example.js"></script>
@endpush
```

可以根据需要多次压入堆栈，通过@stack命令中传递堆栈的名称来渲染完整的堆栈内容。

```php
<head>
    <!-- Head Contents -->
    @stack('scripts')
</head>
```



### 服务注入
- `@inject`命令可用于从Laravel服务容器中检索服务
- 传递给`@inject`的第一个参数为置放该服务的变量名称，而第二个参数是要解析的服务的类或是接口的名称
```php
@inject('metrics', 'App\Services\MetricsService')

<div>
    Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
</div>
```



### 拓展Blade
**自定义组件别名**
- 在`app\Providers\AppServiceProvider.php`中的`boot`函数中追加注册别名
```php
use Illuminate\Support\Facades\Blade;

public function boot()
{
   Blade::component('components.alert', 'alert');
}
```
- 在页面中使用指令渲染
```php
<!-- 数组里的数据将在组件模板以变量的形式生效 -->
@alert(['title' => 'Forbidden'])
    你无权访问！
@endalert

<!-- 可不传参数 -->
@alert
    你无权访问！
@endalert
```

**自定义指令**
- 在`app\Providers\AppServiceProvider.php`中的`boot`函数中追加注册指令
```php
use Illuminate\Support\Facades\Blade;

public function boot()
{
    Blade::directive('datetime', function ($expression) {
        return "<?php echo ($expression)->format('m/d/Y H:i'); ?>";
    });
}
```

- 更新Blade指令的逻辑后，需要使用Artisan命令`view:clear`删除所有已缓存的Blade视图。
- 在页面中使用指令`@datetime($var)`

**自定义If语句**
1. 在`app\Providers\AppServiceProvider.php`中的`boot`函数中追加注册指令
```php
use Illuminate\Support\Facades\Blade;

public function boot()
{
    Blade::if('env', function ($environment) { 
        return app()->environment($environment);
    });
}
```
2. 在模板中使用
```php
@env('local')
    // 该应用在本地环境中...
@elseenv('testing')
    // 该应用在测试环境中...
@else
    // 该应用不在本地或测试环境中...
@endenv
```

**其他**

```php
{{-- 此注释将不会出现在渲染后的 HTML --}}

@php
    // 在模板中执行一段纯 PHP 代码
@endphp
```