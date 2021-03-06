# eslint


Whenever you can you should try and use rules that can be auto-fixed using the `--fix`. Use errors very sparringly. Warnings are reserved for rules that _can_ be over-ridden for a good reason.

There should never be any errors or warnings for any project.

Always use `.json` prefix for `eslint` files



## `package.json`
Ignored files should be outlined in your `package.json`. Use the `--fix` option whenever you lint, as most of the rules were selected to be auto-fixed.


```json
{
	"scripts" : {
		"lint": "eslint --fix **/*.{js,jsx}"
	},
	"eslintIgnore": [
		"build",
		"*.built.js"
	],
}
```


## rules

### errors


#### [`"no-new-object": "error"`](http://eslint.org/docs/rules/no-new-object), [`"no-array-constructor": "error"`](http://eslint.org/docs/rules/no-array-constructor)
These both stop from using the `new` keyword when creating objects and arrays. It's more concise to just use the literal syntax, `let obj = {};`.



#### [`"no-iterator": "error"`](http://eslint.org/docs/rules/no-iterator), [`"no-proto": "error",`](http://eslint.org/docs/rules/no-proto)
These both stop using an obsolete syntax which can lead to errors and odd behaviour in different environments.


#### [`"no-nested-ternary": "error"`](http://eslint.org/docs/rules/no-nested-ternary)
Please never do this, it's super confusing.


#### [`"func-style": ["error", "expression", {"allowArrowFunctions": true}]`](http://eslint.org/docs/rules/func-style)
Functions are first-class citizens in javascript and should be treated as such. Using functional declaration can also cause global scoping issues on certain platforms.


#### [`"camelcase": ["error", {"properties": "never"}]`](http://eslint.org/docs/rules/camelcase)
Enforces the use of `camelCased`, `CAPS_CASE`, and `TitleCase` for variable names. We are excluding object property checking as there may be cases where you need to modify existing library's variables.


#### [`"react/prefer-es6-class": ["error", "never"]`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md)
Enforces the use of the `createReactClass({...})` syntax instead of extending the `React.Component` class. There are many disadvantages to using React ES6 classes.

- Methods within them are not [autobound](https://facebook.github.io/react/docs/react-without-es6.html#autobinding) which means you have to explicitly do this within a constructor. Forgetting to do so can lead to incredibly difficult bugs.
- It promotes the use of Javascript Classes, which are just [syntax sugar](https://stackoverflow.com/questions/36419713/are-es6-classes-just-syntactic-sugar-for-the-prototypal-pattern-in-javascript) and are not actual classes.
- Default props must be defined _outside_ of the class definition, but initial state is defined _within_ the class constructor.

#### [`"react/no-multi-comp": ": ["error", {"ignoreStateless": true }]`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md)

Enforces one react component per file. Stateless/pure functions are ignored. Multiple stateful components should warrant multiple files.




#### [`"react/jsx-no-bind" : ["error", {"allowArrowFunctions" : true}]`](https://github.com/yannickcr/eslint-plugin-react/blob/bd23406f003dc85e06a6fc68b63e81a8a89ec257/docs/rules/jsx-no-bind.md)
Binding parameters to component handlers can produce code that "hides" functionality of the overflowing parameters. using an alias function or pre-building the function produces cleaner and easier to debug code.

```js
//Bad, unclear that handleClick requires the event param
<div onClick={this.handleClick.bind(this)}></div>

//Good, all clear!
<div onClick={(e)=>this.handleClick(e)}></div>
```


### warnings
Warnings are reserved for rules in which you can provide exceptions for on a per file basis. For example, we have an eslint rule to limit the max file length, however we know they will be some files that simply can not be shortened. In that case to resolve the warning you can place a comment at the top of the file disabling the rule while providing reasoning behind it.

```js
/* eslint-disable max-lines */
// This is just a massive config file, no need to break it up

module.exports = {
	// looooooooong file
};
```

#### [`"no-restricted-syntax": ["warn", "ClassDeclaration", "SwitchStatement"]`](http://eslint.org/docs/rules/no-restricted-syntax)

This rule provides warnings about using [ES6 Classe Syntax](js_style.md#Classes) and [Switch Cases}(js_style.md#Switch_Cases).


#### [`"max-lines" : ["warn", {"max": 250, "skipComments": true, "skipBlankLines": true}]`](http://eslint.org/docs/rules/max-lines)
Files provide us with a natural form of encapsulation. Dependacies are declared at the top, and what this file exports are the bottom, almost like mini-APIs. The more focused and concise the functionality of a single file, the easier it is to maintain, improve, and re-use. As the number of lines increase within a file, so does it's complexity and the number of interconnected parts. We've found that around 250 lines is the sweet-spot of file complexity.

Try splitting out common/agnostic functions into a small `utils.js` file. There are always cases where this rule will not apply, so use the ability to provide a single file override and explain why this file is an exception to this rule.

#### [`"max-params" : ["warn", {"max": 4}]`](http://eslint.org/docs/rules/max-params)
Having many parameters in a function puts a lot of cognitive weight onto the developer to remember the exact sequence of params for the function signature. Try using an object-param to capture non-essential/less-important params for the function. This has the added benefit of not breaking your code if you need to remove one of the parameters later.

```js
//Bad
const myFunc = (source, target, useLines, shouldSave=true, beFunky=false, maxCount)=>{}

//Good
const myFunc = (source, target, opts={})=>{
	opts = _.defaults(opts, {useLines:true, shouldSave:true, beFunky:false, maxCount:3})
}
```

#### [`"react/prefer-stateless-function": ["warn"]`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)
There are many performance optimizations done under the hood for pure components, so we should prefer them when possible.


### fixable
The following rules are auto-magically [fixable](http://eslint.org/docs/user-guide/migrating-from-jscs#--fix). We suggest first commiting your work, then running `eslint --fix` so you can double check the diff before you commit. All of these fixable rules should be safe to execute though.

#### [`"quotes": ["warn", "single"]`](http://eslint.org/docs/rules/quotes), [`"prefer-template": "warn"`](http://eslint.org/docs/rules/prefer-template), [`"template-curly-spacing": ["warn", "never"]`](http://eslint.org/docs/rules/template-curly-spacing)
Prefer single quotes, removing extra spacing in template strings, and instead of string concatenation, uses template strings.


#### [`"no-var": "warn"`](https://eslint.org/docs/rules/no-var), [`"prefer-const": "warn"`](https://eslint.org/docs/rules/prefer-const)
Always use either `const` and `let`. `let` should only be used if the variable is actually being manipulated. This helps the developer track which variables should actually be mutated.


#### [`"arrow-parens": ["warn", "always"]`]()
When using arrows functions, always surround parameters in braces. This helps make our code more readable, especially when chaining arrow functions together.

```js
//bad, are these inequity operators?
const currAdd = a=>b=>a+b;

//good
const currAdd = (a)=>(b)=>a+b;
```

#### [`"semi": ["warn", "always"]`]()
Always have semicolons. Duh.

#### [`"indent": ["warn", "tab"]`]()
Always use tabs for indent. Tabs allow the developer to choose how large of indent they prefer, without changing the source code. It also stops issues of selecting or pasting partial indents. If you want to align code up, use indents to define the block depth, then spaces to align by character.

#### [`"key-spacing": ["warn", {"multiLine": {"beforeColon": true, "afterColon": true, "align": "colon"}}]`](http://eslint.org/docs/rules/key-spacing)
Aligns all the colons up for a multiline object with key-value pairs, with a space before and after the colon.

```js
//bad
call({
	foobar: 42,
	bat:2 * 2
});

//good
call({
	foobar : 42,
	bat    : 2 * 2
});
```


### Whitespace
- [`"linebreak-style": ["warn", "unix"]`](http://eslint.org/docs/rules/linebreak-style) - Only use unix style linebreaks.
- [`"object-curly-spacing": ["warn", "never"]`](http://eslint.org/docs/rules/object-curly-spacing) - Removes spacing between curly braces.
- [`"no-whitespace-before-property": "warn"`](http://eslint.org/docs/rules/no-whitespace-before-property) - Removes whitespace between property accessors
- [`"space-in-parens": ["warn", "never"]`](http://eslint.org/docs/rules/space-in-parens) - Removes spacing between braces
- [`"comma-spacing": ["warn", {"before": false, "after": true}]`](http://eslint.org/docs/rules/comma-spacing) - Always have a space after comma, never before
- [`"keyword-spacing": ["warn", {"before": false, "after": false}]`](http://eslint.org/docs/rules/keyword-spacing) - No spaces inbetween keywords and braces.

