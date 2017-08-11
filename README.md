# docs
A collection of my docs, dot files, and anything else related to coding.



Slack docs
Heroku docs
Setup docs


### Style docs
- Modifier classes should be kept as small as possible

## JS standards

- Comment dividers, /** sdfdsf **/


frontend standards (eslint here)


## React Component Guide

### State
- No duplicate or generated values in your state.
- Your state should represent the absolute minimum amount of data


react app guide
Testing


## JS

### No Classes


### No Switch statements
Switch Cases in js were poorly designed. They [not faster](https://stackoverflow.com/questions/8624939/performance-of-if-else-switch-or-map-based-conditioning) than other methods, also each case *must* have a `break;` at then end of it or it will cascade into the next case. This can produce unseens bugs that are _very_ hard to reproduce and track down.

The better approach is to produce an object mapping of your 'cases' and 'results'. This has the added benefit of easily being passed around, dynamically generated, or externalized into a config file if it becomes very large. Lodash's [`_.result`](https://lodash.com/docs/4.17.4#result) is excellent for this, providing pure valueand functional cases, as well as defaults.

```js
const getColor = (name, default='#333')=>{
	const mapping = {
		red : '#c0392b',
		blue : '#2980b9',
		green : '#27ae60',
		rand : ()=>_.sample(_.values(mapping))
	};
	return _.result(mapping, name.toLower(), default)
};
getColor('Green'); //'#27ae60'
getColor('Maroon'); //'#333'
```


Use Arrow Functions
