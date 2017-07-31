# react forms


Form state variable data
metadata
	validator,
	required
	icon
	label

use _.set syntax
isValid on Form

use renderField to build out fields



```javascript

const metadata = {
	name : {
		required : true,
		label : 'User Name',
		icon : 'fa-user',
		validator: (val)=>true
	}
}

const UserForm = createClass({
	getInitialState: function() {
		return {
			data: {
				name : 'Brock'
			}
		};
	},

	renderField : function(value, metadata, onChange){
		const invalidText = metadata.validator(value);
		const isValid = invalidText === true;
		let InvalidField = null;
		if(_.isString(invalid)){
			InvalidField = <div className='invalid'>{invalidText}</div>
		}
		return <div className={cx('field', path, {invalid : !isValid})}>
			<label><i className={'fa ' + metadata.icon} /> {metadata.label} </label>
			<input type='text' value={data} onChange={onChange} />
			{InvalidField}
		</div>
	},

	renderFields : function(){
		return _.map(this.state.form, (val, path)=>{
			const updateState = (val)=>this.setState(_.set(this.state, path, val));
			return this.renderField(val, metadata[path], updateState);
		})
	},
	render : function(){
		return <div className='userForm'>
			{this.renderField('name')}
		</div>
	}
});
```