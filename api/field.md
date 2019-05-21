## `<Field />`

`<Field />`为Formik自动封装input。使用name属性匹配formik state。默认情况，`<Field />`是 html input元素

### Field render props

三种方式render `<Field />`

- <Field component>
- <Field render>
- <Field children>

除了component可以使用 string。每种render方式都提供相同的props

Field的render提供props包含一下属性和方法：
- `field`：包含`onChange`,`onBulr`,`name`和 `value`
- `form`：Formik state and helpers
- 其它传给字段的属性

### Exmaple
```jsx
import React from 'react';
import { Formik, Field } from 'formik';

const Example = () => (
  <div>
    <h1>My Form</h1>
    <Formik
      initialValues={{ email: '', color: 'red', firstName: '' }}
      onSubmit={(values, actions) => {
        setTimeout(() => {
          alert(JSON.stringify(values, null, 2));
          actions.setSubmitting(false);
        }, 1000);
      }}
      render={(props: FormikProps<Values>) => (
        <form onSubmit={props.handleSubmit}>
          <Field type="email" name="email" placeholder="Email" />
          <Field component="select" name="color">
            <option value="red">Red</option>
            <option value="green">Green</option>
            <option value="blue">Blue</option>
          </Field>
          <Field name="firstName" component={CustomInputComponent} />
          <Field
            name="lastName"
            render={({ field /* _form */ }) => (
              <input {...field} placeholder="lastName" />
            )}
          />
          <button type="submit">Submit</button>
        </form>
      )}
    />
  </div>
);

const CustomInputComponent = ({
  field, // { name, value, onChange, onBlur }
  form: { touched, errors }, // also values, setXXXX, handleXXXX, dirty, isValid, status, etc.
  ...props
}) => (
  <div>
    <input type="text" {...field} {...props} />
    {touched[field.name] &&
      errors[field.name] && <div className="error">{errors[field.name]}</div>}
  </div>
);
```

### Props

`children?: React.ReactNode | ((props: FieldProps) => React.ReactNode)`

无论是JSX元素还是回调形式，都和render方法一样。
```jsx
// Children can be JSX elements
<Field name="color" component="select" placeholder="Favorite Color">
  <option value="red">Red</option>
  <option value="green">Green</option>
  <option value="blue">Blue</option>
</Field>

// Or a callback function
<Field name="firstName">
{({ field, form }) => (
  <div>
    <input type="text" {...field} placeholder="First Name"/>
    {form.touched[field.name] &&
      form.errors[field.name] && <div className="error">{form.errors[field.name]}</div>}
  </div>
)}
</Field>
```

`component?: string | React.ComponentType<FieldProps>`

React element或者HTML element的name都可以提供个render
以下之一：

- input
- select
- textarea
- 自定义react component

自定义的component会接受和`<Field render>`的render函数一样的FieldProps加上其他直接传给`<Field>`的属性。

component默认值是input.

```jsx
// Renders an HTML <input> by default
<Field name="lastName" placeholder="Last Name"/>

// Renders an HTML <select>
<Field name="color" component="select" placeholder="Favorite Color">
  <option value="red">Red</option>
  <option value="green">Green</option>
  <option value="blue">Blue</option>
</Field>

// Renders a CustomInputComponent
<Field name="firstName" component={CustomInputComponent} placeholder="First Name"/>

const CustomInputComponent = ({
  field, // { name, value, onChange, onBlur }
  form: { touched, errors }, // also values, setXXXX, handleXXXX, dirty, isValid, status, etc.
  ...props
}) => (
  <div>
    <input type="text" {...field} {...props} />
    {touched[field.name] &&
      errors[field.name] && <div className="error">{errors[field.name]}</div>}
  </div>
);
```

`innerRef?: (el: React.HTMLElement<any> => void)`

用于在使用非自定义组件时，获取由`Field`创建的内部DOM的引用（例如调用focus方法）。

`name: string` Required

字段的name。可以通过类似lodash的风格访问`social.facebook`或者`friends[0].firstName`

`render?: (props: FieldProps) => React.ReactNode`

render方法返回一个或多个 JSX elements

```jsx
// Renders an HTML <input> and passes FieldProps field property
<Field
  name="firstName"
  render={({ field /* { name, value, onChange, onBlur } */ }) => (
    <input {...field} type="text" placeholder="firstName" />
  )}
/>

// Renders an HTML <input> and disables it while form is submitting
<Field
  name="lastName"
  render={({ field, form: { isSubmitting } }) => (
    <input {...field} disabled={isSubmitting} type="text" placeholder="lastName" />
  )}
/>

// Renders an HTML <input> with custom error <div> element
<Field
  name="lastName"
  render={({ field, form: { touched, errors } }) => (
    <div>
      <input {...field} type="text" placeholder="lastName" />
      {touched[field.name] &&
        errors[field.name] && <div className="error">{errors[field.name]}</div>}
    </div>
  )}
/>
```

`validate:? (value: any) => undefined | string | Promise<any>`

可以通过传入validate prop运行字段级别独立校验方法。validate会遵守Field父级 `<Formik>`/`withFormik`的 validateOnBlur 和validateOnChange。validate可以是同步、异步方法

- Sync：如果无效，validate方法返回string类型错误信息，否则return undefined
- Async：validate方法返回一个promise，promise抛出错误信息，如果字段校验不通过。类似于 Formik的validate，不同的地方是Formik抛出的异常是errors 对象而不是字符串。

```jsx
import React from 'react';
import { Formik, Form, Field } from 'formik';

// Synchronous validation function
const validate = value => {
  let errorMessage;
  if (!/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(value)) {
    errorMessage = 'Invalid email address';
  }
  return errorMessage;
};

// Async validation function
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms));

const validateAsync = value => {
  return sleep(2000).then(() => {
    if (['admin', 'null', 'god'].includes(value)) {
      throw 'Nice try';
    }
  });
};

// example usage
const MyForm = () => (
  <Formik
    initialValues={{ email: '', username: '' }}
    onSubmit={values => alert(JSON.stringify(values, null, 2))}
  >
    {({ errors, touched }) => (
      <Form>
        <Field validate={validate} name="email" type="email" />
        {errors.email && touched.email ? <div>{errors.email}</div> : null}
        <Field validate={validateAsync} name="username" />
        {errors.username && touched.username ? (
          <div>{errors.username}</div>
        ) : null}
        <button type="submit">Submit</button>
      </Form>
    )}
  </Formik>
);
```

注意：要支持语言国际化，TypeScript类型验证要宽松要求，允许validate返回 Function.(例如：i18n('invalid'))
