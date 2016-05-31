title: 基于React + webpack的组件化编程和自动化构建实践
date: 2015-12-29 10:03:00
tags:
- react
- webpack
- webpack-dev-server
- react-hot-loader
categories:
- WEB前端

---

作为React入门级的新手，在开始接触React的时候，其实并没觉得React具有多大的优势，理论在实践面前是脆弱的，只有真正应用到工程中，才能知道技术框架有几斤几两重。当真正将工程完全使用React的思想来开发时，才慢慢理解它的原理和优势。而webpack是目前相对于其他构建工具来说与react结合比较完美的工具，经过一段时间在项目中使用react+webpack的经验，这里想分享一下使用这套方案的具体细节，同时也想听听大家的建议，看看这套实践有什么地方需要改善。

<!-- more -->

### Why React ###

在web前端开发过程中，我们经常要处理的一个场景是，将变化的数据反映到前端UI上，我们用的最多的应该是基于jquery的dom节点操作。复杂和频繁的DOM操作通常是前端性能瓶颈产生的原因，如果将数据的变化高效地反映到用户界面上，这就是React所要解决的问题。

React引入了虚拟DOM（Virtual DOM）的机制，它在内存中构造了一个虚拟的DOM树，基于React开发时所有的DOM构造都是在这棵虚拟的DOM树上进行的，这棵DOM树只有当mount（可以理解为安装或载入）到UI上某个容器中，才会渲染成真正的浏览器DOM节点。而每当数据发生变化时，React都会重新渲染整个虚拟的DOM树，并将新的DOM树与上一个状态的DOM树对比，将真正需要变化的部分更新到浏览器实际的DOM节点上。简单来说，React只是对diff部分进行实际的DOM更新。

*可能有人会说，每次数据变化都要构建新的DOM树，性能岂不是很差？*

事实上React构建的DOM树是内存数据，所以性能是极高的，真正的DOM操作只是每次变化的那一小部分。

*可能又有人会说，我有用jquery同样是可以修改真正发生变化的那一小部分，为啥我要用React？*

撇开React底层在操作DOM节点是否真的使用了性能极高的方式不说，使用React开发组件与直接使用jquery操作DOM节点的不同还在于，React简化了开发的逻辑，我们不需要关心某一次具体的数据变化如何更新某一个或多个具体的DOM节点，我们只需要关心在任一数据状态下，组件是如何渲染的，也就是说我们关心的是抽象的渲染逻辑而并非具体的DOM操作。

简单来说，当数据发生变化时，使用jquery的通常做法是查找具体的DOM节点，修改DOM节点的内容，而使用React的做法是更新组件的状态，触发它重新渲染，而真正的DOM节点操作由React框架去负责。这样的好处在于简化了程序员的开发，有利于代码的维护。想象一下如果页面的逻辑很复杂，当出现问题时，我们很难找到bug是由哪部分的DOM操作导致的，因为jquery可以随便操作DOM。

React引入组件化开发思想，这里说的组件，指的是封装起来具有独立功能的UI部件，React提倡以组件的方式去组织整个页面，将UI上一些功能相对独立的部分封装成一个个组件，小组件嵌套组合成大组件，最终构成整个用户界面。组件化开发的好处很明显，高复用，松耦合，易维护。


### Why Webpack ###

一个基于react的前端工程，使用什么模块管理和构建工具比较好呢？网上随便搜索，可能就只有webpack是不二首选了。

尤其是react-hot-loader的存在，将webpack推上了react的最佳拍档的地位。

react-hot-loader是一个实现react组件热加载的webpack插件，人家github首页都说了


*To use React Hot Loader in an existing project, you need to*

*switch to Webpack for builds (instead of RequireJS or Browserify);*


意思是要用老子，麻烦丢弃requirejs和browserify那套东西，用webpack！


我们都知道Javascript模块管理的两大规范：CommonJS和AMD，前者模块的同步加载规范，主要用在服务端NodeJS，后者是模块的异步加载规范，主要应用在客户端，通过requirejs等工具实现。

CommonJS这种同步的规范为什么不能直接用在客户端？原因很简单，客户端加载远程模块是有网络延迟的，这就决定我们只能用异步回调的方式，发出依赖请求，继续执行下面的逻辑，等模块完全加载完毕后再执行回调，这就是所谓的AMD。

写过Node的童鞋都知道，CommonJS定义模块和引入模块的方式是如此优雅（相对于requirejs编写回调的方式），而且现在npm称为主流的javascript组件库的发布平台，越来越多成熟的项目也发布到npm上。如果我们想要在浏览器端使用CommonJS的方式优雅地开发模块，随心所欲地引入第三方的npm模块，或者如果工程中在服务端用了react渲染组件HTML返回到前端的方式，现在要将react组件无缝迁移到客户端，等等场景，我们要怎么做？于是诞生了类似browerify这样的工具，开发时可以用编写node模块的方式开发，打包时会自动转换为AMD异步的方式在浏览器上执行。

而webpack也可以实现browerify的功能，除此之外，webpack还具有很多特性：

1. 同时支持CommonJS和AMD两种方式
2. 将所有静态都视为模块，这里所说的静态资源包括npm模块，js模块，css/less/scss文件，图片文件，字体文件等等。也就是说我们可以在js中用require直接引入css/less/scss、图片等等
3. 提取各个模块的公共部分，充分利用浏览器缓存
4. 提供多种插件和加载器，支持对less/scss，jsx，es6，coffeescript文件的加载。grunt/gulp能做到的，webpack基本都可以做到，包括实时打包、对sourcemap的支持等等。


### webpack配置和执行 ###

#### webpack配置 ####
 
类似grunt和gulp，webpack也是通过运行一个配置文件来执行项目的构建。webpack配置文件的默认名称是webpack.config.js，它是一个node模块，所有的配置在exports的对象中设置。

一个简单又不失完整的webpack.config.js如下：

	var path = require('path');
	var webpack = require('webpack');
	
	module.exports = {
	    entry: [
	        './app/main.js'
	    ],
	    devtool: "source-map",
	    output: {
	        path: path.join(__dirname, '/build/'),
	        publicPath: '/build/',
	        filename: "[name].bundle.js"
	    },
	    plugins: [
	        new webpack.optimize.CommonsChunkPlugin("common.chunk.js")
	    ],
	    module: {
	        loaders: [
	            { test: /\.js$/, loader: 'babel-loader' },  // 加载jsx和es6文件
	            { test: /\.less$/, loader: 'style-loader!css-loader!less-loader' }, //加载less文件
	            { test: /\.css$/, loader: 'style-loader!css-loader' },     // 加载css文件
	            {
	                test: /\.(ttf|otf|eot|svg|woff)(\?v=[0-9]\.[0-9]\.[0-9])?$/,
	                loader: "url-loader?limit=10240"
	            },     // 加载字体文件，允许的最大文件大小为10k
	            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192' }   // 加载图片文件，转换为inline base64数据，允许最大的文件大小为8k
	        ]
	    },
	    resolve: {
	        extensions: ['', '.js', '.css', '.less', '.ejs']     // 定义在该数组中的文件后缀不需要显示给出
	    }


**配置解析**

**entry**： 定义入口文件，webpack会从这些入口文件触发，将文件中所有require的模块（包括js模块，样式，图片等）都打包进来。webpack建议每个页面使用一个入口js文件，页面使用到的所有资源都在这个入口文件中require进来，webpack可以根据这个入口文件将所有资源打包进来。

**output**：
 
 - path：打包后文件存放的绝对路径
 - filename: 打包后的文件名，filename: "[name].bundle.js"表示将入口文件打包成 -> 原文件名.bundle.js
 - publicPath：网站运行时的访问路径。例如上面定义的图片加载器，所有小于8k的图片都会转换成内链base64数据，而超过8k的图片会直接复制到目标路径下，并在代码中用publicPath去查找

**plugins**：定义打包过程中所使用到的所有插件，这里用到CommonsChunk插件，它会将所有模块的公共部分独立出来生成一个common.chunk.js文件，利用浏览器本身的缓存，避免公共部分在不同页面被重复加载。webpack还自带了很多其他插件，例如代码压缩混淆插件UglifyJsPlugin。

**loaders**：定义文件加载器，webpack自带了很多加载器，如babel-loader,less-loader,url-loader等，多个加载器用!来实现管道处理

**resolve**：定义在extensions数组中的文件后缀在require的时候无需显示给出

**devtool**：使用的开发工具，上面用到sourcemap，便于开始时在浏览器中调试。


#### 执行打包 ####

执行webpack命令，会自动读取webpack.config.js文件进行打包
如果要执行其他文件名：

	webpack --config webpack.config.dev.js

-w可以实现监控文件的变化实时打包：
	
	webpack -w


#### 举个栗子 ####

举个简单的实践例子。假设有如下项目文件结构：

![](http://7x00n0.com1.z0.glb.clouddn.com/react-img.png)


index.html是一个demo页面，scripts/main.js是这个页面的入口脚本文件，styles/main.css定义了这个页面的样式。index.html中引入打包后入口文件main.bundle.js：

	<html>
	<head></head>
	<body>
	<section></section>
	<script src="/build/main.bundle.js"></script>
	</body>
	</html>

main.js的逻辑如下，其中Hello是一个react组件：

	requre('../styles/main.css');     // 引入样式
	var React = require('react');
	var Hello = require('./hello.js');

	React.render(<Hello />, document.queryForSelector('section'));

hello.js逻辑如下：

	var React = require('react');
	var Hello = React.createClass({
     render: function() {
          return (
               <p>Hello, this is a webpack demo</p>
          );
     }
	});
	module.exports = Hello;



### 项目实践 ###

目前我所负责的一个前端工程就是基于react+webpack这套方案，工程的组织和webpack的配置如下：

- 每个页面只使用一个脚本作为入口文件，所有入口文件都配置在一个名为entry.config.json的文件中，由webpack config文件去读取。

如：

	{
	    "tag_apply.index": "./web/scripts/tag_apply/index.js",
	    "tag_apply.tags": "./web/scripts/tag_apply/tags.js",
	    "tag_apply.my_tags": "./web/scripts/tag_apply/my_tags.js",
	    "tag_apply.approval": "./web/scripts/tag_apply/approval.js",
	    "athena.feedback": "./web/scripts/athena/feedback.js",
	    "athena.index": "./web/scripts/athena/index.js"
	}

entry.config.json文件其实就是webpack中entry的属性值，指定了所有页面的入口文件名和路径。

webpack config文件中，用require引入该文件：

	entry: require("./entry_config.json")


- HTML页面直接引入打包后的bundle文件名，以及使用CommonsChunk独立出来的公共部分脚本。

如：

	<script src="/build/common.chunk.js"></script>
	<script src="/build/hello.bundle.js"></script>

- 页面所依赖的所有react组件，less样式文件都在页面对应的入口脚本中通过require引入。

- react所依赖的模块和组件自身的样式文件，在react组件中通过require引入。

- 所有js模块都使用CommonJS的方式去编写。

- 开发时用一个webpack config文件（工程中我命名为webpack.config.dev.js），发布时用另外一个webpack config文件。
二者主要的区别是，开发用的配置文件我添加了sourcemap工具，便于开发时的调试，发布用的配置文件我配置了文件名的hash处理，js代码的压缩混淆，以及修改HTML文件中对静态文件的引用名称的工具。


下面我贴出两个文件的内容：

webpack.config.dev.js （开发时的webpack配置，开发过程中用-w实时打包）：

	var path = require('path');
	var webpack = require('webpack');
	var fs = require('fs');
	var glob = require('glob');
	var CommonUtil = require('./web/scripts/common_util.js');
	
	var sDistPath = '/build/';
	
	// 清除build文件夹下的所有文件
	console.log('Clean build files...');
	glob('web' + sDistPath + '*', {}, function (er, files) {
	    files.forEach(function (file) {
	        console.log('delete file:' + file);
	        fs.unlinkSync(file);
	    });
	    console.log('Clean done.');
	});
	
	module.exports = {
	    entry: require("./entry_config.json"),
	    devtool: "source-map",
	    output: {
	        path: path.join(__dirname, 'web/', sDistPath),
	        publicPath: CommonUtil.contextPath + sDistPath,
	        filename: "[name].bundle.js"
	    },
	    plugins: [
	        new webpack.optimize.CommonsChunkPlugin("common.chunk.js")
	    ],
	    module: {
	        loaders: [
	            { test: /\.js$/, loader: 'babel-loader' },  // parse jsx and es6
	            { test: /\.less$/, loader: 'style-loader!css-loader!less-loader' }, // use ! to chain loaders
	            { test: /\.(tpl|ejs)$/, loader: 'ejs'},  // parse ejs
	            { test: /\.css$/, loader: 'style-loader!css-loader' },
	            {
	                test: /\.(ttf|otf|eot|svg|woff)(\?v=[0-9]\.[0-9]\.[0-9])?$/,
	                loader: "url-loader?limit=10000"
	            },
	            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192' }   // inline base64 URLs for <=8k images, direct URLs for the rest
	        ]
	    },
	    resolve: {
	        extensions: ['', '.js', '.css', '.less', '.ejs']
	    }
	};

webpack.config.js（发布时使用的webpack配置）：

	var path = require('path');
	var webpack = require('webpack');
	var revc = require("./webpack-revc");
	var CommonUtil = require('./web/scripts/common_util.js');
	var fs =require('fs');
	var glob =require('glob');
	
	var sHtmlFilePatterm = './web/**/*.html';   // 需要替换引用静态文件名的HTML文件匹配字符串
	var sHtmlFileExcludePattern = './web/lib/**/*.html';    // 不需要替换引用静态文件名的HTML文件匹配字符串
	
	var sDistPath = '/build/';
	
	// 清除build文件夹下的所有文件
	glob('web' + sDistPath + '*', {}, function (er, files) {
	    console.log('Clean build files...');
	    files.forEach(function (file) {
	        console.log('delete file:' + file);
	        fs.unlinkSync(file);
	    });
	    console.log('Clean done.');
	});
	
	
	module.exports = {
	    entry: require("./entry_config.json"),
	    output: {
	        path: path.join(__dirname, 'web/', sDistPath),
	        publicPath: CommonUtil.contextPath + sDistPath,
	        filename: "[name].[hash].bundle.js"
	    },
	    plugins: [
	        new webpack.optimize.CommonsChunkPlugin("common.chunk.js"),
	        new webpack.optimize.UglifyJsPlugin({minimize: true}),
	        function() {
	            this.plugin("done", function(stats) {
	                // 生成stats.json文件
	                revc(stats.toJson(), sHtmlFilePatterm, sHtmlFileExcludePattern);    // 修改HTML中对静态文件的引用名
	            });
	        }
	    ],
	    module: {
	        loaders: [
	            { test: /\.js$/, loader: 'babel-loader' },
	            { test: /\.less$/, loader: 'style-loader!css-loader!less-loader' }, // use ! to chain loaders
	            { test: /\.(tpl|ejs)$/, loader: 'ejs'},  // parse ejs
	            { test: /\.css$/, loader: 'style-loader!css-loader' },
	            {
	                test: /\.(ttf|otf|eot|svg|woff)(\?v=[0-9]\.[0-9]\.[0-9])?$/,
	                loader: "url-loader?limit=10000"
	            },
	            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192' }   // inline base64 URLs for <=8k images, direct URLs for the rest
	        ]
	    },
	    resolve: {
	        extensions: ['', '.js', '.css', '.less', '.ejs']
	    }
	};


由于文件打包后添加了hash值，HTML代码中的引用名也对应进行修改。这里的webpack-revc模块是我自己写的一个实现HTML代码中引用名修改的模块，代码如下：

	var fs = require('fs');
	var glob = require("glob")
	
	module.exports = function (stats, sPattern, sExcludePattern) {
	    /**
	     * 替换文档中的src字符为rev字符
	     */
	    var _fReplaceRev = function (path, src, rev) {
	        var data = fs.readFileSync(path);
	        var newValue = data.toString().replace(src, rev);
	        fs.writeFileSync(path, newValue);
	    };
	
	    console.log('Replacing references in path: ' + sPattern + '...');
	    console.log('File stats: ' + JSON.stringify(stats.assetsByChunkName));
	
	    if (stats && stats.assetsByChunkName) {
	        // 遍历所有满足sFilter匹配的文件，替换文件中引用的静态文件名
	        var oOptions = {};
	        if (typeof sExcludePattern !== "undefined" || sExcludePattern !== null)
	            oOptions = {ignore: sExcludePattern};   // 需要排除的文件
	        glob(sPattern, oOptions, function (er, files) {
	            files.forEach(function (file) {
	                // 替换file中引用的静态文件名称
	                console.log('Replacing references in file: ' + file + '...');
	                for (var name in stats.assetsByChunkName)
	                    _fReplaceRev(file, name + ".bundle.js", stats.assetsByChunkName[name]);
	                console.log('Replace references in file: ' + file + ' done.');
	            });
	        });
	
	    }
	
	    console.log('Replace references done.');
	}


### webpack-dev-server和react组件热加载配置 ###

webpack-dev-server是一个小巧的nodejs expres服务器，webpack-dev-server会根据一个webpack config文件，将静态资源打包后存放在内存，页面访问这些资源直接从内存中读取，所以你会发现，启动webpack-dev-server后，资源打包的目标路径其实是没有东西的。
需要注意的是，webpack-dev-server只适用于简单的静态页面或纯前端的工程（独立于后端服务）。如果你的工程是和服务端程序耦合的，例如java web工程，使用webpack-dev-server会引起跨域的问题，因为静态资源是由webpack server来提供服务，而后端接口又是另外一个服务器提供服务。

安装webpack-dev-server:

	npm install webpack-dev-server --save-dev

启动webpack-dev-server:

	webpack-dev-server --content-base public/

--content-base指定根目录的路径。webpack server会将资源打包到content-base下，并且监听文件的变化，实时打包。

我们也可以自己封装下webpack-dev-server，例如：

	var webpack = require('webpack');
	var WebpackDevServer = require('webpack-dev-server');
	var config = require('./webpack.config');
	
	new WebpackDevServer(webpack(config), {
	  publicPath: config.output.publicPath,
	  hot: true,
	  historyApiFallback: true
	}).listen(3000, 'localhost', function (err, result) {
	  if (err) {
	    console.log(err);
	  }
	
	  console.log('Listening at localhost:3000');
	});


执行node server.js同样可以启动web pack server。

Webpack有一个很实用的功能叫做热替换，可以结合react-hot-loader插件来实现，开发过程中修改react组件代码，不需要刷新浏览器，webpack server会直接帮你将修改同步到浏览器端，实现传说中的live refresh。

react-hot-loader的配置：

1. 安装：

		npm install react-hot-loader —save-dev

2. 将webpack-dev-server添加到entry：

		entry: [
		  'webpack-dev-server/client?http://localhost:3000',
		  'webpack/hot/only-dev-server',
		  './scripts/index'
		]

3. 在output中配置publicPath，web pack server会将资源打包到这个路径下

4. 添加DefinePlugin和HotModuleReplacementPlugin插件：
 
		plugins: [
		  new webpack.DefinePlugin({
		    "process.env": {
		      NODE_ENV: JSON.stringify("development")
		    }
		  }),
		  new webpack.HotModuleReplacementPlugin()
		]

5. 在jsx的loader中添加hot-loader在最前面：

		loaders: [{
		  test: /\.js$/,
		  loaders: ['react-hot', 'babel']
		}]

至此hot－loader就配置完毕，此时可以启动web pack server，然后修改一下react组件，你会发现浏览器没有刷新，但代码已经热替换了。

这里是一个简单的配置示例：
[https://github.com/charleschaochen/reactServerHotLoaderTest](https://github.com/charleschaochen/reactServerHotLoaderTest "https://github.com/charleschaochen/reactServerHotLoaderTest")




总的来说，React是一个全新的前端UI框架，它的优势我认为是两点：

1. 它接管UI开发中最为复杂的局部更新操作，在复杂场景下保证高性能。
2. 引入前端组件化开发思想，将用户界面拆分为多个独立的UI组件，便于复用和维护。

实践证明react+webpack确实是一个不错的前端解决方案，目前使用的还不是很深入，希望通过项目的需求迭代慢慢学习更多相关的东西，收获和坑也会分享出来。

