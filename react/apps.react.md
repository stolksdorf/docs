## how to design react apps

1. List out all the _changeable_ data within your system
- The exception is data that is isolated to a *single* component
  - Visibility toggle, forms, simple request-and-confirm async requests





2. Draw a box-model of your app
- Try to figure out component heirarchy, which components are built up of others
- Look for repeated components
- Figure out the minimum number of smart components you would need
  - Eg. we could make every navItem smart, or just make a single smart navbar that feds the needed information to the navItems

3. List how "agents" (users or other systems) can act on your app
_examples_
- User login
- New data from a socket
- User completes a form

These will become your actions


4. Figure out the minimum number of store mutators to satisify your actions




## flux

### Actions

- Pass actions in as prop to components whenever you can. It keeps dumb components easily testable.
- If a component needs an action, and it doesn't make sense for the parent to require it, set it in the default props
- Actions should encapsulate async operations. Return promises to them.
- You can have many actions. They should describe exactly what is happening. (change selection example)



-


## stores
- Stores contain three main parts; state, mutators, and getters

### state
- There should be no repeated data within your state
- Structure your data to reduce the amount of manipulation required to do common operations
  - eg. Having an array that you scan for an object with a specific id could be more efficient if you convert it object mapping instead of an array.
- Try to group siilar concepts together. Your state shouldn't be incredibly shallow, nor incredibly deep.
	- Grouping all the selected ids under a `selected` key helps with a concise structure



### mutators
- It's useful to have an `INIT` mutator for completely changing the current state
- You should have as few mutators as possible.
- Mutators should not have any async
- Mutators should have minimal logic. Try to add as much logic to actions as possible. Acceptable logic is figuring out if it should trigger an update, or filtering/sorting the incoming data.

### getters
- Use getters to encapsulate logic for extracting certain subsets of data from the state
- Getters can be passed parameters
- Getters allow you to change the shape of the data in the state to suit your components needs. Try to keep data structure logic in getters and out of components.

- If a getter needs to do an expensive operation and it's result should be cached, break that logic off into a separate `*.cache.js` file. The getter should initiate interaction with that caching layer

```javascript
// avg.cache.js
let cache = {
	user1 : {
		hash : '2gfh475jg8sdf',
		val : 28
	}
}
const getUserAvg = (user, data)=>{
	if(!cache[user] || cache[user].hash !== hash(data)){
		cache[user] = {
			val : calcAvg(data),
			hash : hash(data)
		};
	}
	return cache[user].val;
};
```


### async stores & actions
With apps that deal with many concurrent async connections and would benefit from caching, sometimes you want to create an entire store for managing these connections and cache-layer.

- Actions will first check the cache to see if the request has been completed.
- Actions will return a unique id per request that the component can use to request the status and resulting data
-


## smart components
Components should not directly access actions or stores. Smart components wrap existing components

- You should have as few smart components as possible.
- Don't make smart components in iterators
