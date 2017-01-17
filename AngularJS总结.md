#AngularJS总结

##AngularJS介绍

###MVC

MVC==>模型(Model)、视图(View)、控制器(Controller) 

##模块化

###定义应用

ng-app: 可以为任一HTML标签添加该属性，该标签包裹的内容都属于应用(App)的一部分

###定义模块

```js
var App = angular.module('App',[]);
```

###定义控制器

```js
App.controller('DemoController',['$scope',function($scope){
	//代码体
}]);
```
给HTML标签添加ng-controller属性，并赋值相应的控制器名称，就确定了相互关联关系。

以上就是AngularJS的工作模式

##指令

###内置指令

- ng-app: 指定应用根元素，至少有一个元素指定了此属性
- ng-controller: 指定控制器
- ng-show: 控制元素是否显示 true 显示 false 不显示
- ng-hide: 控制元素是否隐藏 true 隐藏 false 不隐藏
- ng-if: 控制元素是否“存在” true 存在 false 不存在
- ng-src: 增强图片路径，避免无效的请求
- ng-href: 增强地址，避免无效请求
- ng-class: 控制类名
- ng-include: 引入模板
- ng-disabled 表单禁用
- ng-readyonly 表单只读
- ng-checked 单/复选框表单选中状态

###自定义指令

```js
var App=angular.module('App',[]);
//自定义指令
App.directive('tag',function(){
	return {
		//自定义指令的类型：E、A、C、M
		restrict:'EA',
		replace:true,
		template:'<h1>hello AngularJS</h1>'
	}
})
```

##数据绑定

AngularJS是以数据作为驱动的MVC框架，所有模型（Model）里的数据经由控制器（Controller）展示到视图（View）中。

所谓数据绑定指的就是将模型（Model）中的数据与相应的视图（View）进行关联，分为单向绑定和双向绑定两种方式

###单向绑定

###双向绑定

###相关指令

ng-bind的简写是{{}}，添加ng-cloak可以解决{{}}绑定数据出现的闪烁现象，通过ng-bind-template可以绑定多个数据

ng-model指令实现视图（View）模板向模型（Model）数据的绑定

ng-init可以初始化模型（Model）也就是$scope

ng-repeat 将数组或者对象数据迭代到视图模板

ng-switch、on、ng-switch-when 可以对数据进行筛选

##作用域

每个控制器对应一个模型也就是$scope对象，不同层级的控制器下的$scope便产生了作用域

###根作用域

$rootScope

###子作用域

ng-controller创建出来的一个子作用域

##过滤器

###内置过滤器

1. currency
2. data
3. filter
4. json
5. limitTo
6. lowercase
7. uppercase
8. number
9. orderBy

###自定义过滤器

```js
var App=angular.module('App',[]);
App.filter('capitlize',funciton(){
	return function(input){
		return input[0].toUpperCase()+input.slice(1);
	}
})
```

##依赖注入

依赖注入是指在运行时自动查找依赖关系，然后将查找到依赖传递给使用者的一种机制

###推断式注入

不推荐使用，压缩会造成依赖找不到

###行内注入

```js
var App=angular.module('App',['$scope','$rootScope',function($scope,$rootScope){
	//代码体
}]);
```

##服务

###内置服务

1. $location是对原生JavaScript中location对象属性和方法的封装

```js
App.controller('DemoController',['$location','$scope',funciton($location,$scope){
	//绝对路径
	$scope.absUrl=$loaction.absUrl();
	//协议
	$scope.protocol=$location.protocol();
	//端口
	$scope.port=$location.port();
	//当前路径
	$scope.path=$location.path();
	//hash值
	$scope.hash=$location.hash();
	//查询字符串
	$scope.search=$loaction.search();
}]);
```

2. $timeout、$interval是对原生JavaScript中的setTImeout和setInterval的封装

```js
App.controller('DemoController',['$timeout','$interval','$scope',function($timeout,$interval,$scope){
	$timeout(funciton(){
		$scope.time=new Date();
	},2000);
	$interval(funciton(){
		$scope.time=new Date();
	},2000)
}]);
```

3. $filter在控制器中格式化数据
4. $log打印调试信息
5. $http用于向服务器发起异步请求

```js
App.controller('DemoController',['$scope','$http',function($scope,$http){
	$http({
		method:'post',
		url:'./example.php',
		data:{name:'itcast',age:10},
		header:{
			'Content-Type':'application/x-www-form-urlencoded'
		}
	}).success(fucntion(info,status,headers,config){

	}).error(funciton(info,status,headers,config){

	});
}]);
```

同时还支持多种快捷方式如$http.get()、$http.post()、$http.jsonp()。

###自定义服务

1. factory方法

```js
App.factory('showTime',['$filter',function($filter){
	var now=new Date();
	now=$filter('date')(now,'yyyy/MM/dd');
	return now;
}]);
```

2. service服务

```js
App.service('showTime',['$filter',function($filter){
	var now=new Date();
	this.now=$filter('date')(now,'yyyy/MM/dd');
}]);
```

3. value方法定义常量

```js
App.value('author','itcast');
```

##模块加载

###配置块

```js
App.config(['$logProvider',function($logProvider){
	$log.debugEnabled(false);
}]);
```

###运行块

```js
App.run(['$http','$rootScope',funciton($http,$rootScope){
	$http({
		method:'post',
		url:'example.php'
	}).success(funciton(info){
		$rootScope.name=info;
	});
}]);
```

run方法是最先执行的，利用这一特点我们可以将一些需要优先执行的功能通过run方法来运行。

##路由

###SPA单页

###路由

```js
<!DOCTYPE html>
<html lang="en" ng-app='App'>
<head>
	<meta charset="UTF-8">
	<title>路由</title>
	<!-- 引入js -->
	<script src='./libs/angular.min.js'></script>
	<script src='./libs/angular-route.js'></script>
</head>
<body>
	<!-- 模板布局 -->
	<header>头部</header>
	<div class="container">
		<div ng-view></div>
	</div>
	<footer>底部</footer>
	<script>
		//实例化模块
		var App=angular.module('App',['ngRoute']);
		//配置路由块
		App.config(['$routeProvider',function($routeProvider){
			$routeProvider.when('',{
				template:'首页'
			});
		}]);
	</script>
</body>
</html>
```

###路由参数

1. when otherwise
2. 第一个参数为字符串 代表hash值
3. 路由参数

	- template 字符串形式的视图模板
	- templateUrl 引入外部视图模板
	- controller 视图模板所属的控制器
	- redirectTo 跳转其它路由
4. 获取参数，在控制中注入$routeParams可以获取传递的参数

##其它

###jQuery

在没有引入jQuery的前提下AngularJS实现了简版的jQuery Lite，通过angular.element不能选择元素，但可以将一个DOM元素转成jQuery对象，如果引提前引入了jQuery则angular.element则完全等于jQuery。

###bower

基于NodeJS的一个静态资源管理工具，由twitter公司开发维护，解决大型网站中静态资源的依赖问题。

1. 依赖NodeJS环境和git工具。
2. npm install -g bower安装bower
3. bower search 查找资源信息
4. bower install  安装（下载）资源，通过#号可以指定版本号
5. bower info 查看资源信息
6. bower uninstall 卸载（删除）资源
7. bower init初始化，用来记录资源信息及依赖。
