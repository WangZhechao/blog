#JSHint配置（临时稿）


##Enforcing options(强制的设置）

当下面的条件符合，JSHint会增加检测代码发出的警告。

###`bitwise`

这个设置禁止你在代码中使用位操作，如`^`(XOR)，`|`(OR)等操作符。位操作在javascript代码中使用非常罕见，而且对于`&`和`&&`类似的操作符容易输入错误。

###`camelcase`

这个设置将强制变量名称的输入样式只能为驼峰式命名`variableName`或者带下划线的大写字母`VARIABLE_NAME`。

###`curly`

这个设置允许你强制在每个代码块上使用大括号。js代码允许你类似下面的写法：

	while(day)
		shuffle();

但是这种写法非常容易导致错误，如下面的情况（你可能想在代码while循环中sleep）：

	while(day)
		shuffle();
		sleep();

###`enforceall`

该设置将会使强制使现在所有的Enforcing配置生效，并禁用所有的Relaxing设置。

###`eqeqeq`

该设置禁止使用`==`和`!=`操作符，而是使用`===`和`!==`。前者比较的时候会将前后元素进行转换后比较，可以会使结果和原本代码中表达的意思不一致而导致问题的发生。

###`es3`

该标记强制代码使用ECMAScript3规范，这为老式浏览器的兼容性而准备的(IE 6/7/8/9)。

###`es5`

该设置允许你使用ECMAScript5.1规范，包括允许关键字以及一些对象属性。

###`forin`

该设置要求所有的`for in`语句中使用`hasOwnProperty`函数对对象进行过滤操作。`for in`遍历对象属性的时候会遍历来自原型的属性信息，而这很有可能会导致程序的bug。为了避免这个问题。

	for (key in obj) {
	  if (obj.hasOwnProperty(key)) {
	    // We are sure that obj[key] belongs to the object and was not inherited.
	  }
	}

###`freeze`

这个设置禁止重写原生对象的原型属性，例如`Array`，`Date`等等。

	// jshint freeze:true
	Array.prototype.count = function (value) { return 4; };
	// -> Warning: Extending prototype of native object: 'Array'.

###`funcscope`

这个设置将允许使用超出作用域范围外的变量。虽然javascript是存在两种作用域，全局和函数内部，这种写法会对代码的理解和调试造成困难，这也是为什么在默认情况下，JSHint会警告说该变量已超出作用域范围的原因。

	function test() {
	  if (true) {
	    var x = 0;
	  }
	
	  x += 1; // Default: 'x' used out of scope.
	            // No warning when funcscope:true
	}


###`globals`

这个设置允许你指定一个全局变量白名单，和undef标记搭配可以去除一些警告信息。

设置true表示变量可以读可写。
设置false表示变量只读。

如：

	$:true,jQuery:true,window:true,document:true,navigator:true,define:true,requirejs:true,ActiveXObject:true  

###`globalstrict`

这个设置允许全局使用严格模式。更多信息查看`strict`设置。

###`immed`

该设置禁止使用下面的匿名函数：

	(function() {
	   // body
	})();

推荐使用：

	(function() {
	   // body 
	}());

这样做的目的主要是明确获取的是函数结果，而不是函数自身。

###`indent`

该选项设置代码中的缩进宽度。

###`iterator`

这个设置允许使用`__iterator__`属性。使用的时候需要小心，因为这个属性并不是所有浏览器都支持。

###`latedef`

这个设置禁止你使用未定义的变量。js变量遵循的是函数级别的作用域，而不是块级作用域，简单的说，下面的代码：

	function sum(numbers) {
	    for (var i = 0, n = numbers.length; i < n; i++) {
	        var sum = sum + numbers[i];
	    }
	    return sum;
	}

相当于：

	function sum(numbers) {
	    var i, n, sum;
	    for (i = 0, n = numbers.length; i < n; i++) {
	       sum = sum + numbers[i];
	    }
	    return sum;
	}

这种行为叫做“变量提升”。为了方便理解，推荐使用下面的声明方式。

###`maxcomplexity`

这个选项设置了圈复杂度(Cyclomatic Complexity)。简单的说就是一般的独立分支结构的数量，类似if语句的分支结构。具体可以参考 [wiki](http://en.wikipedia.org/wiki/Cyclomatic_complexity "Cyclomatic_complexity")。

###`maxdepth`

这个选项控制代码块嵌套的深度。

	// jshint maxdepth:2

	function main(meaning) {
	  var day = true;
	
	  if (meaning === 42) {
	    while (day) {
	      shuffle();
	
	      if (tired) { // JSHint: Blocks are nested too deeply (3).
	          sleep();
	      }
	    }
	  }
	}


###`maxerr`

这个设置允许JSHint中断检查前的最大容错量，默认为50。

###`maxlen`

这个设置为了限制每行代码的最大长度，便于源代码的阅读。

###`maxparams`

这个设置主要是限制你函数声明时接受参数的数量。

	// jshint maxparams:3

	function login(request, onSuccess) {
	  // ...
	}
	
	// JSHint: Too many parameters per function (4).
	function logout(request, isManual, whereAmI, onSuccess) {
	  // ...
	}

###`maxstatements`

这个设置限制一个函数内部的最大变量声明数。

	// jshint maxstatements:4

	function main() {
	  var i = 0;
	  var j = 0;
	
	  // Function declarations count as one statement. Their bodies
	  // don't get taken into account for the outer function.
	  function inner() {
	    var i2 = 1;
	    var j2 = 1;
	
	    return i2 + j2;
	  }
	
	  j = i + j;
	  return j; // JSHint: Too many statements per function. (5)
	}

###`newcap`

这个设置主要是为了遵循构造函数首字母大写的约定。该约定有利于使用者区分普通函数和构造函数，避免错误的发生。

如果没有通过new直接条用构造函数，会将构造函数中设置的变量添加到全局作用域，从而导致bug的发生。

###`noarg`

该选项禁止使用`arguments.caller`和`arguments.callee`。两者的使用一方面很难让浏览器对其进行优化，另一方面在未来的javascript中可能会将该特性移除。在ECMAScript5的严格模式下，`arguments.callee`已经禁止使用。

###`nocomma`

该选项禁止使用","运算符。该运算符很容易在声明的时候进行误用，导致错误的发生。

###`noempty`

该选项禁止代码中存在空的代码块。虽然不会导致错误，但是显然改代码的存在是没有必要的。

###`nonbsp`

这个选项禁止关于“non-breaking whitespace”字符。这个字符可能会在Mac电脑上被输入，据说会破坏非UTF8编码的页面。

###`nonew`

这个选项禁止直接调用构造器函数来完成一些功能。有些人喜欢这样干：

	new MyConstructor();

虽然该函数可能会按照你的意愿去执行，但是显然这种操作是不可取的，你应该创建一个一般的函数来完成类似的功能，而不是直接调用构造函数。

###`notypeof`

这个设置允许你使用`typeof`操作。该操作的返回值是[一个固定的集合](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)。默认情况下，使用该操作，JSHint会给你警告。

	// 'fuction' instead of 'function'
	if (typeof a == "fuction") { // Invalid typeof value 'fuction'
	  // ...
	}

除非你绝对确认你在干什么，否则请谨慎使用`typeof`。

###`quotmark`

该选项将强制你在代码中使用引号的风格。可以有三个值：

+ true 不限制，但是要配对。
+ single 只能是单引号。
+ double 只能是双引号。

###`shadow`

该选项允许你使用variable shadowing(就是多次声明使用同一变量名）。例如：

	var x = 5;

	function test(x) {
		x = 10;
	
		if(x) {
			var x = 20;
		}
	}
	
	test(x);

该设置有四种选项：

+ `inner` 检测同一个作用域内部的变量。
+ `outer` 检测外部作用域变量和内部作用域。
+ `false` 和`inner`相同。
+ `true` 允许variable shadowing。

###`singleGroups`

该选项禁止你使用不必要的`()`（分组操作符)。使用该操作符主要是对于一元运算符和优先级不理解的结果。例如:

	// jshint singleGroups: true

	delete (obj.attr); // Warning: Grouping operator is unnecessary for lone expressions.

###`undef`

这个选项禁止你使用未声明的变量。它非常有用，可以避免很多类似输入错误的bug。

	// jshint undef:true

	function test() {
	  var myVar = 'Hello, World';
	  console.log(myvar); // Oops, typoed here. JSHint with undef will complain
	}

如果你使用的变量在别的文件内部定义，可以通过`global`选项告诉JSHint。

###`unused`

该选项可以检测未使用的变量声明，该设置可以使你的代码更加整洁。

	// jshint unused:true

	function test(a, b) {
	  var c, d = 2;
	
	  return a + d;
	}
	
	test(1, 2);
	
	// Line 3: 'b' was defined but never used.
	// Line 4: 'c' was defined but never used.

除了代码内部的变量，它也会提示你在`Global`内部声明却没有使用的变量。

##Relaxing options（宽松的设置）

当下面的条件符合，JSHint会减少检测代码发出的警告。

###`asi`

该选项允许你省略分号。虽然javascript可以补全末尾的分号，但是你最好不要那样做。

###`boss`

该设置允许你在一些条件语句中进行赋值操作。在代码中，这样做一般是个错误，如：`if (a = 10) {...}`，然而并不是都如此：

	for (var i = 0, person; person = people[i]; i++) {}

你可以消除这条警告：

	for (var i = 0, person; (person = people[i]); i++) {}


###`debug`

该选项允许在你的代码中使用`debugger`声明。

###`elision`

这个选项告诉JSHint允许你在代码中使用ES3数组的elision元素，或者empty元素。例如：

	[1, 2, , , 4, , , 7]

###`eqnull`

该选项允许你使用`== null`。这个选项在你想比较一个变量是否为`null`或`undefined`可能会被用到。

###`esnext`

该选项允许在你的代码中使用ECMAScript 6规范。当前并不是所有浏览器都支持该标准。

###`evil`

该选项允许你在代码中使用evil。最好不要使用它，因为这可能会存在代码注入的风险。

###`expr`

该选项允许你在赋值或函数调用的时候使用表达式。

###`lastsemic`

该选项允许你省略单行代码后的分号。

	var name = (function() { return 'Anton' }());

这种情况很少会是人为书写造成的，一般该情况可能是使用javascript代码生成器的结果。

###`laxbreak`

该选项允许不安全的行中断。一般和`laxcomma`配合。

###`laxcomma`

该选项允许comm-first代码样式。

	var obj = {
	    name: 'Anton'
	  , handle: 'valueof'
	  , role: 'SW Engineer'
	};


###`loopfunc`

该选项允许循环内部定义函数。该操纵容易导致下面的bug：

	var nums = [];

	for (var i = 0; i < 10; i++) {
	  nums[i] = function (j) {
	    return i + j;
	  };
	}
	
	nums[0](2); // Prints 12 instead of 2

修改该方案的一个方式是使用闭包：

	var nums = [];

	for (var i = 0; i < 10; i++) {
	  (function (i) {
	    nums[i] = function (j) {
	        return i + j;
	    };
	  }(i));
	}

###`moz`

这个设置告诉JSHint在你的代码中使用了Mozilla扩展。一般情况下并不需要此设置。

###`multistr`

这个设置允许多行字符串。多行字符串是危险的，它非常容易导致你的字符串和原意不符。例如你不小心添加一个空格，或者在某代码中使用了`\`字符。

虽然被允许使用跨行字符串，但是它仍然可能产生警告。例如你没有使用`\`或者在`\`后面存在空白字符。

	// jshint multistr:true

	var text = "Hello\
	World"; // All good.
	
	text = "Hello
	World"; // Warning, no escape character.
	
	text = "Hello\ 
	World"; // Warning, there is a space after \

###`noyield`

该设置允许在使用生成器函数（generator functions)的时候没有`yield`声明。

###`phantom`

这个设置定义了一些全局变量，当你的代码运行在PhantomJS环境中。 PhantomJS 是一个基于WebKit的服务器端 JavaScript API。它全面支持web而不需浏览器支持，其快速，原生支持各种Web标准： DOM 处理, CSS 选择器, JSON, Canvas, 和 SVG。

###`plusplus`

该设置禁止你使用一元运算符`++`和`--`。

###`proto`

这个设置允许代码中使用`__proto__`属性。

###`scripturl`

这个设置允许使用script-targeted URLs，如：`javascript:...`。

###`strict`

这个设置要求所有的函数全部运行在ECMAScript 5的严格模式下。该设置开启严格模式只限定在函数作用内部。因为全局的严格模式可能会导致一些第三方库出现问题。如果你想在全局使用严格模式，请查看`globalstrict`选项。

###`sub`

该设置允许你使用`[]`符号，当可以使用`.`符号的时候。`person['name']` VS. `person.name`。

###`supernew`

这个设置允许在代码中使用一些“weird”结构。类似`new function(){...}`或`new Object;`等等。有的时候你可能会创建一个单例：

	var singleton = new function() {
	  var privateVar;
	
	  this.publicMethod  = function () {}
	  this.publicMethod2 = function () {}
	};

###`validthis`

这个设置允许你在严格模式下，在非构造函数使用`this`。你应该仅仅在一个函数内部使用这个设置，如果你确保这个this是有效的情况下，例如：(使用`Function.call`)。

该设置仅仅在函数作用域内部使用，如果在全局使用，JSHint会给出错误提示。

###`withstmt`

这个设置允许使用`with`声明。

##Environments（环境）

这个设置告诉JSHint知道一些提前定义的全局变量。

###`browser`

该设置定义了浏览器模式常用的相关变量。从老的`docment`和`navigator`到HTML5支持的`FileReader`。

这个设置不支持类似`alert`和`console`功能的关键字。具体可以了解`devel`设置。

###`browserify`

这个设置定义了使用[`Browserify tool`](http://browserify.org/)构建一个工程的相关变量。

###`couch`

这个设置定义了使用`CouchDB`的常用变量。

###`devel`

这个设置定义了常用用于日志或debugging提示等等的变量。如：`console`，`alert`等等。在生产环境中不使用它们通常是个好主意，例如：`console.log`在IE下就会报错。

###`dojo`

定义了`Dojo Toolkit`相关的变量。

###`jasmine`

定义了`Jasmine unit testing framework`相关变量。

###`jquery`

定义了`JQuery`相关的变量。

###`mocha`

定义了`Mocha unit testing framework`相关变量。

###`mootools`

定义了`mootools`相关变量。

###`node`

定义了`node`相关变量。

###`nonstandard`

这个定义了非标准但是被广泛采纳的变量。如： `escape` and `unescape`。

###`prototypejs` [prototype](http://prototypejs.org/)
###`qunit` [qunit](http://qunitjs.com/)
###`rhino` [rhino](http://www.mozilla.org/rhino/)
###`shelljs`[shelljs](http://documentup.com/arturadib/shelljs)
###`typed` [typed](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)
###`worker` [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/basic_usage)
###`wsh` [wsh](http://en.wikipedia.org/wiki/Windows_Script_Host)
###`yui` [yui](http://yuilibrary.com/)