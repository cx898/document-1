## 用更多的Angular的魔法大力提升你的 Web App 开发效率

@(翻译)[angular|毕设翻译]

由于之前的那篇《利用angular去增强你的web应用开发》的受欢迎，我决定给所有的开发者写一篇文章涵盖更多的关于angular的有趣和便捷的使用让更多的人去使用它。Angular是一个令人难以置信的工具，但是有一大堆的高级功能和特性隐藏在了文档的深处，还有一些并不能那么直接的学习到。AngularJs是一个新产品所以会有大量的新特性还没被发现或者被博文记录。
 
这篇文章涵盖了更多关于angular的隐藏的宝藏和开发的技巧方法去加强你的angularjs开发。如果你想成为一个angularjs开发的专家，最好阅读这篇文章。


###关于这篇文章

这篇文章是前一篇《利用angular去增强你的web应用开发》的续篇。那些还没有阅读过前一篇的的读者，要确保已经非常熟悉前一篇所讲的内容，你才能深入的理解我们这篇文章的内容。因为我们这篇文章所有的基础知识都是基于前篇文章中的内容的。

澄清一点东西，在之前一篇文章里的angularjs的主要模块的加载顺序一般是如代码里所显示的一样的（这个App模块在本篇文章里经常被用到）。

```javascript
// 你可以任意移除这个ng资源数组中的内容
var App = angular.module('YOUR_APP_NAME', ['ngResource']);
```

angularjs很庞大并且提供了许多还没被他的社区开发者发现的新特性。与其说这是隐藏的特性，不如说这些特性的使用方法还没有在互联网上广泛的覆盖开。本文就提到了很多。本文会介绍很多有用的技巧和方法，能够让你开发angularjs驱动的web应用更有效果，即使你已经用到了很多高效的方法。

###Angularjs 和 IE浏览器

在我们深入讨论更多关于Angularjs的独特技巧之前，一些关于IE浏览器的信息应该被先提及。

真的很不推荐在IE6和IE7上运行Angularjs的应用程序。一些定制的标签和hashchange事件的确实让这个应用他本身受到了限制和阻碍。尽管已经有一些这些方面的支持，但是Angularjs还是推荐只去在一些高级浏览器中使用它（例如：Chrome,Firefox,IE8+,Safari和Opera）。尽最大的可能避免在IE6和IE7重使用Angularjs，这样你会更高效的开发。

Angularjs提到定制标签的使用在IE8中是支持的并且他强调Angularjs确实在IE8中是可以使用任意的定制标签的。我不相信IE8完全的遵循自定义标签因为要让HTML5标签能够更好的跑在IE8上，需要一个特殊的HTML5 shiv文件。因此，你将需要去在你的文件开头定义每一个你用到的标签（例如```document.createElement('ng-pluralize')```,```document.createElement('ng-view')```,等）。因此如果你能解决用到的自定义标签或者你可以完全的避免IE8那就坚定的去使用标准的HTML/HTML5的标签，那样会更好一点。

###数据绑定和$scope作用域改变

数据绑定通过各种方式绑定包含在$scope变量里的的值的改变去响应DOM的变化。不管他是直接的用花括号{{some_value}}，还是直接写在一个属性里还是用一种绑定模型，Angularjs就是为了把这件事做的更好而设计的。

Angularjs控制在```$scope```中的改变每当一个主要的事件在程序中触发，然后一旦事件发生，一个digest操作将会被发布，然后scope会被更新。Angularjs里的```$scope```变量作为一个巨大的key/value存储哈希表，这个哈希表在内部循环并且每当digestion触发的时候便与之前的值做脏值检查。如果和之前的值不一样发生了改变，然后Angularjs会消除一个改变事件并且重新渲染一些基于这些值绑定的的DOM。这些Angularjs关注的主要事件主要是用户输入事件（鼠标、键盘等），任何一种重要的浏览器事件（超时、失去焦点等），还有任何数据从服务端返回（$http请求、模板下载等）。这就是为什么每当任何不是angular自身的代码发生变化（例如一个第三方插件做了一些事，或者URl被人为的修改了）会产生一个对```$scope.$apply```直接的请求，去通知AngularJS去识别这些改变（所有的这些会在后面详细介绍）。


好处就是AngularJS并不是贪婪的，他只会在必要的时候运行，所以如果你的网页只是作为一个空的背景标签，除非你在后台有实现一些循环，那么AngularJS不会消除任何的digest请求。

你需要考虑任何一个应用会间歇性的一遍又一遍地检查相同的数据将会很快的使运行效率降低，但是没有。AngularJS在与其他的MVC的框架里表现得非常的好，并且相同的脏值检查的方式在一些视频游戏程序里是非常常见的。

###根作用域和作用域的继承

$rootScope扮演了一个所有的```$scope```对象的父层作用域对象的角色这就意味着当一个controller被执行之后这个$scope变量已经依据```$rootScope```对象里的内容通过链接/拷贝的方式生成了。最好的方法就是把```$scope```变量作为一个```$rootScope```的子类去理解（```$scope```是从```$rootScope```继承而来）。理解了这个对你在你的整个应用里想去新增一些新的方法到你的作用域变量上是非常有用的（例如session信息、标记和状态等等）。

下面的样例代码就给你展示了怎么在你的```$scope```上添加不同的库或者对象代码。

```javascript
App.run(['$rootScope',function($rootScope) {

  //这个将会被所有的作用域变量取到
  $rootScope.includeLibraries = true;

  //这个方法也能够被所有的作用域变量得到
  $rootScope.include = function(libraries) {
    var scope = this;
    //将这个libraries里的每一个元素直接
    for(var i=0;i<libraries.length;i++) {
      var key = libraries[i];
      scope[key] = getLibrary(key);
    }
    return scope;
  };

}]);
```

然后在你的控制器里或者指令里你可以像下面这么做

```
var Ctrl = function($scope) {
  if($scope.includeLibraries) { // 这个标记是设置在$rootScope里的
    $scope = $scope.include(['plugin1', 'library1']);
  }
};
Ctrl.$inject = ['$scope'];
```

尽量的不要去设置太多的数据在```$scope```和```$rootScope```变量里。毕竟，AngularJS会很经常的的处理```$scope```变量里的数据然而你并不想覆盖数据。



###```$apply```, ```$digest```和```$$phaase```


这是去理解AngularJS的最重要的一部分，在将来的某个时间某个地点你需要去整合一个第三方的的app到你的网站里你会发现他会打破Angular的规范或者不会被Angular所接纳。为了实现这个需求，你将需要去理解```$digest```和```$apply```是怎么工作的。

Angular在运行的时候，每当一个核心事件触发的时候（例如页面首次加载，ajax请求返回成功，URL发生了改变等，Angular会检测到他的改变然后准备一个digestion（一个在```$scope```成员中的内部循环事件），这些只需要花费大概几毫秒，但是Angular只会运行一次这个过程。你可以手动执行这个过程，只需要运行一次```$scope.$apply()```方法就好（这在第三方应用程序在你的网页上进行一些Angular需要知道的一些激活更新操作的时候是很有用的）。如果设置了你自己的数据绑定并且运行了```$scope.$apply()```方法，然后可能会抛出一个可能引起你程序终止的异常（这种情况一般发生在一个digestion正在后台运行的时候）。所以，你将需要意识到这一点，当一个digestion正在被```$$phase```变量检查的时候（这点将在后面介绍）。```$apply```方法会执行一个叫做```$diest()```的内部方法去激活angular去轮询他的所有的```$watch```方法。


为了应对这个```$apply```的一场，你讲需要去关注```$scope.$$phase```标记来观察是否一个digestion阶段正在后台运行。如果一个digestion阶段正在运行，那么你只需要直接的去设置```$scope```里的值然后他们就会被当前的digestion过程回收。这里是一个我用来绕过这个问题的组合方法。

```javascript
// 当你把这个方法添加到$rootScope变量上，所有的$scope变量都可以调用这个方法
$rootScope.$safeApply = function($scope, fn) {
  fn = fn || function() {};
  if($scope.$$phase) {
  // 不要担心，这个变量已经被设置过了并且angularjs会回收他
    fn();
  }
  else {
    $scope.$apply(fn); 
  }
};
$scope.some_value = 'value...';
$scope.$safeApply($scope, function() {
});
```

如果遇到一些需求不包含在angular services提供的功能中，你需要手工对其事件处理进行绑定，在这种时候，你可能需要使用上述的method（比如，当运行任何```$http```方法时，angular会自动获取其改变）

考虑当你想要改变网页的URL的时候，（由于使用了Angular）你需要注意```$$phase```变量是否允许你改变URL。如果当前工作（phase相位）状态是digesting中，那么就只要采用window.location这种旧的方式来改变URL就行了。

```javascript
//be sure to inject $scope and $location somewhere before this
var changeLocation = function(url, force) {
  //this will mark the URL change
  $location.path(url); //use $location.path(url).replace() if you want to replace the location instead

  $scope = $scope || angular.element(document).scope();
  if(force || !$scope.$$phase) {
    //this will kickstart angular if to notice the change
    $scope.$apply();
  }
};
```

这需要你确保你的网页URl无论如何都会得到改变

###Services与Controllers之间的通信

无论何时你的应用中发生了会对controller和directive造成影响的事情，你最好使用Angular提供的$emit,$on和$broadcast方法
比如这个例子，包含了权限和会话改变（就好像用户退出或者某个标志位？？改变的时候）

当你需要一个 父controller或scope 通知所有子controller某一改变时，你可以使用$broadcast方法

```javascript
//get the topmost scope
var $scope = angular.element(document).scope();

//logout event
var logoutEvent = 'logout';
var logoutArgs = ['arg'];
$scope.$broadcast(logoutEvent, logoutArgs); 

//login event
var logoutEvent = 'logout';
var logoutArgs = ['arg'];
$scope.$broadcast(logoutEvent, logoutArgs);
```

然后你在你的controller和directive李可以这么做：
```javascript
//
//in your controller
//
var Ctrl = function($scope) {
  $scope.$on('logout', function(args) {
    alert('bye bye');
  });
  $scope.$on('login', function(args) {
    alert('hello there');
  });
};
Ctrl.$inject = ['$scope'];

//
//in your directive
//
App.directive('sessionStatus', function() {
  return function($scope, element, attrs) {
    $scope.$on('login', function() {
      element('html', 'You are logged in!');
    });
    $scope.$on('logout', function() {
      element('html', 'You are logged out!');
    });
  };
});
```

你还可以使用```$scope.emit```将事件向上传递并触发事件


```javascript
var Ctrl = function($scope) {
  $scope.onLogoutClick = function() {
    $scope.$emit('logout');
  }
};
Ctrl.$inject = ['$scope'];
```


使用这些controller内通信方法，就不需要创建controoler间的相关耦合代码。


###Controller和Routes的附加特性

AngularJS同样带来了一些很多人不知道的方法用来处理在controllers之间的请求通信。但是，在我降到这个之前我想重点指出一个可供选择的创建controller的方式。

```javascript
App.controller('Ctrl', ['$scope', function($scope) {
  //exactly same result as creating a controller with an explicit function 
}]);
```

然后，回归正题。当一个请求发生，你可以在route中安放一些带有即时解析的动作作为route的一部分，这些解析对象一般来说是函数，他们会在请求传送到controller之前被执行。
注：（你可以配置route，使得route遇到一个请求时，把resolve内设定的解析请求执行再作为参数传送给controller）


```javascript
//checkout the route code below first before reading this
var Ctrl = function($scope, $http, argument1, argument2, argument3) {
  alert(argument1); //someDependeny's value or toString method
  alert(argument2); //"some value"
  alert(argument3); //the response.data value returned from GET /path/to/some/url
};
Ctrl.$inject = ['$scope', '$http', 'argument1', 'argument2', 'argument3'];

//here's where the magic happens
App.config(['$routeProvider',function($routeProvider) {
  $routeProvider.when('/some/page/with/an/:id',{
    templateUrl: '/path/to/some/template.html',
    controller: Ctrl,
    resolve: {
      argument1: 'someDependency', //this is a registered dependency and works the same as a dependeny injection would within a controller
      argument2: function() { return 'some value'; }, //this is resolved instantly since there is nothing going on
      argument3: function() {
        return $http.get('/path/to/some/url',function(response) {
          return response.data; //this is what is returned as the value for argument3
        }
      }
    }
  });
}]);
```

当应用按照它的方式路由到了/some/page/with/an/:id，Angular讲开始解析这些呈现在resolve:{}块中的依赖项。一旦就绪（当所有promise完成），请求就会被转发到controller并正常工作。

如果你想把你的数据请求与controller解耦，这是很有帮助的。
并且，在resolve函数中，你也可以像往常一样注入service（如果你需要某些数据在controller执行之前被优先获取，这也很有用）。需要注意的是，如果你把controller作为ng-controller属性来设置，那resolve块就会被跳过而不执行（因为它依赖于页面中的route而不是controller绑定）

$route变量能够被直接注入到任何的controller里，并且能被用来得到当前路由的信息


```javascript
var Ctrl = function($scope, $route) {
  $route.current.templateUrl; //the URL of the template
  $route.current.params; //same as $routeParams
  $route.current.controller; //the name of the controller (Ctrl)
};
Ctrl.$inject = ['$scope','$route'];
```

###你应该使用自定义Services

自定义services能够使Angular项目代码高可管理性与简易测试性。通过使用 Angular的依赖注入功能，你可以在你的应用的任何地方构造一个自定义service，然后非常简单地在别的地方引用它。举个一个能够体现service共享价值的简单例子，是使用这一概念来构造一个适用于你的应用的特殊的$http service。

```
App.factory('myHttp',['$http',function($http) {
  return function() {
    get : function(url, success, fail) {
      $http.get(url).success(function(response) {
        return response.data;
      }).error(fail);
    }
  };
}]);
//this service can now be called by doing...
$myHttp.get('/path', function(data) {
  alert(data);
});
```

此外，下面是一个关于数据是怎么在同一个model的service之间通信的简介

```
App.factory('myFoo',['$http',function($http) {
  //any variables defined in this area will be ACCESSIBLE
  //within the any of the other services which are defined
  //within the same module. So if a variable called foo...
  var foo = 'bar';
  //then foo can be accessed in another service. Be sure to
  //keep this in mind since it's hard to debug
  return foo;
}]);

App.factory('myBar',['$http',function($http) {
  var bar = 'bar2';
  return foo.toString() + bar; //this should return either bar2 or barbar2
}]);
```

当一个service被创建的时候，你也可以注入任意一个你自己的service到任何一个其他的service里。这对代码复用和测试非常有用。


#####显示、隐藏、加载前隐藏、初始化四种指令

你会发现在模板里用显示或者隐藏指令赋值会很难用，因为你不能引来服务端的程序语言去构建你的模板（模板是静态的）。这里是一个用php语言写的一个简单的模板渲染样例。



```php
<div class="session">
  <?php if($isAdmin) { ?>
    <span class="admin">Hello Admin</span>
  <?php } else { ?>
    <span class="user">Hello User</span>
  <?php } ?>
</div>
```

同样的效果用angular来写是这样的


```html
<div class="session">
  <span class="admin" data-ng-show="isAdmin">Hello Admin</span>
  <span class="admin" data-ng-hide="isAdmin">Hello User</span>
</div>
```

只要这样进行数据绑定就可以了
```javascript
$scope.isAdmin = true; //or false or whatever
```

这样可以实现效果，但是当网页仍然在下载的过程中（当第一次加载的时候）你可能可以同时看到两个效果，所以我们要解决这个问题，就需要用到ng-cloak指令

```html
<div class="session ng-cloak">...</div>
```

然后定义这样一段css

```css
.ng-cloak {
  /* this will change to block when scope and angular is ready */
  display:none;
}
```

哦，还有，如果你想要直接在HTML中设置isAdmin的值，就需要跟下面样例一样使用data-ng-init

```javascript
<div class="session ng-cloak" data-ng-init="isAdmin=false;">
  <span class="admin" data-ng-show="isAdmin">Hello Admin</span>
  <span class="admin" data-ng-hide="isAdmin">Hello User</span>
</div>
```

data-ng-init属性适用于变量预设定。这种语法就像你直接在JavaScript中设定值一样方便（因为它使用eval来计算属性中的文本）。

这不是很神奇么！

#####捕捉错误

捕捉异常是一个在产品开发过程中非常重要的一部分。接下来是介绍一些方法去做捕捉异常。

捕捉所有的路由（用otherwise方法）

尽管这个已经是已经一个用来设置主页的常用的方法，但是这个方法最好还是用来作为你404页面的路由。

```javascript
$routeProvider.when('/404',{
  controller : ErrorCtrl
});
$routeProvider.otherwise({
  redirectTo : '/404'
});
```

当你的路由发生了错误

在一个路由产生错误的事件之后（可能是由于丢失了模板URL或者其他问题）你可以在你的作用域里捕获这个事件，你只需要像下面一样写：

```javascript
App.run(['$rootScope','$location',function($rootScope, $location) {
  $rootScope.$on("$routeChangeError", function (event, current, previous, rejection) {
    //change this code to handle the error somehow
    $location.path('/404').replace();
  });
}]);
```

http请求的外层服务

更早的时候在第一篇文章里我解释了自定义service对于代码复用的重要性。当你设置了一个自定义service去包含在你的ajax请求外层的时候你可以在错误传递到其他部分之前捕捉到他们。

```javascript
App.factory('myHttp',['$http','$location',function($http, $location) {

  var onEmpty = function() {
    window.location = '/404';
  };

  return function() {
    get : function(url, success, fail) {
      $http.get(url).success(function(response) {
        var data = response.data;
        if(!data) {
          onEmpty();
          return;
        }
        success();
      }).error(onEmpty);
    }
  };
}]);
```

确保你只是在获取你应用程序内部的资源和数据的时候用这个方法（例如一个特定view里的JSON数据）

#####关于Loops的更多介绍

Loops是一个Angular里一个棘手的又有趣的东西。更多关于Loops的介绍我们已经在之前的文章里提到过了，但是还有一些东西我们还没有提到。

为了在angular中得到一个loop的索引，你可以直接从他的```$index```变量里直接得到。

```javascript
<ol>
  <li data-ng-repeat="option in options">
    <h2>Option #{{ $index + 1 }}: </h2>
  </li>
</ol>
```


用```$index+1```是因为这个值是从0开始计算的

这个默认语法依赖在你的作用域里的已经定义好的一个数组。但是当你并没有设置好一个数组并且你只是简单的想简单的用最大值和最小值构造一个表格会发生什么呢？你需要去设置一个准备好了循环数组的过滤器（filter）。这里有个例子：

```javascript
App.filter('range', function() {
  return function(input, total) {
    total = parseInt(total);
    for (var i=0; i<total; i++) {
      input.push(i);
    }
    return input;
  };
});
```

然后你可以在你的循环里用一个filter去获取它。（下面的代码会创建100个div标签，从0到99）

```javascript
<div ng-repeat="n in [] | range:100">
  {{ $index }} - do something
</div>
```

记住还有很多其他的选项可以用，例如```$first```、```$middle```、```$last```。所有这些都在angular的文档里提到了，你们可以去看。

#####跟踪URl和路径

为了获取到当前页面的路径无论是否有hash改变或者是一个直接的路径（不管是不是从历史记录里取取到的），你可以直接从```$location```里取到。

```javascript
var path  = $location.path();
var url   = $location.absUrl();
var hash  = $location.hash();
```

为了跟踪URL的变化，你需要去设置一个轮询事件

```javascript
$scope.$watch('$location.path()', function(path) {
  //new path!
  alert(path);
});
```

此外，你还可以明确的在你的作用域里设置这些事件。

```javascript
$scope.$on('$locationChangeStart', function(event, newUrl) {
  alert('new location');
});
```

#####过滤器和自定义的过滤器

有两种方式在Angular里定义一个filter：你可以定义一个filter或者一个service。

定义filter

```javascript
App.filter('my', function() {
  return function(data) {
    return data;
  };
});
```

或者你可以这样定义一个service

```javascript
App.factory('myFilter', function() {
  return function(data) {
    return data;
  };
});
```

你可以直接的在这个HTML里使用filter

```html
<span class="some-data">{{ value | my }}<span>
```

或者你可以在你的service里或者controllers直接通过依赖注入使用filter

```javascript
App.factory('someService', ['$filter', function($filter) {
  return function(data) {
    return $filter('my')(data);
  };
}]);
```



#####更多关于Directive的内容

Directives通常是用一个指令块的link方法来实现的。但是这里还有其他很多方法设置指令选项。

```javascript
App.directive('myDirective', ['$location', function($location) {

  return {
    restrict : 'ECA', //Element, Comment and Attribute instances are all scanned
    scope : {
      //these values will be apart of the scope variable passed into the link method
      key : 'value',
      key2 : 'value2'
    },
    compile : function() {
      return {
        pre : function() { ... }, //this is called before the directive element is attached to the DOM
        post : function() { ... } //this is called after the directive element is attached to the DOM (same as link)
      };
    },
    link : function(scope, element, attrs, controllerObject) {
      //this is what is normally used and is the same as the compile:post function.
    }
  };

}]);
```

你可以通过使用一个函数来设置指令避免这么杂乱的代码。这和往link里传递一个hash值是一样的。

```javascript
App.directive('myDirective', function() {
  return function($scope, element, attrs, controller) {
    //less code is nice
  };
});
```

还有很多的关于指令的内容，但是百分之90的内容都需要你在用到的时候才会了解。



#####表单和表单验证

######快速的介绍表单模型和表单绑定之间的区别

在之前的文章里我们介绍了绑定和表单输入。但是，当你有一个表单字段输入需要追踪。（例如一个input字段或者一个textarea）。AngularJS需要去意识到有一个双向的关系在字段之间，所以如果可以的话（就是说如果对你有用的话）可以直接设置表单字段也行，不需要在用{{value}}}这样的字符串了。

```javascript
<input type="text" data-ng-model="name" />
<textarea data-ng-model="name"></textarea>
```

如果因为某些原因没有起作用那你还可以坚持使用{{ value }}没关系，但是他可能没法及时根据他的作用域更新因为这不是一个双向数据绑定。

######表单验证

AngularJS并不提供很多表单验证方法，你期待的表单验证的插件也木有，但是他确实提供了足够的支持你去验证你的表单的东西，并且他的数据模型能在数据提交之前做很多事。如果你已经准备在angular之外用其他的表单验证程序的话那就不要再用angular的表单验证了，下面是它的用法。


```javascript
<form novalidate class="simple-form">
  <ol class="fields">
    <li>
      <label for="input-name">Name:</label>
      <input id="input-name" type="text" data-ng-model="name" />
    </li>
    <li>
      <label for="input-email">Email:</label>
      <input id="input-email" type="email" data-ng-model="email" />
    </li>
  </ol>
  <button data-ng-click="submit()">Submit</button>
</form>
```

这个基础的验证是检查是否是空值或者email是不是一个合法的email。一旦合法，那么这个数据就会通过验证并且被提交。还没有用太多的这些功能，但是效果不错。同样，如果你直接用```input type="submit"``` 或者 ```input type = "botton"```去提交数据可能会不起作用。（因为那是通过本地提交的数据，遇到情况请截图发给我。）

#####Internationalization 和 Localization

Internationalization是一种设计用来映射从你的应用内部到应用外部的逻辑的独立转换方式。Localization也很相似，但是他是设计用来转换序列化数据（比方说数字和日期）到本地的格式的，（最好的一个举例就是日期格式化）。

Angular并没有被设计成拥有完善的internationalization和localization工具，因为在你的应用服务端可以解决这个问题。但是他还是提供了很多有用的特性。


######多元化（使用ngPluralize）

让我们先说如果你有一个页面需要用一个方便人类可读的方式列出你收件箱信息还有你想去显示的信息总数。


```javascript
<div data-ng-pluralize 
  count="messagesCount"
  when="{
    '0' : 'No have no messages in your inbox',
    '1' : 'You have one message in your inbox',
    'other' : 'You have {{ messagesCount }} messages in your inbox'
  }"></div>
```

理想化的说，你不想直接地把英语转换到你自己的网页上所以你可以选择这个方法，然后转换可以在一个全局的转换列表里进行查询。

```javascript
<div data-ng-pluralize 
  count="messagesCount"
  when="{
    '0' : translate('message.zero'),
    '1' : translate('message.one'),
    'other' : translate('message.many', { count : messagesCount })
  }"></div>
```

你不得不去定义你自己的查询因为这件事angular不会帮你做。

```javascript
var LOOKUP = {
  'messages.zero' : 'No have no messages in your inbox',
  'messages.one' : 'You have one message in your inbox',
  'messages.many' : 'You have %messagesCount% messages in your inbox'
}
var translate = function(key, args) {
  var data = LOOKUP[key];
  if(data) {
    for(var key in args) {
      var value = args[key];
      key = '%' + key + '%';
      data = data.replace(key,value);
    }
    return data;
  }
  return '';
};
```

######格式化数据和时间

为了用angular去格式化数据和时间，任何的Date对象或者日期字符串能够通过一个filter处理。

```javascript
<!-- this shows up as Sep 9, 2012 -->
<span class="string-date">{{ '2012-09-01' | date:'medium' }}</span>

<!-- same thing but longer and using a variable  -->
<span class="string-date">{{ someDateVariable | date:'fullDate' }}</span> 

<!-- and you can set your own --> 
<span class="string-date">{{ anotherDateVariable | date:'EEEE, MMMM d,y' }}</span> 
```

你也可以直接在你的controller和service里使用日期的filter，只需要用```$filter```就好。

```javascript
var dateFilter = $filter('date');
var mediumDateString  = dateFilter('2012-09-01', 'medium');
var fullDateString    = dateFilter(someDateVariable, 'fullDate');
var anotherDateString = dateFilter(anotherDateVariable, 'EEEE, MMMM d,y');
```


#####Promises是怎么工作的

Promise一般来说就是一串垂直（在语法层级上相同）的回调，而不需要函数层状缩进。
如果程序的某一部分需要在某些处于另外代码块的功能完成之后再运行，那Promise会有所帮助。举个例子，在执行一个带后台权限的HTTP GET操作之前，先检查用户会话是否仍然active（这可能包含额外的HTTP调用）。如果要在不适用Promise的情况下使上述例子正常工作，那将会是一个编码噩梦（许多的回调，以及无价值的编码困难）

AngularJS的Promise组件```$q service```能够高效地处理这些东西。$q是受Kris Kowall的Q的启发，并且拥有相似的功能（注指API和行为）。通常，任何依赖回调的事情，都会被封装进一个defer/promise循环，然后一旦特定的promise被处理完成了（要么reject()，要么resolve()），那就会继续处理队列中的下一个被延迟的promise请求。只要promise之间相互链接（就好像队列中的节点一个连着一个），那所有东西都能按部就班一个接一个地加载。

```javascript
var lastPromise, defer = $q.defer();
function runCommand(success, fail) {
  if(!lastPromise) {
    lastPromise = defer.promise; 
  }
  lastPromise =  lastPromise.then(success, fail);
}
```

这会创建一个promise的链式结构循环的链接在一起。


```javascript
runCommand(
  function(anyArguments) {
    //Success! Resolved!
  },
  function(anyArguments) {
    //Failure! Rejected!
  }
);
```

现在当你运行 defer.reject()或者defer.resolve()，然后这些东西就会运行。如果你运行了resolve命令那么所用的在队列里的东西都会在第一个方法之后运行（成功的方法）并且如果你运行了reject命令之后所有东西都会在第二个方法后运行（失败的方法）。

```javascript
if(allIsGood) {
  defer.resolve(anyArguments); //runs success()
}
else {
  defer.reject(anyArguments); //runs fail()
}
```

他本身的功能和运行一系列嵌套在一起的回调函数是一样的。如果任何在上层的方法失败了，那么所有嵌套的子方法都会失败。唯一的区别就是你可以更好的读懂代码。Angular也使用这个内部的拦截器特性。



#####引入和自定义Html View

######引入（使用 ng Include）

如果你有一个HTML页面的区块需要引入另一个片段代码那么你就可以使用这个```data-ng-include```的属性

```javascript
<div data-ng-include src="/some/path/to/a/template.html">...</div>
```

你甚至可以定义一个属性并且不需要src属性然后就可以用这个属性直接引入了。

```javascript
<!-- this is same as above -->
  <div data-ng-include="/some/path/to/a/template.html">...</div>
```

上面这行代码会在页面加载完毕之后被angular解析。所以当你已经获得了数据之后再让这个代码加载。

```
<div data-ng-show="someVariable" data-ng-include="someVariable"></div>
```

然后当你需要显示这个引入内容的时候只需要设置```$scope.someVariable``` 变量一个值就可以了。

一点这个引用的内容已经下载完毕了并且加载完毕了那么```$scope.$on('$includeContentLoaded')```将会被广播。而且，如果你在同一个元素上使用了一个onload属性那么这个属性将会被评估一次一旦这个引用准备好了。


#####自定义HTML和$compile

要是你经常碰到一些情况，需要把你自己的HTML编译成view（比如模态窗口，弹框等），那么你就应该考虑使用$compile service对你的程序做一点点hack

让我们假设，你想要加载一个模态窗口，并且通过运行一个叫ModalCtrl的controller来控制其窗口内容。你的HTML就像下面的那样。

```javascript
<div data-ng-controller="ModalCtrl" id="modal-container">
  <header>
    {{ header_title }}
  </header>
  <div class="content">
    {{ content }}
  </div>
</div>
```

然后你的controller就会看起来像这样

```javascript
var ModalCtrl = function($scope) {
  $scope.header_title = 'My Modal';

  $scope.content = '...';
};

ModalCtrl.$inject = ['$scope'];
```

现在在你的应用的某些地方你有一个指令去加载这个模态框。首先它会下载他的内容然后编译。一旦编译完成这个controller会自己运行。

```javascript
App.directive('modalLink', ['$http','$compile',function($http, $compile) {

  return function($scope, element, attrs) {
    element.click(function() {
      $http.get('/some-modal.html').success(function(html) {

        //you may need to trim the whitespace around this if you're using
        //jquery since sizzle breaks if you dont...
        if($ && $.trim) html = $.trim(html); 
        if(html.trim) html = html.trim(); //or MooTools

        //this is custom HTML you have fetched 
        var topScope = angular.element(document).scope();
        var elm = $compile(html)(topScope); //you should provide the top level scope so that it can find the modal in between

        //now just stick this somewhere in your body as an element
        document.body.appendChild(elm);
      });
    });
  };

});
```

现在angular将会识别这个模态框的HTML并且这个controller会在它的data-ng-controller属性里设置。然后他会通过代码运行并且执行ModalCtrl的controller。现在所有的包含在ModalCtrl里的逻辑都会被直接的应用到这个模态框容器的HTML里。（就是那个id是modal-container的html元素）

这里有更多的关于编译HTML的东西。angular的指令里的模板和模板URL属性都在后台为了准备controller的模板而使用着这些东西。

#####内联模板

如果你计划大量的在你的应用内复用模板，那么你需要使用内联模板（script标签模板）。通过设置一个script元素的type属性为“text/ng-template”然后你就能设置你的模板数据并且接下来可以在任意地方可以直接用它。这里是个例子：

```javascript
<script type="text/ng-template" id="heading-template">
  <h5>Hello There </h5>
</script>
```
现在你可以通过使用ng-view或者ng-include去召唤这个模板。在script标签里的数据将会被运用到这个模板的html里。

```javascript
<!-- 
  keep in mind that there are quotes around the 'heading-template' 
  so that angular won't confuse it with a variable
-->
<div class="something" data-ng-include="'heading-template'"></div>
```

如果你设置了在HMTL里设置了模板为空然后在一个include或者template使用了这个路径去指向这个模板的ID，那么AngularJS将会像其他的模板一样从你的服务器上下载模板。创建一个空的ng-template script标签这点确实不重要因为有没有这个属性其实没有任何区别。AngularJS如果没有在缓存里找到这个模板的缓存就会继续从服务器上下载这个模板（不管他有没有设置templateUrl）。

在一个拥有很多不同元素的页面里使用内联模板是最棒的。举个例子，有个项目列表，每个项目都由好多个局部元素构成。你可以直接预取每个模板，把它们保持在缓存中，而不是在模板响应时再去加载局部模板元素。你也可以在其他模板中定义内联模板（然后如果他们下载后就会待在模板缓存中）

#####怎么确保你的指令在你的作用域准备完成之后才运行

一旦模板加载，你的controller以及于controller之前的模板内的所有directive都会被执行。意思是，如果你需要在你的directive渲染前加载一些数据，你需要确保一旦$scope具备了controller中获取到的数据，你的directive就立即执行。另一个问题是插件可能过早加载，比如你需要数据在插件初始化之后再加载。意思是，你的插件可能显示DOM中的旧数据（可能是原始绑定数据）因为新的数据并不是一开始尽在模板中。要处理好这个，你需要让你的插件具备更新自身加载新的DOM数据的能力。这你本来不应该处理，应该是自动的。

为了处理好这个问题，你需要通过$scope.$watch()方法轮训某些特定的变量比如$scope.isReady，但有时候会出问题。相比，在$rootScope中使用$q提供的promise，是一个更好的解决方案。

下面是本文最开始的一段代码，但是因为这段代码比较复杂所以已经把他放在了github上。这里是一个关于怎么用这个插件去确保directive是在你的controller之后运行的例子。

```javascript
//your controller
var Ctrl = function($scope, $http) {
  $scope.$prepareForReady();
  $http.get('/some.json')
    .success(function(json) {
      var someArgs = [json.data];
      $scope.$onReady(someArgs);
    })
    .error(function() {
      var someArgs = [null]; //just some argument
      $scope.$onFailure(someArgs);
    });
};

//your directive
module.directive('myDirective', function() {

  return function($scope, element, attrs, controller) {
    $scope.$whenReady(
      function(someArgs) { //called when $scope.$onReady() is run
        element.html('your data was loaded fine');
        element.show();
      },
      function(someArgs) { //called when $scope.$onFailure() is run
        element.html('something went wrong when fetching the data');
        element.show();
      }
    );
  };

});
```

这个指令的html和其他的没什么区别

```html
<div class="something" style="display:none" data-my-directive>
  ...this data will get replaced once it's ready...
</div>
```



#####总结：

现在就是这样。感谢每一位对之前文章感兴趣的朋友。希望能够让大家对angular背后的知识得到更多的填充。Angular是一个神奇的工具，谢谢！

