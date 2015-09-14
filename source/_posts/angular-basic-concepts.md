title: AngularJS基础概念与核心原理
date: 2015-05-21 12:46:00
tags:
- AngularJS
categories:
- WEB前端

---

AngularJS是这两三年兴起的一款优秀的前端框架，最令我惊讶的是它实现了一些后台框架的思想，如MVC, 双向数据绑定，依赖注入等等，并且支持自定义语义化标签，方便封装一些前端组件。最新项目中用到这个框架，所以开始在看，稍微整理一下Angular的一些基础概念与原理性的东西分享一下。


<!-- more -->

### Angular的一些核心概念 ###

**MVC**

写过后端的都知道什么是MVC，一种常用的Web设计模式，将模型（Model），视图（View），控制器（Controller）分离的一种思想，这里不累述。Angular实现了纯前端的MVC，使得前端应用更加工程化，模块之间耦合性小。

**双向数据绑定(Two-way data binding)**

双向数据绑定，实现视图与模型的双向绑定，即任意一方发生变化，都会同步到另一方。 这种思想使得用惯Jquery的童鞋在刚接触Angular的时候，会感到一些不适应，因为双向数据绑定使我们可以完全抛弃直接去操作DOM的做法，颠覆了传统的DOM操作。举个例子，如果我们要改变页面上某个<span>的内容。使用Jquery的话我们的做法无非是获取这个<span>的DOM，然后修改DOM的text内容。而使用Angular，我们只需要修改这个<span>所绑定的模型数据，就可以自动更新。

**依赖注入(Dependency Inject)**

控制器所使用到的service服务可以由Angular的injector自动注入。

玩过Spring的童鞋都知道，Spring的IoC容器也实现了依赖注入，Spring注入的方式有构造注入，getter/setter注入和反射注入，而Angular使用的注入方式是构造注入，即通过参数的方式传入到控制器的逻辑函数中。Angular本身提供了多种service，例如$scope,$window,$location等等，我们也可以通过service函数自定义服务。

**directive指令**

Angular提供的一套指令集，用于将模型的数据绑定到视图上。

**$scope对象**

$scope对象是一个Angular控制器的数据模型对象，一个控制器所负责的视图绑定的模型数据都是来自$scope对象。

另外有一个$rootScope对象，它与$scope对象的不同在于作用域。每个controller都有自己的scope，所以$scope对象的作用域是所在的controller中，对其他controller不可见，而$rootScope的作用域是整个Angular模块，对该模块下的所有controller可见。

**过滤器**

Angular的指令可以和一些过滤器配合使用，过滤器是用来对模型数据进行格式化或对数组元素进行筛选，或者对某个模型值进行预处理。

### Angular Hello World ###

稍微了解了AngularJS的一些概念之后，按惯例要来一发Hello World。

    <!DOCTYPE html>
    <html lang="en">
	
	<head>
	    <title>Angular Helloworld</title>
	</head>
	
	<body ng-app="myapp">
	
	<div ng-controller="HelloController" >
	
	    <h2>Hello { { helloTo } } !</h2>
	
	</div>
	
	
	<script>
	angular.module("myapp", [])
	    .controller("HelloController", function($scope) {
	        $scope.helloTo = "AngularJS";
	    } );
	</script>
	
	<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.5/angular.min.js"></script>
	
	</body>
	
	</html>

示例中，body标签用ng-app指定了一个Angular模块，表示从这里开始，所有DOM将由Angular来负责渲染和绑定。

示例中视图view的部分就是：

    <div ng-controller="HelloController">	
	    <h2>Hello { { helloTo } }  !</h2>
	</div>

视图通过ng-controller绑定一个控制器，告诉这个视图应该去使用哪个控制器。 helloTo外面的双大括号是Angular的指令符，表示这地方要显示模型中helloTo的值。

我们再来看js代码：

    angular.module("myapp", [])
	    .controller("HelloController", function($scope) {
	        $scope.helloTo = "AngularJS";
	    });

这里定义了一个"myapp"模块，并通过调用controller函数定义了一个控制器，控制器的处理函数中，参数$scope就是所谓的模型，而$scope是通过Angular的injector注入到这个函数中。$scope上定义了一个属性helloTo,它的值将显示在视图上。

创建module的时候，第二个是一个数组。这个数组是干嘛用的呢。 其实是用来声明依赖注入的对象的，如果当前这个module需要依赖另一个module的功能，例如引用到其他module中定义的service，我们可以将所要引用的module名称写到数组中，如：

    var module = angular.module("myapp", ["servModule"]);

这样一来，当前module下所有控制器就可以使用servModule中定义的service了。同样定义controller的时候，我们也可以声明依赖注入的对象，事实上hello world中定义controller的完整写法应该是：

    module.controller("HelloController", ["$scope",function($scope) {
	        $scope.helloTo = "AngularJS";
	    }]);

以上程序告诉了Angular，当前控制器需要一个名为$scope的对象，请帮我注入吧！同样，如果我要用到其他的service，也可以添加到数组中，例如我希望用到servModule下的myServ服务，我们先要在定义module时声明注入"servModule"，正如我们之前所做的，然后要在声明controller的时候，声明注入"myServ":
	
	module.controller("HelloController", ["$scope","myServ",function($scope) {
	        $scope.helloTo = "AngularJS";
	    }]);

这样我们就可以访问myServ的属性和函数了。

Angular就是使用这种依赖注入的方式，实现模型、服务与控制器的分离，我们在实际开发的过程中，可以将一些独立功能的服务封装到一个module中，供其他业务module的controller引用。 类比Java，这里的module其实就是包，service其实就是包中类。


### Angular指令Directives ###

AngularJS提供了一套directives指令将模型的数据绑定到视图上。

常见的指令有：

【普通指令】

嵌入式指令：如 `<span> { { text } } </span>`

ng-app: 指定一个模块

ng-controller: 指定一个控制器

ng-bind: 绑定一个model的属性或函数，所绑定的属性值或函数返回值会添加到所在标签中

ng-bind-html: 如果所绑定的模型值包含HTML标签，Angular默认会将HTML标签转义成普通的文本，防止HTML注入。如果我们确实要输出HTML文档，可以使用该指令。

【条件指令】

作用于条件判断逻辑。

ng-show: 根据所绑定的属性值或函数返回值决定所在dom节点是否显示

ng-hide: 同ng-show，判断是否隐藏

ng-if: 根据所绑定的属性值或函数返回值决定所在dom节点是否被移除

ng-switch: 开关语句，如下：

    <div ng-switch on="myData.switch">
        <div ng-switch-when="1">Shown when switch is 1</div>
        <div ng-switch-when="2">Shown when switch is 2</div>
        <div ng-switch-default>Shown when switch is anything else than 1 and 2</div>
    </div>

ng-include：引入一个页面

ng-repeat：遍历model的对象或数组, 如：

    <span ng-repeat="item in items">{{item}}</span>

	<div ng-repeat="(name, value) in myData.myObject">
	   <div>{{name}}</li>
	   <div>{{value}}</li>
	</div>

【事件指令】

Angular事件用指令的形式指定对应的事件处理逻辑，如：

    <div ng-controller="MyController" >
    <div ng-click="myData.doClick()">Click here</div>
	</div>
	
	<script>
	    angular.module("myapp", [])
	            .controller("MyController", function($scope) {
	                $scope.myData = {};
	                $scope.myData.doClick = function() {
	                    alert("clicked");
	                }
	            } );
	</script>


常用的事件包括：

- ng-click
- ng-dbl-click
- ng-mousedown
- ng-mouseup
- ng-mouseenter
- ng-mouseleave
- ng-mousemove
- ng-mouseover
- ng-keydown
- ng-keyup
- ng-keypress
- ng-change


### 过滤器Filtering ###

有些AngularJS指定支持过滤器Filtering。过滤器以类似于Shell**管道**的方式应用到指令上。

【格式化过滤器】

- date	格式化日期
- currency	格式化货币
- number	格式化数值
- lowercase	小写转换
- uppercase	大写转换
- json	格式化json对象

如：

![](http://7x00n0.com1.z0.glb.clouddn.com/未命名.png)

【数组过滤器】

- limitTo：个数限制
- filter：自定义过滤器

如：

    <div ng-repeat="item in myData.items | filter: itemFilter"></div>

"filter: itemFilter"表示将itemFilter这个过滤器应用到myData.items这个数组上。itemFilter是一个定义在$scope对象上的函数。这个函数接收数组的单个元素作为参数，并返回true或false，表示保留或不保留该元素：


    $scope.itemFilter = function(item){
      if(item.text == "one") return false;
    }


- orderBy

用法：orderBy:sortField:reverse

如：

    <ul np-repeat = "book in books | orderBy:'name':true">

表示按name倒序


【自定义过滤器】

Angular提供filter函数支持自定义过滤器，用来对模型值进行处理。

有一个常见的应用场景就是输出HTML。前面提到，如果视图绑定的模型数据是一个HTML字符串，Angular会默认将其转义为普通文本以避免HTML注入。如果我们确实像输出HTML文档，可以使用ng-bind-html指令。但只有ng-bind-html还不够，我们还需要自定义个信任HTML的过滤器:

	module.filter("trustHtml", ["$sce",function($sce){
        var filterFunc = function(text){
            return $sce.trustAsHtml(text);
        }

        return filterFunc;
    }]);

这里利用了$sce服务的trustAsHtml函数，返回受信任的html文档。
对应的视图代码：
	
	<p ng-bind-html="value | trustHtml"></p>


### Angular监听机制 ###

Angular的模型数据发生变化时，所绑定的视图也会随之发生变化。这是由Angular的监听机制来实现的。
Angular的监听机制由三个函数来实现：**$watch()**, **$digest()**, **$apply()**

**$watch()**

$watch函数会为所要监听的变量创建一个watch对象，这个watch对象由两部分组成：所要监听的变量，以及变量发生变化时执行的监听函数。这两部分分别对应$watch函数的两个参数valueFunction和listenerFunction。

$watch(valueFunction, listenerFunction)

如：

    $scope.$watch(function(scope) { return scope.data.myVar },
              function(newValue, oldValue) {
                  document.getElementById("").innerHTML =
                      "" + newValue + "";
              }
             );

当我们将视图与$scope对象的某个属性绑定时，Angular会自动为$scope的属性调用$watch函数，创建一个watch对象。


**$digest()**

Angular为模型数据创建watch之后，当数据发生变化时，如果去触发监听器函数呢？这个动作将由$digest函数来完成。

$digest函数会迭代$scope上所有的watch对象，当处理一个watch对象时，digest函数会先调用watch对象的valueFunction，如果取到的返回值与就值不一致，digest函数就会调用listenerFunction触发监听函数，更新所绑定的DOM的内容。

Angular会在必要的时机为我们调用digest()函数检查watches，例如angular一些指令触发或ajax请求返回的时候。**但有些情况下Angular是不会自动为我们调用digest()**，这就会导致我们修改了$scope属性但对应的视图没有更新。此时我们需要显式去调用$digest函数。

下面是一个主动调$digest的示例：
    
	<!DOCTYPE html>
	<html ng-app="org.angularstudy.apply">
	<head lang="en">
	    <meta charset="UTF-8">
	    <title></title>
	</head>
	<body>
	<div ng-controller="applyController">
	    <p>
	        <button ng-click="doUpdate()">Update time via directive</button>
	    </p>
	    <p>
	        <button id="updateDt">Update time</button>
	    </p>
	    <p id="datetime">{{datetime}}</p>
	</div>
	<script src="js/angular-1.2.5.min.js"></script>
	<script src="js/jquery-1.8.3.js"></script>
	<script>
	    var module = angular.module("org.angularstudy.apply", []);
	    module.controller("applyController", ["$scope", function ($scope) {
	        $scope.datetime = new Date().toLocaleTimeString();
	        $scope.doUpdate = function () {
	            $scope.datetime = new Date().toLocaleTimeString();
	        }
	
	        $("#updateDt").click(function () {
	            $scope.datetime = new Date().toLocaleTimeString();
	            $scope.$digest();
	        });
	    }]);
	</script>
	</body>
	</html>

我们为第一个按钮绑定了ng-click指令，当click事件触发时，修改了datetime数据，此时Angular会自动为我们调用$digest函数更新视图。但第二个按钮我们是通过jquery去绑定click事件，此时Angular并不会自动为我们调用$digest，我们只有显式去调用$digest才能更新视图。

**$apply**

另外一种触发Angular去检查wathces的方法是$apply，我们可以将修改模型数据的操作放在$apply函数的参数函数中，当$apply函数退出时会触发$digest函数，$scope上所有watches将会被检查。

所以上一个例子，我们也可以通过$apply的方式来取代调用$digest:
    
	$("#updateDt").click(function () {
            $scope.$apply(function(){
                  $scope.datetime = new Date().toLocaleTimeString();
               });  
        });


$watch，$digest，$apply不仅可以应用在$scope对象上，也可以作用在普通变量上。也就是说我们可以利用Angular的监听API来实现自己的监听逻辑。

以下就是一个例子。例子中利用了$watch函数监听输入框值的变化，当值发生变化时，调用$digest触发监听函数。

    <!DOCTYPE html>
	<html ng-app="org.angularstudy.watch">
	<head lang="en">
	    <meta charset="UTF-8">
	    <title></title>
	</head>
	<body>
	<input type="text" id="inputText"/>
	
	<div ng-controller="watchController">
	    <span id="content"></span>
	</div>
	<script src="js/angular-1.2.5.min.js"></script>
	<script src="js/jquery-1.8.3.js"></script>
	<script>
	    var module = angular.module("org.angularstudy.watch", []);
	    module.controller("watchController", function ($scope) {
	        $scope.$watch(function () {
	            return $("#inputText").val();
	        }, function (newVal, oldVal) {
	            console.log("New Value: " + newVal + ", Old Value: " + oldVal);
	            $("#content").text($("#inputText").val());
	        });
	
	        $("#inputText").change(function () {
	            $scope.$digest();
	        });
	    });
	</script>
	</body>
	</html>

关于AngularJS的一些基础概念和核心原理就大概讲到这里，Angular的学习曲线一开始还是比较陡，比较容易上手。后续会分享更多AngularJS的学习笔记和实践经验。
