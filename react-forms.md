# react forms
Building forms in React requires a bit of extra work. A form is any interface that allows the user to change a large variety of data, and generally allows the user to enter into "invalid data states" when entering in data.

It's useful to think of Forms as a simple component that:
- Can be pre-populated with data
- Changes to the form are tracked within the form's own state
- Has a `onSubmit` prop to send it's modified data model back to the parent.


### basic form

In this form, we default the data state to whatever `data` props we pass in. Our props are always defaulted, in case this form ever needs to be used as a "new form" as well as an "edit form".

You should always default as much of the `data` structure as you can, so there are no surprises to future developers, and all the fields can render with _somthing_.


Adding numerous `handle*Change` functions to the form will bloat it as more fields are added. Instead we can use a generalized `updateField` function. This takes a string `path` to the specific key within the `data` as well as the value to update it to.

```
const UserForm = createClass({
	getDefaultProps : function(){
		data : {
			name : '',
			status : {
				star : false
			}
		},
		onSubmit : ()=>{}
	},
	getInitialState : function(){
		return {
			data: this.props.data
		};
	},
	updateField : function(path, val){
		this.setState(_.set(this.state.data, path, val));
	},
	render : function(){
		const data = this.state.data;
		return <div className='userForm'>
			<label>
				Name :
				<input type='text' value={data.name} onChange={(e)=>this.updateField('name', e.target.value)} />
			</label>
			<label>
				Starred :
				<input type='checkbox' checked={data.status.star} onChange={(e)=>this.updateField('status.star', !data.status.star)} />
			</label>
			<button onClick={this.props.onSubmit(this.state.data)}>Submit</button>
		</div>
	}
});

```

### metadata and validation
In our previous example, you can see we are duplicating structure with our fields. This tends to happen with forms as they are many common fields. We can write a simple generalized renderer for common fields. To do this we need to structre the information about the fields in our form; We'll do this using *metadata*. This approach still allows us to create more unique fields for complex data within our `render` function, without having to overload our `renderField` function.


```
const metadata = {
	name : {
		type : 'text',
		required : true,
		label : 'Name',
		validate : (val)=>{
			if(val.length > 30) return 'Name must be shorter than 30 characters'
		}
	},
	profilePic : {
		type : 'image'
	},
	status : {
		star : {
			type : 'boolean',
			required : false,
			label : 'Starred'
		}
	}
};

const UserForm = createClass({
	getDefaultProps : function(){ /* ... */ },
	getInitialState : function(){ /* ... */ },
	updateField : function(path, val){ /* ... */ },
	renderField : function(path){
		const data = _.get(this.state.data, path);
		const meta = _.get(metadata, path);

		const err = meta.validate ? meta.validate(data) : true;
		const isValid = !_.isString(err);

		let field;
		if(meta.type == 'text'){
			field = <input type='text' value={data} onChange={(e)=>this.updateField(path, e.target.value)} />;
		}else if(meta.type == 'boolean'){
			field = <input type='checkbox' checked={data} onChange={(e)=>this.updateField(path, !data)} />;
		}

		let errors;
		if(!isValid) errors = <div className='errors'>{err}</div>;

		return <label className={cx({invalid : !isValid, required : meta.required})}>
			{meta.label}
			{field}
			{errors}
		</label>;
	},
	renderComplexImageEditor : function(){ /* ... */ },
	render : function(){
		return <div className='userForm'>
			{this.renderField('name')}
			{this.renderComplexImageEditor()}
			{this.renderField('status.star')}
			<button onClick={this.props.onSubmit(this.state.data)}>Submit</button>
		</div>
	}
});
```

our `metadata` follows the exact same key strucutre as our `data`, however at the leafs it has the information regarding that field. Storing labels, validation functions, is required, icons, etc. per field becomes easy and straitforward. This metadata object can be stored in an external `js` file if it becomes large. As well our `renderField` function can be easily broken out into a subcomponent if it becomes large and complex.

### validation
Our validation function will be passed the current data of the field, and returns a string of why it's invalid, otherwise it's considered valid.

If using metadata with validation, complete form validation becomes easy.

```
const metadata = {
	/* ... */
}

const UserForm = createClass({
	getDefaultProps : function(){ /* ... */ },
	getInitialState : function(){ /* ... */ },

	isFormValid : function(){
		const check = (data, meta)=>{
			return _.some(data, (val, key)=>{
				if(_.isPlainObject(val)) return check(val, meta[key]);
				return _.isString(_.invoke(meta[key], 'validate', val));
			});
		}
		return check(this.state.data, metadata);
	},
	updateField : function(path, val){ /* ... */ },
	renderField : function(path){  /* ... */ },

	render : function(){
		const data = this.state.data;
		const isValid = this.isFormValid();

		return <div className={cx('userForm', {invalid : !isValid})} >
			{this.renderField('name')}
			{this.renderField('status.star')}
			<button onClick={this.props.onSubmit(this.state.data)} disabled={!isValid} >Submit</button>
		</div>
	}
});
```


### updating the form
Someitmes you need to update the form's data even while the user is currently editing it. There are two approaches:

#### partial update
Some event has updated information and you would like the new information to be pushed to the form, you would push this new information via props. However you do not want the current data to be overridden, so in this case we create a `componentWillReceiveProps` and write logic how we should update our state via the props being sent.

In this example, we want our form to have a pending state while we save the information gathered from a `onSubmit`

```
const UserForm = createClass({
	getDefaultProps : function(){ /* ... */ },
	getInitialState : function(){
		return {
			data: this.props.data,
			isPending: this.props.isPending
		}
	},
	componentWillReceiveProps : function(nextProps){
		this.setState({
			isPending : nextProps.isPending
		})
	},
	updateField : function(path, val){ /* ... */ },
	renderField : function(path){ /* ... */ },
	renderButton : function(){
		if(this.state.isPending){
			return <button><i className='fa fa-spin fa-spinner' /> Saving<button>
		}
		return <button onClick={this.props.onSubmit(this.state.data)}>Submit</button>
	},
	render : function(){
		return <div className='userForm'>
			{this.renderField('name')}
			{this.renderField('status.star')}
			{this.renderButton()}
		</div>
	}
});
```

#### refresh form
Sometimes we'd like the entire form to be refreshed, potentially with new data. The easiest way to accomplish this is by changing the `key` in which the form was invoked with. It usually best to use a unique id from the data model for the form key, that way if the user wishes to edit different data models, our form will be reloaded properly.


In this example, if the `selectedUserId` is changed, the form will fully reload with different information.

```
const Page = createClass({
	getDefaultProps : function(){
		users : {}
	},
	getInitialState : function(){
		return {
			selectedUserId : ''
		}
	},
	getSelectedUser : function(){
		return this.props.users[this.state.selectedUserId];
	},
	render : function(){
		const user = this.getSelectedUser();
		return <div className='userPage'>
			<UserForm data={user} key={user.id} />
		</div>
	}
});
```

