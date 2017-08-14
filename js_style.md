# Javascript Style



### Object Parameters

Having many parameters in a function puts a lot of cognitive weight onto the developer to remember the exact sequence of params for the function signature. Try using an object-param to capture non-essential/less-important params for the function. This has the added benefit of not breaking your code if you need to remove one of the parameters later.

```js
//Bad
const myFunc = (source, target, useLines, shouldSave=true, beFunky=false, maxCount)=>{}

//Good
const myFunc = (source, target, opts={})=>{
	opts = _.defaults(opts, {useLines:true, shouldSave:true, beFunky:false, maxCount:3})
}
```



### Classes
Classes in js are just syntax sugar. Many people have tried to implement class-based inheritance by manipulating Javascript's prototype chain. While possible, this can lead to many strange and extreme bugs. The Class syntax was introduced in ES6 to provide a unified way to manipulating the protptype chain to produce class-based inheritance that many programmers are familar with. However it's possible to avoid this problem all together by properly understanding the language and use functional and composition programming techniques. We want to discourage "fighting" the language and not just to use language features just because they are there and use them when they make actual sense. If you feel you have a legitiment use-case for using a Class, you can override this rule and provide explanation.

### Switch Cases
Switch Cases in js were poorly designed. They [not faster](https://stackoverflow.com/questions/8624939/performance-of-if-else-switch-or-map-based-conditioning) than other methods, also each case *must* have a `break;` at then end of it or it will cascade into the next case. This can produce unseens bugs that are _very_ hard to reproduce and track down.

The better approach is to produce an object mapping of your 'cases' and 'results'. This has the added benefit of easily being passed around, dynamically generated, or externalized into a config file if it becomes very large.

```js
const hrefMap = {
	camelCase : "http://www.thecamelcase.com",
	jsFiddle  : "http://www.jsfiddle.net",
	cricInfo  : "http://www.cricinfo.com",
	apple     : "http://www.apple.com",
	yahoo     : "http://www.yahoo.com"
};
window.location.href = hrefMap[id];
```
