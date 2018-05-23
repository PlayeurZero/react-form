# React Form

> Draft: work in progress

A suit of utilities to manage forms in React.

## Install

To install, run in a terminal&#x202f;:

```bash
# using npm
npm install --save playeurzero/react-form
# using yarn
yarn add playeurzero/react-form
```

### Install a specific version

```bash
# using npm
npm install --save playeurzero/react-form#v1.0.0
# using yarn
yarn add playeurzero/react-form#v1.0.0
```

## Usage

### Usage in TypeScript

```tsx
import * as React from 'react'
import { default as ReactForm, Form, Field } from 'react-form'

const requestAdapter =
  (options) =>
    (onRequestSuccess, onRequestFailure) => {
      myFunctionToFetch(options.action, options.method, { headers: "" }).then(onRequestSuccess).catch(onRequestFailure)
    }

ReactForm.setRequestAdapter(requestAdapter)

class MyForm extends React.Component {
  render() {
    return (
      <Form
        options={{
          action: "/payment",
          method: "post",
        }}
        validate
        initialFields={{ firstname: "Jean" }}
        onRequestSuccess={() => {}}
        onRequestFailure={() => {}}
        requestAdapter={requestAdapter}
      >
        <Field
          name="lastname"
          required
          validate={/[A-z][A-z\d-]{,49}/}
        >
          <input type="text" />
          
          {({ error, value }) => (
            null != error && `There is an error with this field. Error: ${error}. Given value: ${value}`
          )}
        </Field>

        {/* Field can be placed anywhere inside <Form>, not only in the first level */}
        <div>
          <Field name="firstname">
            <input type="text" />
          </Field>
        </div>

        <Field
          name="evenNumber"
          defaultValue="6"
          required
          validate={(value => parseInt(value) % 2 === 0)}
        >
          <input type="text" />
        </Field>

        <Field name="phone">
          <Input type="number" addonBefore={(
            <Field name="phonePrefix">
              <select>
                <option value="">Phone prefix</option>
                <option value="33">+33</option>
                <option value="34">+34</option>
              </select>
            </Field>
          )} />
        </Field>

        <Field name="price">
          <Input
            type="number"
            formatter={value => `${value} €`.replace('.', ',').replace(/\B(?=(\d{3})+(?!\d))/g, ' ')}
            parser={value => value.replace(',', '.').replace(/\s€?/g, '')}
          />
        </Field>

        {/* do something */}
        <button type="button">Cancel</button>

        <button>Continue</button>
      </Form>
    )
  }
}

export { MyForm as default }
```

## Description

### \<Form> props

#### options

> `IOptions`

Options that will be passed to the `requestAdapter` function.

#### validate

> `boolean?` = `true`

Should the form be validated before send.

#### initialFields

> `IFields?` = `{}`

#### onRequestSuccess

> `(...args: any[]): void`

`...args` Depends of what the `requestAdapter` sends

Callback executed when the request has been done.

#### onRequestFailure

> `(...args: any[]): void`

`...args` Depends of what the `requestAdapter` sends

Callback executed when the request cannot be done.

#### children

> `((api) => React.ReactElement<any> | <React.ReactElement<any>)`

If the prop is a function, an `api` object will pass useful parameters:
- `fields` the list of fields
- `validatings` the list of fields
- `submitting` indicates the form is submitting data and waits for response
- all methods that are described above

### \<Form> methods

#### isValid

> `(<Form {...props}>).isValid(): boolean`

Returns if the form is globally valid or not.

#### submit

> `(<Form {...props}>).submit(): void`

Force the form to be submitted.

#### reset

> `(<Form {...props}>).reset(): void`

Rebuild the form as it was at it initial state.

#### setField

> `(<Form {...props}>).setField(name: string, value: string): void`

Add, remove or modify a field.

Set value to `void(0)` (unsafe equivalent: `undefined`) or `null` to delete a field.

### \<Field> props

#### name

> `string`

The name of the field and what will be sent to the request.

#### defaultValue

> `string`

The default value of the value when it will be mounted.

#### required

> `boolean?` = `false`

Indicates if the field is required.
When the form will be submitted, and if this prop is set to `true`, the form will use `validate` prop to check if the value match the requirements.

#### validate

> `(value => boolean | RegExp)?` = `value => value != ""`

#### children

> `React.ReactElement<any>`

Direct components SHOULD be a field. That means the component should provides few props:
- `value` a controlled input
- `onChange(event: InputEvent | SyntheticEvent)` a callback triggered each time the component receive data by the user
- `onFocus(event: InputEvent | SyntheticEvent)` a callback triggered each time the component is being focused by the user
- `onBlur(event: InputEvent | SyntheticEvent)` a callback triggered each time the component is being blured by the user

If you want to add custom components which are not input, you COULD use an arrow function or `Fragment`.

If you use an arrow function, useful props will be passed to it such as:
- `name` corresponding to the name of the actual field
- `error` corresponding to the error of the actual field
- `value` corresponding to the value of the actual field

All of previous values are based on `name` prop given to the closest `<Field>` parent.

### Interfaces

#### IOptions

```ts
interface IOptions {
  action: string
  method: 'post' | 'get'
  [option: any]?: any
}
```
#### IFields

```ts
interface IFields {
  [name: string]?: string
}
```
