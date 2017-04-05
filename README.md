# SCHEMA

<img src="https://img.shields.io/badge/build-passing-brightgreen.svg" />
<img src="https://img.shields.io/badge/coverage-100%25-brightgreen.svg" />
<img src="https://img.shields.io/badge/license-MIT-blue.svg" />
<img src="https://img.shields.io/badge/npm-v1.3.0-blue.svg" />

[1. Installation](#installation)<br />
[2. How to use](#how-to-use)<br />
[3. Methods](#methods)<br />
[4. Example of custom validator](#example-of-custom-validator)<br />
[5. Schema definition Example](#schema-definition-example)<br />
[6. Example of schema in schema](#example-of-schema-in-schema)<br />
[7. Schema keys description](#schema-keys-description)<br />
[8. Custom validation messages](#custom-validation-messages)<br />


### Installation

```bash
$ npm install form-schema-validation --save
```

### How to use

Schema give you posibility to validate object using schema validation. You can defined schema and use validate method to check object. Validate method allways returns errors object but if You don't have errors object is empty so You can check errors by 
```js
import Schema from 'form-schema-validation';

const schema = new Schema({
    companyName: {
        type: String
    }
});

const modelObject = {
    companyName: 'Test Company'
};

const errors = schema.validate(modelObject); // {}
const error = Object.keys(errors).length > 0; // false
```

### Promises support

You can use validators that return Promise. If You return promis in validator then shema.validate(model) will return Promise.

```js
import Schema from 'form-schema-validation';

const customValidator = {
    validator: (value) => {
        return new Promise((resolve) => {
            setTimeout(() => {
                resolve(value === 'test');
            }, 1000);
        });
    },
    errorMessage: 'async test error';
}

const schema = new Schema({
    companyName: {
        type: String,
        validators:[customValidator]
    }
});

const modelObject = {
    companyName: 'Test Company'
};

const results = schema.validate(modelObject); // Promise
results.then((errors) => {
    console.log(Object.keys(errors).length > 0); // true
});
```

### Methods

| Name | Attributes | Description |
|---|---|---|
| validate | model: Object | Validate Object using defined schema |
| getDefaultValues |  | Get default values for model using defined schema |
| getField | name: String | Get field schema |
| getField |  | Get all fields schemas |

#### Example of custom validator
This validator will check two fields. You can validate one field on base another field.
```js
const validateIfFieldTitleIsFilled = (minLength, message) => ({
    validator: (value, fieldSchema, formData) => {
        if(formData.title){
            return !!value;
        }
        return true;
    },
    errorMessage: message
});
```

### Schema definition Example

If You want create new schema You must put object to constructor with information about object keys names and type of value on key.

```js
import Schema from 'form-schema-validation';

const min = (minLength, message) => ({
    validator: (value) => {
        return value.length > minLength;
    },
    errorMessage: message
});

const schema = new Schema({
    companyName: {
        type: String,
        required: true,
        label: 'Company name'
        validators: [min(2, 'Company name should be longer then 2 chars')]
    },
    createdAt: {
        type: Date
        defaultValue: new Date(),
        label: 'When start'
    },
    workers: {
        type: Number
        label: 'How many workers we have'
    }
});
```

##### Example of schema in schema

```js
import Schema from 'form-schema-validation';

const userSchema = new Schema({
    name: {
        type: String,
        required: true
    },
    surname: {
        type: String,
        required: true
    },
    age: {
        type: Number
    }
});

const groupSchema = new Schema({
    name: {
        type: String,
        required: true,
        label: 'Group name'
    },
    createdAt: {
        type: Date
        defaultValue: new Date(),
        label: 'Created at'
    },
    members: {
        type: [userSchema]
        label: 'Members'
    }
});
```

#### Schema keys description

When You defined schema You can use this keys: 

| Key | Allowed values | Description |
|---|---|---|
| companyName, createdAt, workers, ... | any name | this key defined object key name |
| type | String, Number, Object, Date, Boolean, Array, instance of Schema, [String] ... | this key tell as what type of value we should have on this key in model |
| required | true, false | this key tell as that field is required |
| defaultValue | Any | You can set default value for model |
| options | Array of (String, Number, Object, Date, ...) | If you use schema for forms You can defined options for select field |
| label | Any instance of String | If you use schema for forms You can defined label for form field |
| validators | array of Functions | You can add custom validators for validate field for example min or max length of value. |


#### Custom validation messages
```js
import Schema from 'form-schema-validation';

ErrorMessages = {
    notDefinedKey(key) { return `Key '${key}' is not defined in schema`; },
    modelIsUndefined() { return 'Validated model is undefined'; },
    validateRequired(key) { return `Field '${key}' is required`; },
    validateString(key) { return `Field '${key}' is not a String`; },
    validateNumber(key) { return `Field '${key}' is not a Number`; },
    validateObject(key) { return `Field '${key}' is not a Object`; },
    validateArray(key) { return `Field '${key}' is not a Array`; },
    validateBoolean(key) { return `Field '${key}' is not a Boolean`; },
    validateDate(key) { return `Field '${key}' is not a Date`; }
};

const groupSchema = new Schema({
    name: {
        type: String,
        required: true,
        label: 'Group name'
    },
    createdAt: {
        type: Date
        defaultValue: new Date(),
        label: 'Created at'
    },
    members: {
        type: [userSchema]
        label: 'Members'
    }
});

```