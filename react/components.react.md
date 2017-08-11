# React/JSX Style Guide

*A mostly reasonable approach to React and JSX*

## Table of Contents

1. [Basic Rules](#basic-rules)
1. [Method Ordering](#method-ordering)
1. [`render` functions](#render-function)
1. [Observers and Handlers](#observers-and-handlers)
1. [`createClass` vs Pure Components](#react-create-class-vs-pure-components)
1. [Default Props](#default-props)
1. [Naming](#naming)
1. [Spacing](#spacing)
1. [Props](#props)
1. [Alignment](#alignment)
1. [Tags](#tags)
1. [Methods](#methods)
1. [`isMounted`](#is-mounted)


### Basic Rules

- Only include one React component per file.
  - However, multiple [Stateless, or Pure, Components](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) are allowed per file.
- Always use JSX syntax.
- No logic within `render`
- Always define `defaultProps`
- Don't use [mixins](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html)
- Never mutate `props` or `state`
- The less interaction between state and props, the better
- Make your props as simple as possible
- A component should not require in a store or actions, use a smart component



### general design

When designing your component, you should consider it like designing a function; carefully define all inputs it needs.





### Method Ordering

All methods within your componenet should be ordered using the following list:

1. `getDefaultProps`
1. `getInitialState`
1. [Lifecycle Methods](https://facebook.github.io/react/docs/component-specs.html#lifecycle-methods), eg. `componentWillMount`, `componentDidMount`
1. Event Handlers like `handleSubmitClick()` or `handleChangeDescription()`
1. Getter methods for `render*` functions like `getSelectReason()` or `getFooterContent()`
1. Additional render methods like `renderNavigation()` or `renderProfilePicture()`
1. `render`


### `render` function

- The logic within your `render` function should be kept at a minimum. If there is any looping, or complex conditions, it should be broken out into it's own `render*` method.

```jsx
	// bad
	const Foo = createClass({
		render : function() {
			return <ul className="items">
				{_.map(this.props.items, (item) => {
					return <li key={item.id}>{item.text}</li>
				})}
			</ul>;
		}
	});

	// good
	const Foo = createClass({
		renderItems : function(){
			return _.map(this.props.items, (item) => {
				return <li key={item.id}>{item.text}</li>
			})
		},
		render : function() {
			return <ul className="items">
				{this.renderItems()}
			</ul>;
		}
	});
```

- `render*` functions are good candidates to be broken out into sub-components if the file becomes large.

- `render*` functions should return the whole wrapper for the elements.

```jsx
// bad
const Foo = createClass({
	renderProgress : function(){
		return <progress value={this.props.val} max='100'/>
	},
	render : function() {
		return <div className='foo'>
			<div className='progress'>{this.renderProgress()}</div>
		</div>
	}
});

// good
const Foo = createClass({
	renderProgress : function(){
		return <div className='progress'>
			<progress value={this.props.val} max='100'/>
		</div>
	},
	render : function() {
		return <div className='foo'>
			{this.renderProgress()}
		</div>
	}
});
```

- Don't have components render to non-top-level HTML elements (eg. `li`, `thead`). This limits their ability to be modular. The exception to this is if the component is made as direct sub-component.


### Observers and Handlers
- Any prop passed to a component that is a function meant to be an "observer" should be prefixed by `on*`, eg. `onChange`, `onSubmit`, `onUserSelect`


```jsx
	// bad
	<Foo click={this.handler} whenUserSubmits={this.handleSubmit} />

	// good
	<Foo onClick={this.handler} onUserSubmit={this.handleSubmit} />
```

- Use `onChange` and `value` as prop names when you can, to match native HTML components.

- Any function within a function that reacts to external changes should be prefixed with `handle*`, eg. `handleKeyPress`, `handleUserSelect`

```jsx
	// bad
	const Foo = createClass({
		onClick : function(){
			/* ... */
		},
		render : function() {
			return <button onClick={this.onClick}>Click me!</button>;
		}
	});

	// good
	const Foo = createClass({
		handleClick : function(){
			/* ... */
		},
		render : function() {
			return <button onClick={this.handleClick}>Click me!</button>;
		}
	});
```


- It's common that a `handle*` function may just call an observer prop. This is fine, as often additional logic or logging will usually be added later.

```jsx
	// bad
	const Foo = createClass({
		getDefaultProps : function(){
			return {
				onClick : ()=>{}
			}
		},
		render : function() {
			return <button onClick={this.props.onClick}>Click me!</button>;
		}
	});
	// good
	const Foo = createClass({
		getDefaultProps : function(){
			return {
				onClick : ()=>{}
			}
		},
		handleClick : function(){
			this.props.onClick();
		},
		render : function() {
			return <button onClick={this.handleClick}>Click me!</button>;
		}
	});
```
- Use



### `createClass` vs Pure Components

- Use the `createClass` syntax for creating components

```jsx
	// good
	const Listing = createClass({
		// ...
		render : function() {
			return <div>{this.state.hello}</div>;
		}
	}
```

- If your componenet does not use state of refs, use function notation instead. There are many optimizations under React's hood for rendering functional components.

```jsx
	// bad
	const Listing = createClass({
		render : function() {
			return <div>{this.props.hello}</div>;
		}
	})

	// good
	const Listing = ({ hello })=>{
		return <div>{hello}</div>;
	}
```

### Default Props
- If your component is expecting props, they should be defined within the `getDefaultProps`

> Why not `propTypes`? You can use [Prop Validation](https://facebook.github.io/react/docs/reusable-components.html#prop-validation), but our preferred method is prop defaulting. Validation tends to be more strict and less backwards compatible when working with shared components.

```jsx
	// bad
	const Foo = createClass({ /* ... */ });

	<Foo unexpectedProp="Neat!" />

	// good
	const Foo = createClass({
		getDefaultProps : function(){
			return {
				unexpectedProp : ""
			}
		}
		/* ... */
	});
```

- Especially make sure to stub out any handlers with noop functions

```jsx

	// good
	const Foo = createClass({
		getDefaultProps : function(){
			return {
				onChange : ()=>{}
			}
		}
	});
```


### Naming

- **Extensions**: Use `.jsx` extension for React components.
- **Filename**: Use camelCase for filenames. E.g., `reservationCard.jsx`.
- **Reference Naming**: Use PascalCase for React components.

```jsx
	// bad
	const reservationCard = require('./ReservationCard.jsx');

	// good
	const ReservationCard = require('./reservationCard.jsx');
```

- **Component Naming**: Use the filename as the component name. For example, `reservationCard.jsx` should have a reference name of `ReservationCard`.

### Spacing

- Always include a single space in your self-closing tag.

```jsx
	// bad
	<Foo/>

	// very bad
	<Foo                 />

	// bad
	<Foo
	 />

	// good
	<Foo />
```

### Props

- Always use camelCase for prop names.

```jsx
	// bad
	<Foo
		UserName="hello"
		phone_number={12345678}
	/>

	// good
	<Foo
		userName="hello"
		phoneNumber={12345678}
	/>
```

### Alignment

```jsx
	// bad
	render() {
		return <MyComponent
		className="long body" foo="bar">
							<MyChild />
				</MyComponent>;
	}

	// good
	render() {
		return <MyComponent className="long body" foo="bar">
			<MyChild />
		</MyComponent>
	}

	// good, when single line
	render() {
		const body = <div>hello</div>;
		return <MyComponent>{body}</MyComponent>;
	}
```

### Tags

- Always self-close tags that have no children.

```jsx
	// bad
	<Foo className="stuff"></Foo>

	// good
	<Foo className="stuff" />
```

### Methods

- Do not use underscore prefix for internal methods of a React component.

```jsx
	// bad
	createClass({
		_checkSecretThing : function() {
			// do stuff
		},

		// other stuff
	});

	// good
	createClass({
		checkSecretThing : function() {
			// do stuff
		}

		// other stuff
	}
```


### `isMounted`

- Do not use `isMounted`.

> Why? [`isMounted` is an anti-pattern][anti-pattern], is not available when using ES6 classes, and is on its way to being officially deprecated.

[anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html
