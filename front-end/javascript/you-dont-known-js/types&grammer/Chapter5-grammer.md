# You Don't Know JS: Types & Grammar

# Chapter 5: Grammar

我们要解决的最后一个主要话题是JavaScript的语言语法是如何工作的（也就是语法）。你可能认为自己知道如何写JS，但语言语法的各个部分存在很多细微差别导致混淆和误解，因此我们希望深入研究这些部分并搞清楚一些概念。

**注意：** “语法(grammar)”一词对读者来说可能比“语法(句法，syntax)”一词更不熟悉。在许多方面，它们是类似的术语，描述了语言如何工作的规则。存在微妙的差异，但它们对我们在这里的讨论大多无关紧要。JavaScript的语法是一种结构化的方式，用于描述语法（运算符，关键字等）如何组合成格式良好的有效程序。换句话说，讨论没有语法(grammar)的句法(syntax)会遗漏许多重要的细节。所以我们在本章中的重点是最准确地描述为语法，即使语言的原始句法是开发人员直接与之交互。

## Statements & Expressions

开发人员认为术语“声明”和“表达”大致相同是很常见的。但是在这里我们需要区分这两者，因为我们的JS程序中存在一些非常重要的差异。

为了区分，让我们借用你可能更熟悉的术语：英语。

“句子”是表达思想的一个完整的词汇形式。由一个或多个“短语”组成，每个短语都可以用标点符号或连词（“和”，“或”等）连接。短语本身可以由较小的短语组成。有些短语是不完整的，自己完成不了多少，而其他短语可以独立存在。这些规则统称为英语语法(*grammar*)。

所以它与JavaScript语法一致。语句是句子，表达式是短语，运算符是连词/标点符号。

JS中的每个表达式都可以评估为单个特定值结果。例如：

```js
var a = 3 * 6;
var b = a;
b;
```

在此片段中，`3 * 6`是表达式（计算值`18`）。但是第二行上的`a`也是表达式，就像第三行中的`b`一样。`a`和`b`表达式都评估当时存储在那些变量中的值，也恰好是`18`。

而且，三行中的每一行都是包含表达式的语句。`var a = 3 * 6`和`var b = a`被称为"声明语句"，因为它们都声明了一个变量（并且可选地为它赋值）。`a = 3 * 6`和`b =a `（除去`var`）称为赋值表达式。

第三行只包含表达式`b`，但它本身也是一个声明（虽然不是一个非常有趣的声明！）。

这通常被称为“表达式声明”。

### Statement Completion Values

这是一个鲜为人知的事实，语句都有完成值（即使该值只是`undefined`）。

你怎么才能看到一个语句的完成值呢？

最明显的答案是在浏览器的开发人员控制台中键入语句，因为当你执行它时，控制台默认会报告它执行的最新语句的完成值。

我们考虑`var b = a`。该声明的完成值是什么？
`b = a`赋值表达式导致结果是分配的值（上面的`18`），但`var`语句本身结果是`undefined`。为什么？因为`var`语句是在规范中定义的。如果你把`var a = 42;`放入你的控制台，你会看到`undefined`的输出而不是`42`。

**注意：** 从技术上讲，它比这复杂一点。在ES5规范第12.2节“变量声明”中，`VariableDeclaration`算法实际上确实返回一个值（一个包含声明的变量名称的`string` - 怪异，呵呵！），但是这个值基本上被`VariableStatement`算法吞噬了（除了`for..in`循环使用），它强制一个空的（也就是`undefined`的）完成值。

事实上，如果你在控制台（或在javascript环境REPL--read/evaluate/print/loop工具中）进行了大量的代码实验，那么你可能在许多不同的语句之后看到了`undefined`的报告，也许从来没有意识到为什么或那是什么。

但是控制台打印出的完成值不是我们可以在程序中使用的东西。那么我们如何捕获完成值呢？

这是一项更复杂的任务。在我们解释如何解释之前，让我们探讨一下 *为什么* 要这样做。

我们需要考虑其他类型的语句完成值。例如，任何常规`{..}`块都具有完成值，就是其最后包含的语句/表达式的完成值。

考虑下面的代码：

```js
var b;

if (true) {
	b = 4 + 38;
}
```

如果你在你的控制台/ REPL中键入它，你可能会看到`42`的报告，因为`42`是`if`块的完成值，它接受了它的最后赋值表达式语句`b = 4 + 38`的完成值。

换句话说，块的完成值类似于块中最后一个语句值的 *隐式* 返回。

**注意：** 这在概念上与CoffeeScript等语言相似，它具有与`function`中最后一个语句值相同的函数的隐式`return`值。

但是有一个明显的问题。这种代码不起作用：

```js
var a, b;

a = if (true) {
	b = 4 + 38;
};
```

我们无法捕获语句的完成值，并以任何简单的句法/语法方式将其分配到另一个变量中（至少现在还没有！）。

所以，我们可以做什么？

**注意：** 仅用于演示目的 - 实际上不要在您的实际代码中执行以下操作！

我们可以使用备受诟病的`eval(..)`（有时发音为“evil”）函数来捕获此完成值。

```js
var a, b;

a = eval( "if (true) { b = 4 + 38; }" );

a;	// 42
```

耶耶耶，这也太难看了。但是他工作了！它说明了语句完成值是一个真实的东西，不仅可以在我们的控制台中捕获，而且可以在我们的程序中捕获。

有一个名为“表达式”的ES7提案。以下是它的工作原理：

```js
var a, b;

a = do {
	if (true) {
		b = 4 + 38;
	}
};

a;	// 42
```

`do {..}`表达式执行一个块（其中包含一个或多个语句），块内的最终语句完成值成为`do`表达式的完成值，然后可以将其分配给`a`，如上所示。

一般的想法是能够将语句视为表达式 - 它们可以显示在其他语句中 - 而无需将它们包装在内联函数表达式中并执行显式`return..`。

目前，语句完成值只不过是一些琐事。但随着JS的发展，它们可能会扮演更重要的角色，并且希望`do {..}`表达式能够减少使用`eval(..)`之类的东西的诱惑。

**警告：** 重复我之前的警告：避免使用`eval(..)`。这是认真的。有关更多说明，请参阅本系列的"*Scope & Closures*"标题。

### Expression Side Effects

大多数表达式没有副作用。例如：

```js
var a = 2;
var b = a + 3;
```

表达式`a + 3`本身不具有副作用，例如改变`a`。它有一个结果，即`5`，结果在`b = a + 3`的语句中被赋值给`b`。

具有（可能的）副作用的表达式的最常见示例是函数调用表达式：

```js
function foo() {
	a = a + 1;
}

var a = 1;
foo();		// result: `undefined`, side effect: changed `a`
```

但是，还有其他副作用表达式。例如：

```js
var a = 42;
var b = a++;
```

表达式`a ++`有两个不同的行为。首先，它返回`a`的当前值，即`42`（然后将其分配给`b`）。但接下来，它会改变`a`自身的值，将其递增`1`。

```js
var a = 42;
var b = a++;

a;	// 43
b;	// 42
```

许多开发人员会错误地认为`b`具有值`43`就像`a`一样。但是，混淆来自于没有充分考虑`++`运算符的副作用的时间。

`++`增量运算符和 `-` 减量运算符都是一元运算符（参见第4章），可以在后缀（“后”）位置或前缀（“前”）位置使用。

```js
var a = 42;

a++;	// 42
a;		// 43

++a;	// 44
a;		// 44
```

当`++`在前缀位置使用作为`++ a`时，其副作用（递增`a`）发生在从表达式返回值之前，而不是在使用`++`之后返回。

**注意：** 你认为`++ a ++`是合法的句法吗？如果你尝试它，你将收到`ReferenceError`错误，但为什么？因为副作用运算符需要变量引用来定位其副作用。对于`++ a ++`，首先评估`a ++`部分（因为运算符优先级 - 见下文），它在增量之前返回`a`的值。但后来它试图评估`++ 42`，它（如果你尝试的话）给出了相同的`ReferenceError`错误，因为`++`不能直接对像`42`这样的值产生副作用。

有时会错误地认为你可以通过将它包装在`()`对中来封装`++`的后效应，例如：

```js
var a = 42;
var b = (a++);

a;	// 43
b;	// 42
```

不幸的是，`()`本身没有定义一个新的包装表达式，它将在`a ++`表达式的后副作用之后进行评估，正如我们所希望的那样。事实上，即使它确实如此，一个`++`首先返回`42`，除非你有另一个表达式在`++`的副作用之后重新评估`a`，你不会从该表达式得到`43`，所以`b`将不会被赋值`43`。

但是有一个选项: `,` 声明系列逗号运算符。此运算符允许你将多个独立表达式语句串联到一个语句中：

```js
var a = 42, b;
b = ( a++, a );

a;	// 43
b;	// 43
```

**注意：** `(..)`在这里包围`a++, a`是需要的。原因是运算符优先级，我们将在本章后面介绍。

表达式`a++, a`，表示第二个语句表达式在第一个`a ++`语句表达式的副作用之后被计算，这意味着它返回`43`值以赋值给`b`。

副作用运算符的另一个例子是`delete`。正如我们在第2章中所示，`delete`用于从`object`或插槽中删除`array`中的属性。但它通常只是作为独立声明调用：

```js
var obj = {
	a: 42
};

obj.a;			// 42
delete obj.a;	// true
obj.a;			// undefined
```

如果请求的操作有效/允许，则`delete`运算符的结果值为`true`，否则为`false`。但是运算符的副作用是它删除了属性（或数组槽）。

**注意：** 有效/允许是什么意思？不存在的属性或存在且可配置的属性（请参阅本系列的this＆Object Prototypes标题的第3章）将从`delete`运算符返回`true`。否则，结果将为`false`或错误。

副作用运算符的最后一个例子是`=`赋值运算符，它可能同时是明显的和非显而易见的。

考虑下面的例子：

```js
var a;

a = 42;		// 42
a;			// 42
```

看起来似乎`=`在  `a = 42`是表达式的副作用运算符。但是如果我们检查`a = 42`语句的结果值，它就是刚刚分配的值（`42`），因此将相同值赋值给`a`本质上是一个副作用。

**提示：** 关于副作用的相同推理适用于复合赋值运算符，如`+ =`， `- =`等。例如， `a = b += 2`首先被作为`b += 2`处理(这个就是`b = b + 2`)，然后将结果通过`=`赋值给`a`。

这种赋值表达式（或语句）赋值的行为主要用于链式赋值，例如：

```js
var a, b, c;

a = b = c = 42;
```

这里，`c = 42`被评估为`42`（具有将`42`分配给`c`的副作用），然后`b = 42`被评估为`42`（具有将`42`分配给`b`的副作用），并且最终评估`a = 42`（具有将`42`分配给`a`的副作用）。

**警告：** 开发人员使用链式赋值所犯的常见错误就像`var a = b = 42`。虽然这看起来像是同样的事情，但事实并非如此。如果在没有单独的`var b`（作用域内的某个地方）正式声明`b`的情况下发生该声明，那么`var a = b = 42`将不会直接声明`b`。根据`strict`模式，可能会抛出错误或创建全局的变量（请参阅本系列的“*Scope & Closures*”标题）。

另一种需要考虑的方案：

```js
function vowels(str) {
	var matches;

	if (str) {
		// pull out all the vowels
		matches = str.match( /[aeiou]/g );

		if (matches) {
			return matches;
		}
	}
}

vowels( "Hello World" ); // ["e","o","o"]
```

这是有效的，许多开发人员比较喜欢这样做。但是使用我们利用赋值副作用的惯用语，我们可以通过将两个`if`语句组合成一个来简化：

```js
function vowels(str) {
	var matches;

	// pull out all the vowels
	if (str && (matches = str.match( /[aeiou]/g ))) {
		return matches;
	}
}

vowels( "Hello World" ); // ["e","o","o"]
```

**注意：** `( .. )`包围`matches = str.match..`是必要的。原因是运算符优先级，我们将在本章后面的“Operator Precedence”一节中介绍。

我更喜欢这种较短的风格，因为我认为这两个条件实际上是相关的，连在一起比分开要更清楚。但是和JS中的大多数风格选择一样，认为哪一个 *更好* 纯粹是个人意见。

### Contextual Rules

JavaScript语法规则中有很多地方，相同的语法意味着不同的东西，这取决于它在何处/如何使用。这种事情，孤立地使用，会引起相当多的困惑。

我们不会在这里详尽地列出所有这些案例，而只是列举一些常见案例。

#### `{ .. }` Curly Braces

有两个主要的地方（还有更多来自JS的演变！），你的代码中会出现一对{..}花括号。我们来看看他们的每一个。

##### Object Literals

首先，作为`object`字面量：

```js
// 假设这里有一个 `bar()` 函数被定义

var a = {
	foo: bar()
};
```

我们怎么知道这是一个`object`文字？因为`{..}`是一个被分配给`a`的值。

**注意：** `a`引用称为“l值”（又称左边值），因为它是赋值的目标。`{..}`对是一个“r值”（又名右边值），因为它仅用作值（在这种情况下作为赋值的来源）。

##### Labels

如果我们删除上述代码段的`var a =`部分会发生什么？

```js
// 假设这里有一个 `bar()` 函数被定义

{
	foo: bar()
}
```

许多开发人员认为`{..}`只是一个独立的`object`字面量，不会被分配到任何地方。但它实际上完全不同。

这里，`{ .. }`只是一个常规的代码块。它在JavaScript中并不是非常惯用（在其他语言中更是如此！）拥有这样的独立`{..}`块，但它是完全有效的JS语法。结合`let`块级作用域声明时，它可能特别有用（请参阅本系列中的Scope＆Closures标题）。

这里的`{..}`代码块在功能上几乎与附加到某个语句的代码块相同，如`for` / `while`循环，`if`条件等。

但是，如果它是一个正常的代码块，那么奇怪的是`foo: bar()`语法是什么，以及它是如何合法的？

这是因为JavaScript中有一个鲜为人知（并且坦率地说，不鼓励使用）的功能称为“带标签的语句”。`foo`是语句`bar()`的标签（已省略其尾随`;`  -- 请参阅本章后面的“Automatic Semicolons”）。但标签声明的重点是什么？

如果JavaScript有一个`goto`语句，理论上你可以说`goto foo`并在代码中跳转到该位置。`goto`通常被认为是糟糕的编码习惯，因为它们使代码更难理解（也就是“意大利面条代码”），所以javascript没有一个通用的`goto`是一件非常好的事情。

但是，JS确实支持一种有限的特殊形式的`goto`：带标签的跳转。`continue`和`break`语句都可以选择接受指定的标签，在这种情况下，程序流“跳转”类似于`goto`。考虑下面的代码：

```js
// `foo` labeled-loop
foo: for (var i=0; i<4; i++) {
	for (var j=0; j<4; j++) {
		// whenever the loops meet, continue outer loop
		if (j == i) {
			// jump to the next iteration of
			// the `foo` labeled-loop
			continue foo;
		}

		// skip odd multiples
		if ((j * i) % 2 == 1) {
			// normal (non-labeled) `continue` of inner loop
			continue;
		}

		console.log( i, j );
	}
}
// 1 0
// 2 0
// 2 1
// 3 0
// 3 2
```

**注意：** `continue foo`并不意味着“转到'foo'标记的位置继续”，而是“继续使用下一次迭代标记'foo'的循环。”所以，它并不是一个任意的`goto`。

如你所见，我们跳过了奇数倍`3 1`的迭代，但标签循环跳转也跳过迭代`11`和`2 2`。

也许一个稍微有用的标记跳转形式是在内部循环内使用`break __`，你想要突破外部循环。

也许更有用的标记跳转形式是从一个内部循环中`break__`，在这个内部循环中，你希望从外部循环中断。如果没有标记的`break`，这种相同的逻辑有时会很难编写：

```js
// `foo` labeled-loop
foo: for (var i=0; i<4; i++) {
	for (var j=0; j<4; j++) {
		if ((i * j) >= 3) {
			console.log( "stopping!", i, j );
			// break out of the `foo` labeled loop
			break foo;
		}

		console.log( i, j );
	}
}
// 0 0
// 0 1
// 0 2
// 0 3
// 1 0
// 1 1
// 1 2
// stopping! 1 3
```

**注意：** `break foo`并不意味着“转向'foo'标记的位置继续，”而是“跳出标记为'foo'的循环/块并继续它后面的部分。”不完全是传统意义上的`goto`，啊哈？

上述的非标记`break`选项可能需要涉及一个或多个函数，共享范围变量访问等。它很可能比标记`break`更令人困惑，因此使用带标签的`break`可能是更好的选择。

标签可以应用于非循环块，但只有`break`可以引用这样的非循环标签。可以在任何标记的块中执行标记的`break___`，但是你不能`continue ___`非循环标签，也不能从一个块中进行非标记的`break`。

```js
function foo() {
	// `bar` labeled-block
	bar: {
		console.log( "Hello" );
		break bar;
		console.log( "never runs" );
	}
	console.log( "World" );
}

foo();
// Hello
// World
```

标记的循环/块是非常罕见的，并且经常不被赞成。如果可能的话，最好避免使用它们;例如，使用函数调用而不是循环跳转。但也许有些情况可能会有用。如果你打算使用带标签的跳转，请务必使用大量注释记录你正在做的事情！

人们普遍认为JSON是JS的一个子集，所以一个JSON字符串（如`{"a"：42}` - 注意到JSON要求的属性名称周围的引号！）被认为是一个有效的JavaScript程序。**不对！** 尝试将`{"a"：42}`放入JS控制台，你将收到错误消息。

那是因为语句标签周围没有引号，所以`"a"`不是有效的标签，因此`:`不能在它之后。

因此，JSON确实是JS语法的一个子集，但JSON本身并不是有效的JS语法。

沿着这些方向的一个非常常见的误解是，如果你要将JS文件加载到只有JSON内容的`<script src = ..>`标记中（例如来自API调用），数据将被读取为有效的JavaScript，但程序无法访问。JSON-P（在函数调用中包装JSON数据的做法，如`foo({"a": 42})`）通常被称为通过将值发送到程序的一个函数来解决这种不可访问性。

**不对！** 完全有效的JSON值`{"a": 42}`本身实际上会抛出一个JS错误，因为它被解释为带有无效标签的语句块。但是`foo({"a":42})`是有效的js，因为在这里，`{"a":42}`是传递给`foo(..)`的对象字面量值。所以，正确地说，**JSON-P使JSON成为有效的JS语法！**

##### Blocks

另一个常被引用的JS问题（与强制相关 - 见第4章）是：

```js
[] + {}; // "[object Object]"
{} + []; // 0
```

这似乎意味着`+`运算符根据第一个操作数是`[]`还是`{}`给出不同的结果。但这实际上与它无关！

在第一行，`{}`出现在`+`运算符的表达式中，因此被解释为实际值（空`object`）。第4章解释说`[]`被强制为`""`，因此`{}`也被强制转换为字符串值：`"[object Object]"`。

但是在第二行，`{}`被解释为一个独立的`{}`空块（它什么都不做）。块不需要用分号来终止它们，所以这里缺少一个不是问题。最后，`+ []`是一个表达式，它 *明确地强制* （见第4章）`[]`为一个`number`，即`0`值。

##### Object Destructuring

从ES6开始，您将看到另一个显示`{..}`对的地方是“解构分配”（有关更多信息，请参阅本系列的ES6和Beyond标题），特别是`object`解构。考虑：

```js
function getData() {
	// ..
	return {
		a: 42,
		b: "foo"
	};
}

var { a, b } = getData();

console.log( a, b ); // 42 "foo"
```

你可能会说，`var { a , b } = ..`是ES6解构分配的一种形式，这大致相当于：

```js
var res = getData();
var a = res.a;
var b = res.b;
```

**注意：** `{a，b}`实际上是`{a：a，b：b}`的ES6解构缩写，因此两者都有效，但预计较短的`{a，b}`将成为首选形式。

使用`{..}`对的对象解构也可以用于命名函数参数，对于同一种隐式对象属性赋值，它是语法糖：

```js
function foo({ a, b, c }) {
	// no need for:
	// var a = obj.a, b = obj.b, c = obj.c
	console.log( a, b, c );
}

foo( {
	c: [1,2,3],
	a: 42,
	b: "foo"
} );	// 42 "foo" [1, 2, 3]
```

因此，我们使用`{..}`对的上下文完全决定了它们的含义，这说明了句法(syntax)和语法(grammer)之间的区别。理解这些细微差别以避免JS引擎的意外解释非常重要。

#### `else if` And Optional Blocks

人们普遍误解javascript有一个`else if`子句，因为你可以这样做：

```js
if (a) {
	// ..
}
else if (b) {
	// ..
}
else {
	// ..
}
```

但是这里有一个隐藏的JS语法特征：这里没有`else if`。但`if` 和`else`语句如果只包含一个语句，则允许省略其附加块周围的`{}`。你之前见过很多次，就是：

```js
if (a) doSomething( a );
```

许多JS风格指南都会坚持让你总是在一个语句块周围使用`{}`，例如：

```js
if (a) { doSomething( a ); }
```

但是，完全相同的语法规则适用于`else`子句，因此，可能你一直编码的`else if`形式*实际* 上被解析为：

```js
if (a) {
	// ..
}
else {
	if (b) {
		// ..
	}
	else {
		// ..
	}
}
```

`if (b) { .. } else { .. }`是跟在`else`后面的单个语句，所以你可以将周围的`{}`放入或不放入。换句话说，当你使用`else if`时，你在技术上打破了常见的样式指南规则，只是使用单个`if`语句定义你的`else`。

当然，`else if`惯用法非常普遍，导致缩进程度较低，因此具有吸引力。无论你采用哪种方式，只需在你自己的样式指南/规则中明确指出，并且不要假定像`else if`这样的东西是直接语法规则。

## Operator Precedence

正如我们在第4章中所述，JavaScript的版本关于`&&`和`||`有趣的是，他们选择并返回其中一个操作数，而不仅仅是得到`true`或`false`。如果只有两个操作数和一个操作符，这很容易解释。

```js
var a = 42;
var b = "foo";

a && b;	// "foo"
a || b;	// 42
```

但是当涉及两个操作符和三个操作数时呢？

```js
var a = 42;
var b = "foo";
var c = [1,2,3];

a && b || c; // ???
a || b && c; // ???
```

要理解这些表达式导致的结果，我们需要了解当表达式中存在多个表达式时，运算符处理方式的规则。

这些规则称为“运算符优先级”。

我敢打赌，大多数读者都认为他们对运算符的优先权有很好的把握。但正如我们在本系列丛书中所涵盖的所有其他内容一样，我们将深入了解这种理解，看看它究竟是多么坚固，并希望在此过程中学到一些新的东西。

回想一下上面的例子：

```js
var a = 42, b;
b = ( a++, a );

a;	// 43
b;	// 43
```

但是如果我们移除`()`会发生什么？

```js
var a = 42, b;
b = a++, a;

a;	// 43
b;	// 42
```

等等！为什么这会改变分配给`b`的值？

因为`,`运算符的优先级低于`=`运算符。因此，`b = a++, a`被解释为`(b = a++), a`。因为（正如我们前面所解释的）`a++`有后副作用，所以给`b`赋的值是在`++`改变`a`之前的值`42`。

这只是需要理解运算符优先级的简单问题。如果你打算使用`,`作为一个语句系列运算符，重要的是要知道它实际上具有最低优先级。其他所有运算符的绑定都将比`,`更紧密。

现在，回忆一下之前的这个例子：

```js
if (str && (matches = str.match( /[aeiou]/g ))) {
	// ..
}
```

我们说赋值周围的`()`是必需的，但为什么呢？因为`&&`的优先级高于`=`，所以没有`()`强制绑定，表达式将被视为`(str && matches) = str.match ...`。但这将是一个错误，因为`(str && matches)`的结果不是一个变量，而是一个值（在这种情况下是`undefined`的），因此它不能是在`=`赋值的左侧！

好的，所以你可能认为你已经将这个运算符优先级搞懂了。

让我们继续讨论一个更复杂的例子（我们将在本章的下几部分中介绍），以真正测试你的理解：

```js
var a = 42;
var b = "foo";
var c = false;

var d = a && b || c ? c || b ? a : c && b : a;

d;		// ??
```

好吧，邪恶，我承认了。没有人会像这样写一串表达式，对吧？可能不是，但我们将用它来检查将多个操作符链接在一起的各种问题，这是一项非常常见的任务。

上面的结果是`42`。但是这并没有什么意思，除非我们自己能找到这个答案，而不仅仅是将其插入到JS程序中让JavaScript对其进行排序。

让我们深入研究。

第一个问题 - 你甚至可能没想到 - 是，第一部分(`a && b || c`)是否表现得像`(a && b) || c`或`a && (b || c)`？你知道吗？你能说服自己他们实际上是不同的吗？

```js
(false && true) || true;	// true
false && (true || true);	// false
```

所以，有证据证明他们是不同的。但是，`false && true || true`有什么样的表现？答案：

```js
false && true || true;		// true
(false && true) || true;	// true
```

所以我们有答案。首先评估`&&`运算符，然后评估`||`运算符。

但这只是因为从左到右的处理？让我们颠倒运算符的顺序：

```js
true || false && false;		// true

(true || false) && false;	// false -- nope
true || (false && false);	// true -- winner, winner!
```

现在我们已经证明`&&`先被评估，然后是`||`，在这种情况下，这实际上与通常预期的从左到右的处理相反。

那是什么导致了这种行为？ **运算符优先级。**

每种语言都定义了自己的运算符优先级列表。令人沮丧的是，JS开发人员阅读了JS列表的是多么罕见。

如果你知道的话，上面的例子就不会让你困惑，因为你已经知道`&&`比`||`更优先。但我敢打赌，一定有相当数量的读者必须稍微考虑一下。

**注意：** 不幸的是，JS规范并没有真正将其操作符优先列表放在一个方便的单一位置。你必须解析并理解所有的语法规则。因此，我们将尝试以更方便的格式布置更常见和有用的位。有关运算符优先级的完整列表，请参阅MDN站点上的“Operator Precedence”（*https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence*）。

### Short Circuited

在第4章中，我们在旁注中提到了像`&&`和`||`这样的运算符的“短路”特性。现在让我们再详细介绍一下。

对于`&&`和`||`如果左侧操作数足以确定操作的结果，则 **不会评估** 右侧操作数。因此，名称“短路”（如果可能的话，它将采用早期的捷径）。

例如，使用`a&& b`，如果`a`是falsy的，则不会评估`b`，因为`&&`操作数的结果已经确定，所以没有必要费心去检查`b`。同样，使用`a|| b`，如果`a`是truthy的，操作数的结果已经确定，所以没有理由检查`b`。

这种短路非常有用，通常用于：

```js
function doSomething(opts) {
	if (opts && opts.cool) {
		// ..
	}
}
```

`opts && opts.cool `的`opts`部分充当了一种防护，因为如果`opts`未设置（或不是`object`），则表达式`opts.cool`会抛出错误。`opts`测试失败加上短路意味着`opts.cool`甚至不会被评估，因此没有错误！

同样，你可以使用`||`短路：

```js
function doSomething(opts) {
	if (opts.cache || primeCache()) {
		// ..
	}
}
```

在这里，我们首先检查`opts.cache`，如果它存在，我们不调用`primeCache()`函数，从而避免可能不必要的工作。

### Tighter Binding

但是让我们把注意力转回到早期的复杂语句示例和所有链式运算符，特别是`? :`三元运算符部分。`? :`运算符具有比`&&`和`||`更多或更少的优先级？

```js
a && b || c ? c || b ? a : c && b : a
```

这更像是这样的：

```js
a && b || (c ? c || (b ? a : c) && b : a)
```

或是这样的？

```js
(a && b || c) ? (c || b) ? a : (c && b) : a
```

答案是第二个。但是为什么？

因为`&&`比`||`更优先，并且`||`比`? :`更优先。

所以，`(a && b || c)`首先在`? :`参与之前被评估了。另一种常见的解释方式是`&&`和`||`“绑定更紧密”，而不是`? :`。如果相反，那么`c ? c ...`会更紧密的绑定，它会表现为（作为第一选择），如`a && b || ( c ? c ..)`。

### Associativity

所以，`&&`和`||`运算符首先绑定，然后是`? :`运算符。但是同样优先的多个运算符呢？他们总是从左到右或从右到左处理？

通常，运算符是左关联的或右关联的，指的是 **分组是从左进行还是从右进行。**

值得注意的是，关联性与从左到右或从右到左处理不同。

但是，为什么处理是从左到右还是从右到左都很重要？因为表达式可能有副作用，例如函数调用：

```js
var a = foo() && bar();
```

这里，`foo()`首先被评估，然后可能是`bar()`，这取决于`foo()`表达式的结果。如果`bar()`在`foo()`之前被调用，肯定会得到不同的程序行为。

但是这种行为只是从左到右处理（JavaScript中的默认行为！） - 它与`&&`的关联性无关。在那个例子中，因为这里只有一个`&&`，因而没有相关的分组，所以关联性甚至没有发挥作用。

但是使用类似`a && b && c`的表达式，分组*将* 隐式发生，这意味着将首先评估`a && b`或`b && c`。

从技术上讲，`a && b && c`将被处理为`(a && b) && c`，因为`&&`是左关联的（顺便说一下，`||`也是如此）。然而，右关联替代方法`a && (b && c)`的行为方式与此类似。对于相同的值，相同的表达式以相同的顺序计算。

**注意：** 如果假设`&&`是右关联的，那么它的处理方式与手动使用`()`创建像`a && (b && c)`这样的分组相同。但这仍然 **不意味** 着`c`将在`b`之前处理。相关性并不意味着从右到左的评估，它意味着从右到左的 **分组** 。无论哪种方式，无论分组/关联性如何，评估的严格排序将是`a`，然后是`b`，然后是`c`（又名从左到右）。

所以`&&`和`||`是左联想的并不重要，除了准确讨论他们的定义。

但情况并非总是如此。根据左关联性和右关联性，一些运算符的行为会有很大不同。

考虑一下`? :`(“三元”或“条件”）运算符：

```js
a ? b : c ? d : e;
```

`? :`是右关联的，所以哪个分组表示它将如何处理？

- `a ? b : (c ? d : e)`
- `(a ? b : c) ? d : e`

答案是`a ? b : (c ? d : e)`。与上的`&&`和`||`不同，这里的右关联性实际上很重要，因为对于某些（但不是全部！）值的组合来说`(a ? b : c) ? d : e`的行为将会不同。

一个这样的例子：

```js
true ? false : true ? true : true;		// false

true ? false : (true ? true : true);	// false
(true ? false : true) ? true : true;	// true
```

即使最终结果相同，更多细微差别潜伏在其他值组合中。考虑：

```js
true ? false : true ? true : false;		// false

true ? false : (true ? true : false);	// false
(true ? false : true) ? true : false;	// false
```

从那种情况来看，相同的最终结果意味着分组没有实际意义。然而：

```js
var a = true, b = false, c = true, d = true, e = false;

a ? b : (c ? d : e); // false, evaluates only `a` and `b`
(a ? b : c) ? d : e; // false, evaluates `a`, `b` AND `e`
```

那么，我们已经清楚地证明了这一点`? :`是右关联的，并且关于运算符与自身链接的行为方式，它实际上很重要。

右关联性（分组）的另一个例子是`=`运算符。回想一下本章前面的链式赋值示例：

```js
var a, b, c;

a = b = c = 42;
```

我们之前断言`a = b = c = 42`是首先评估`c = 42`赋值，然后`b = ..`，最后`a = ...`，为什么？由于右关联性，它实际上像这样对待声明：`a = (b = (c = 42))`。

还记得本章前面的运行复杂赋值表达式的示例吗？

```js
var a = 42;
var b = "foo";
var c = false;

var d = a && b || c ? c || b ? a : c && b : a;

d;		// 42
```

有了我们的优先级和关联性知识，我们现在应该能够将代码分解为分组行为，如下所示：

```js
((a && b) || c) ? ((c || b) ? a : (c && b)) : a
```

或者，如果将其缩进更容易理解：

```js
(
  (a && b)
    ||
  c
)
  ?
(
  (c || b)
    ?
  a
    :
  (c && b)
)
  :
a
```

现在让我们来解决他：

1. `(a && b)`就是`"foo"`
2. `"foo" || c`就是`"foo"`
3. 对于第一个`?`来说，`"foo"`是truthy的
4. `(c || b)`是`"foo"`
5. 对于第二个`?`，`"foo"`是truthy的
6. `a`是`42`

就是这样的，我们完成了！答案就是`42`，正如我们之前看到的那样。那真的不是很难，是吗？

### Disambiguation

现在你应该更好地掌握运算符优先级（和关联性），并且更容易理解具有多个链式运算符的代码将如何表现。

但是一个重要的问题仍然存在：我们是否都应该编写理解代码的代码，并且完全依赖于运算符优先级/关联性的所有规则？我们是否应该在需要强制执行不同的处理绑定/顺序时使用`()`手动分组？

或者，另一方面，我们是否应该认识到即使这些规则实际上是可以学习的，但仍有足够的理由来忽略自动优先/关联性？如果是这样，我们是否应该总是使用`()`手动分组并消除对这些自动行为的依赖？

这场辩论具有高度的主观性，与第4章关于 *隐性* 强制的辩论非常对称。大多数开发人员对这两种辩论都有相同的看法：要么他们接受行为并且使用，要么他们放弃这两种行为并坚持手工/显性的惯用法。

当然，我不能像第四章那样，为读者明确地回答这个问题。。但是我向你展示了优点和缺点，并希望更深刻地理解能让你做出明智的决定，而不是跟风别人的决定。

在我看来，这里有一个重要的立场。我们应该将运算符优先级/关联性和`()`手动分组混合到我们的程序中 - 我在第4章中以相同的方式对 *隐式* 强制的健康/安全使用进行了论证，但肯定不会无限制地认可它。

例如, `if(a && b && c)..`对我来说完全没有问题，我就不会为了明确的强调关联性而去`if((a && b) && c)..`，因为我认为这是过度的冗长。

另一方面，如果我需要链接两个`? :`条件运算符在一起，我当然会使用`()`手动分组来清楚地说明我的逻辑是什么。

因此，我的建议类似于第4章：**使用运算符优先级/关联性可以使代码更短更清晰，但在有助于创建清晰度和减少混淆的地方使用`()`手动分组。**

## Automatic Semicolons

ASI（自动分号插入）是指javascript假定`;`在JS程序的某些地方，即使你没有将其放在那里，他就会进行ASI。

为什么会这样做？因为如果你省略一个必要的`;`你的程序会失败。这是不可原谅的。ASI允许JS容忍某些地方`;`通常认为没有必要。

值得注意的是，ASI只会在换行符存在时生效。分号不会插入到行的中间。

基本上，如果JS解析器解析一个可能发生解析器错误的行（应该是缺失的`;`），并且它可以合理地插入`;`，那么它会这样做。插入什么是合理的？只有当某些语句的结尾和该行的换行符之间只有空白和/或注释时。

考虑下面的代码：

```js
var a = 42, b
c;
```

JS应该将下一行的`c`作为`var`语句的一部分来处理吗？如果`,`在`b`和`c`之间的任何地方（甚至是另一条线），它肯定会是的。但由于没有一个`,`，JS反而假定有一个隐含的`;`（在换行符处）在`b`后面。因此`c;`被保留为独立的表达式语句。

同样的：

```js
var a = 42, b = "foo";

a
b	// "foo"
```

这仍然是一个没有错误的有效程序，因为表达式语句也接受ASI。

在某些地方ASI很有帮助，比如：

```js
var a = 42;

do {
	// ..
} while (a)	// <-- ; expected here!
a;
```

语法需要一个`;`在`do..while`循环之后，但不是在`while`或`for`循环之后。但大多数开发人员都不记得了！因此，ASI很有帮助地介入并插入一个。

正如我们在本章前面所述，语句块不需要`;`终止，所以不需要ASI：

```js
var a = 42;

while (a) {
	// ..
} // <-- no ; expected here
a;
```

ASI介入的另一个主要案例是`break`、`continue`、`return`和（es6）`yield`关键字：

```js
function foo(a) {
	if (!a) return
	a *= 2;
	// ..
}
```

`return`语句不会作用于`a * = 2`表达式，因为ASI假设为`;`终止`return`声明。当然，`return`语句很容易跨多行，只是在`return`除了换行符之外没有任何其他内容。

```js
function foo(a) {
	return (
		a * 2 + 3 / 12
	);
}
```

相同的推理适用于`break`，`continue`和`yield`。

### Error Correction

JS社区中最激烈的 *宗教战争* 之一（除了制表符与空格之外）是否严重/完全依赖ASI。

大多数（但不是全部）分号是可选的，但是两个`;`在`for(..)..`循环头中是必需的。

在本次辩论的专业方面，许多开发人员认为ASI是一种有用的机制，允许他们通过省略除严格要求的所有`;`（非常少）来编写更简洁（更“漂亮”）的代码。经常声称ASI使得很多`;`是可选的，所以一个 *不带它们* 而正确编写的程序与 *带着它们* 而正确编写的程序没什么区别。

在辩论的另一方面，许多其他开发人员将断言，有太多的地方可能是偶然的陷阱，特别是对于那些无意识的新的，经验不足的开发人员来说，这些地方的`;`意外插入会改变其含义。类似地，一些开发人员会争辩说，如果他们省略了分号，这完全是一个错误，他们希望他们的工具（linters等）能够在JS引擎纠正错误之前抓住它。

让我分享一下我的观点。严格阅读规范意味着ASI是一个“纠错”程序。你可能会问什么样的错误？具体来说，**解析器错误。** 换句话说，为了让解析器失败更少，ASI让它更宽容。

但宽容什么？在我看来，**解析器错误** 发生的唯一方法是给出一个不正确/错误的程序来解析。因此，虽然ASI正在严格的纠正解析器错误，但是它得到此类错误的唯一方法是 首先出现程序编写错误 - 省略语法规则要求的分号。

所以，更直言不讳地说，当我听到有人声称他们想要省略“可选的分号”时，我的大脑会将这种说法翻译成“我想编写破坏解析的程序，但仍然可以使用它”。

我发现这是一个可笑的立场，以及节省几次键盘敲击和让更“漂亮的代码”充其量只是软弱的论点。

此外，我不同意这与空格与制表符的争论是一样的——这纯粹是表面化的——但我认为这是一个基本问题，即编写符合语法要求的代码，而不依赖于语法异常的代码，而这些代码几乎不会滑过。

另一种看待它的方式是，依赖ASI本质上是把换行看作是重要的“空白”。像Python这样的其他语言具有真正重要的空白。但是就今天的JavaScript来说，认为它拥有有意义的换行真的合适吗？

我的看法：**使用分号，只要你知道它们是“必需的”，并将你对ASI的假设限制在最低限度。**

但是，不要只听我的话。早在2012年，JavaScript的创建者Brendan Eich（http://brendaneich.com/2012/04/the-infernal-semicolon/）表示如下：

> 这个故事的寓意：ASI（正式来说）是一种语法错误纠正程序。如果你开始编码就认为他好像是一个普遍的重要换行规则，那你就会遇到麻烦。 .. 如果回到1995年五月的那十天，我希望我使换行在JS中更有意义。注意不要使用ASI，就好像它给了JS重要的换行符。

## Errors

与运行时期间发生的所有其他错误相比，JavaScript不仅具有不同的错误子类型（`TypeError`，`ReferenceError`，`SyntaxError`等），而且语法定义了在编译时要强制执行的某些错误。

特别是，长期以来一直存在许多应该被捕获并报告为“早期错误”的特定条件（在编译期间）。任何直接语法错误都是早期错误（例如，`a = ,`），但语法也定义了语法上有效但不允许的东西。

由于你的代码尚未开始执行，因此`try..catch`无法捕获这些错误。他们将无法解析/编译你的程序。

**提示：** 规范中没有关于浏览器（和开发者工具）应该如何报告错误的具体要求。因此，你可能会在以下错误示例中看到浏览器之间的差异，报告的错误的特定子类型或包含的错误消息文本的内容。

一个简单的例子是在正则表达式字面量中使用语法。这里的JS语法没有任何问题，但无效的正则表达式会抛出一个早期错误：

```js
var a = /+foo/;		// Error!
```

赋值的目标必须是标识符（或生成一个或多个标识符的ES6解构表达式），因此该位置中的`42`之类的值是非法的，可以立即报告：

```js
var a;
42 = a;		// Error!
```

ES5的`strict`模式定义了更多的早期错误。例如，在`strict`模式中，函数参数名称不能重复：

```js
function foo(a,b,a) { }					// just fine

function bar(a,b,a) { "use strict"; }	// Error!
```

另一个`strict`模式早期错误是具有多个同名属性的对象字面量：

```js
(function(){
	"use strict";

	var a = {
		b: 42,
		b: 43
	};			// Error!
})();
```

**注意：** 从语义上讲，这样的错误在技术上不是句法错误，而是更多的语法错误 - 上面的片段在语法上是有效的。但由于没有`GrammarError`类型，因此某些浏览器使用`SyntaxError`。

### Using Variables Too Early

ES6定义了一个名为TDZ（"Temporal Dead Zone"）的新概念（坦白地说容易混淆）。

TDZ指的是代码中还不能进行变量引用的地方，因为它还没有达到所需的初始化。

最明显的例子是使用ES6 `let`块级作用域：

```js
{
	a = 2;		// ReferenceError!
	let a;
}
```

赋值`a=2`正在（它确实是在块范围`{}`中）在它被`let a`声明初始化之前访问`a`变量，所以它在`tdz`中表示`a`，并抛出一个错误。

有趣的是，虽然`typeof`有一个例外，对于未声明的变量是安全的（见第1章），但没有为TDZ的引用做出这样的安全例外：

```js
{
	typeof a;	// undefined
	typeof b;	// ReferenceError! (TDZ)
	let b;
}
```

## Function Arguments

使用ES6默认参数值是TDZ违规的另一个示例（请参阅本系列的ES6和Beyond标题）：

```js
var b = 3;

function foo( a = 42, b = a + b + 5 ) {
	// ..
}
```

赋值中的`b`引用将发生在参数`b`的TDZ中（不是请求外部`b`的引用），因此它将引发错误。但是，赋值中的`a`很好，因为到那时它已经经过参数`a`的TDZ。

使用ES6的默认参数值时，如果省略参数，或者在其位置传递`undefined`的值，则会将默认值应用于参数：

```js
function foo( a = 42, b = a + 1 ) {
	console.log( a, b );
}

foo();					// 42 43
foo( undefined );		// 42 43
foo( 5 );				// 5 6
foo( void 0, 7 );		// 42 7
foo( null );			// null 1
```

**注意：** `null`被强制转换为`a + 1`表达式中的`0`值。有关详细信息，请参阅第4章。

从ES6默认参数值的角度来看，省略参数和传递`undefined`的值之间没有区别。但是，有一种方法可以检测某些情况下的差异：

```js
function foo( a = 42, b = a + 1 ) {
	console.log(
		arguments.length, a, b,
		arguments[0], arguments[1]
	);
}

foo();					// 0 42 43 undefined undefined
foo( 10 );				// 1 10 11 10 undefined
foo( 10, undefined );	// 2 10 11 10 undefined
foo( 10, null );		// 2 10 null 10 null
```

即使默认参数值应用于`a`和`b`参数，如果在这些插槽中没有传递参数，`arguments`数组也不会有数据。

相反，如果显式传递`undefined`参数，则该参数的`arguments`数组中将存在一个条目，但它将是`undefined`的， 与应用于同一插槽的命名参数的默认值不同。

虽然ES6默认参数值可以在`arguments`数组槽和相应的命名参数变量之间创建差异，但在ES5中，同样的不连续性也可能以复杂的方式出现：

```js
function foo(a) {
	a = 42;
	console.log( arguments[0] );
}

foo( 2 );	// 42 (linked)
foo();		// undefined (not linked)
```

如果传递参数，则`arguments`槽和命名参数将链接到始终具有相同的值。如果省略参数，则不会发生此类链接。

但在`strict`模式下，无论如何都不存在联系：

```js
function foo(a) {
	"use strict";
	a = 42;
	console.log( arguments[0] );
}

foo( 2 );	// 2 (not linked)
foo();		// undefined (not linked)
```

几乎可以肯定，依赖任何这样的链接都是一个坏主意，事实上，链接本身是一个泄漏的抽象，它公开了引擎的底层实现细节，而不是一个正确设计的特性。

使用`arguments`数组已被弃用（特别是支持ES6 `...` rest参数 - 请参阅本系列的ES6和Beyond标题），但这并不意味着它一切都很糟糕。

在ES6之前，`arguments`是获取所有传递参数的数组以传递给其他函数的唯一方法，这证明是非常有用的。你还可以将命名参数与`arguments`数组混合并且是安全的，只要你遵循一个简单的规则：**永远不要同时引用命名参数及其对应的`arguments`槽。**

如果你避免这种不良做法，你将永远不会暴露泄漏的联系行为。

```js
function foo(a) {
	console.log( a + arguments[1] ); // safe!
}

foo( 10, 32 );	// 42
```

## `try..finally`

你可能熟悉`try..catch`块是如何工作的。但是你有没有停下来考虑可以与之配对的`finally`子句？事实上，你是否意识到`try`只需要`catch`或`finally`，但两者都可以在需要时出现。

`finally`子句中的代码总是运行（无论如何都会），它总是在`try`（和`catch` ，如果存在）完成之后运行， 并且在任何其他代码运行之前。从某种意义上说，你可以将`finally`子句中的代码视为在回调函数中，无论块的其余部分如何运行，都会始终调用该函数。

那么如果在`try`子句中有一个`return`语句会发生什么？它显然会返回一个值，对吗？但接收该值的调用代码是在`finally`之前还是之后运行？

```js
function foo() {
	try {
		return 42;
	}
	finally {
		console.log( "Hello" );
	}

	console.log( "never runs" );
}

console.log( foo() );
// Hello
// 42
```

`return 42`立即运行，他设置了`foo()`调用的完成值。此操作完成`try`子句，接下来`finally`子句立即运行。只有这样才能完成`foo()`函数，以便返回其完成值以供`console.log(..)`语句使用。

在`try`里`throw`具有相同的行为：

```js
function foo() {
	try {
		throw 42;
	}
	finally {
		console.log( "Hello" );
	}

	console.log( "never runs" );
}

console.log( foo() );
// Hello
// Uncaught Exception: 42
```

现在，如果在`finally`子句中抛出（意外或有意）异常，它将覆盖该函数的主要完成。如果`try`块中的先前`return`已为该函数设置了完成值，则该值将被舍弃。

```js
function foo() {
	try {
		return 42;
	}
	finally {
		throw "Oops!";
	}

	console.log( "never runs" );
}

console.log( foo() );
// Uncaught Exception: Oops!
```

其他非线性控制语句（如`continue`和`break`）表现出类似的`return`和`throw`行为，这一点不足为奇：

```js
for (var i=0; i<10; i++) {
	try {
		continue;
	}
	finally {
		console.log( i );
	}
}
// 0 1 2 3 4 5 6 7 8 9
```

`console.log(i)`语句在循环迭代结束时运行，这是由`continue`语句引起的。但是，它仍然在`i++`迭代更新语句之前运行，这就是为什么打印的值是`0..9`而不是`1..10`。

**注意：** ES6在生成器中添加了一个`yield`语句（参见本系列的Async和Performance标题），它在某些方面可以看作是一个中间`return`语句。但是，与`return`不同，在生成器重新开始之前，`yield`不会完成，这意味着`try { .. yield .. }`尚未完成。因此，附加的`finally`子句不会像`return`一样在`yield`之后运行。

`finally`中的`return`有着覆盖前一个`try`或`catch`子句中的`return`的特殊能力，但是仅在`return`被明确调用的情况下：

```js
function foo() {
	try {
		return 42;
	}
	finally {
		// no `return ..` here, so no override
	}
}

function bar() {
	try {
		return 42;
	}
	finally {
		// override previous `return 42`
		return;
	}
}

function baz() {
	try {
		return 42;
	}
	finally {
		// override previous `return 42`
		return "Hello";
	}
}

foo();	// 42
bar();	// undefined
baz();	// "Hello"
```

通常，在函数中忽略`return`与`return;`相同或者甚至与`return undefined;`相同，但是在`finally`块中，省略`return`并不像一个重写的`return undefined;`，它只是让之前的`return`生效。

事实上，如果我们`finally`将结合标记的`break`（在本章前面讨论过），我们可以真正提高疯狂度：

```js
function foo() {
	bar: {
		try {
			return 42;
		}
		finally {
			// break out of `bar` labeled block
			break bar;
		}
	}

	console.log( "Crazy" );

	return "Hello";
}

console.log( foo() );
// Crazy
// Hello
```

但是......不要这样做。我是认真的。使用`finally` +标记的`break`来有效地取消`return`，是你正在尽最大努力创建最容易混淆的代码。我敢打赌，任何注释都无法救赎这段代码。

## `switch`

让我们简要探讨一下`switch`语句，这是一种`if..else if..else ..`语句链的一种语法简写。

```js
switch (a) {
	case 2:
		// do something
		break;
	case 42:
		// do another thing
		break;
	default:
		// fallback to here
}
```

你可以看到，它计算一次`a`，然后将结果值与每个`case`表达式匹配（这里只是简单的值表达式）。如果找到匹配，则执行将在该匹配的`case`下开始，并且将一直持续到遇到`break`或者直到找到`switch`块的结尾。

这可能不会让你感到惊讶，但是有一些关于`switch`的怪异行为你以前可能没有注意到。

首先，表达式`a`和每个`case`表达式之间的匹配与`===`算法相同（参见第4章）。通常，`switch`在`case`语句中与绝对值一起使用，如上所示，因此严格匹配是合适的。

然而，你可能希望强制相等(就是`==`，参考第四章)，为了做到强制相等，你可能需要对`switch`语句进行一点“黑客”处理：

```js
var a = "42";

switch (true) {
	case a == 10:
		console.log( "10 or '10'" );
		break;
	case a == 42:
		console.log( "42 or '42'" );
		break;
	default:
		// never gets here
}
// 42 or '42'
```

这可以工作，是因为`case`子句可以有任何表达式(不仅仅是简单的值)，这意味着他将严格的匹配表达式的结果和测试表达式(`true`)。因为`a==42`在这里结果为`true`，所以匹配成功。

尽管`==`，`switch`匹配本身仍然严格，在这里介于`true`和`true`之间。如果`case `表达式结果是truthy的，但不是严格的`true`(看第四章)，这不会工作。例如，如果在表达式中使用“逻辑运算符”，例如`||`或`&&`，则可能会坑到你：

```js
var a = "hello world";
var b = 10;

switch (true) {
	case (a || b == 10):
		// never gets here
		break;
	default:
		console.log( "Oops" );
}
// Oops
```

因为`(a || b == 10)`的结果是`"hello world"`不是`true`，严格匹配失败。在这个案例里，修复方法是强制表达式显式地为`true`或`false`，例如`case !!(a || b==10):`（见第4章）。

最后，`default`子句是可选的，不一定要在末尾出现（尽管这是强约定）。即使在`default`子句里，同样的规则也适用于遇到`break`或不遇到`break`：

```js
var a = 10;

switch (a) {
	case 1:
	case 2:
		// never gets here
	default:
		console.log( "default" );
	case 3:
		console.log( "3" );
		break;
	case 4:
		console.log( "4" );
}
// default
// 3
```

**注意：** 正如前面关于带标签的`break`的讨论，`case`子句中的`break`也可以标记。

此代码段处理的方式是首先通过所有`case`子句匹配，找不到匹配，然后返回到`default`子句并开始执行。由于那里没有`break`，它会在已经跳过的`case3`块中继续执行，然后一旦它到达该`break`就停止。

虽然这种循环逻辑在JavaScript中显然是可能的，但它几乎不可能形成合理或可理解的代码。如果你发现自己想要创建这样的循环逻辑流，那就请你得好好的想想了，如果你真的这样做，请确保包含大量的代码注释来解释你的目的！

## Review

JavaScript语法有很多细微的差别，作为开发人员，我们应该花费更多的时间来关注这些细微的差别。一点点的努力可以巩固你对语言的更深层次的了解。

声明和表达式在英语中都有类似物 - 声明就像句子，表达式就像短语。表达式可以是纯/自包含的，也可以有副作用。

JavaScript语法在纯句法之上层叠语义使用规则（也称为上下文）。例如，程序中各个位置使用的`{}`对可以表示语句块，对象字面量，（ES6）解构赋值或（ES6）命名函数参数。

JavaScript运算符都有明确定义的优先级规则（首先绑定在其他之前）和关联性（多个运算符表达式如何隐式分组）。一旦你学会了这些规则，由你来决定优先级/关联性是否过于隐含于自己的用处，或者它们是否有助于编写更短，更清晰的代码。

ASI（自动分号插入）是JS引擎中内置的解析器错误更正机制，允许它在某些情况下插入假定的`;`在需要插入的地方，省略了插入，并且插入修复了解析器错误。争论这种行为是否意味着更多`;`是可选的（可以/应该省略，为了更干净的代码）或者是否意味着省略它们会导致JS引擎只为你清理错误。

JavaScript有几种类型的错误，但不太知道它有两个错误分类：“早期”（编译器抛出，不可捕获）和“运行时”（可以`try..catch`）。所有句法错误显然是在程序运行之前就停止程序的早期错误，但也有其他错误。

函数参数与其正式声明的命名参数有一个有趣的关系。具体来说，如果你不小心，`arguments`数组有许多漏洞抽象行为。如果可以，请避免使用`arguments`，但如果必须使用它，则一定要避免在`arguments`中使用位置槽的同时为同一参数使用命名参数。

`finally`子句附加到了`try`(或者`try..catch`)，在执行处理顺序方面提供了一些非常有趣的怪癖。其中一些怪癖可能会有所帮助，但是可能会造成很多混乱，特别是如果与标记的块结合使用。与往常一样，使用`finally`以使代码更好更清晰，而不是聪明过头或令人困惑。

`switch`为`if..else if ..`声明提供了一些不错的简写，但要注意关于其行为的许多常见的简化假设。如果你不小心的话，有几个怪癖可能会坑到你，但`switch`也有一些巧妙的隐藏技巧！