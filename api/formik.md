## `<Formik />`
`<Formik />`组件使用render props来构建表单。

```jsx
import React from 'react';
import { Formik } from 'formik';

const BasicExample = () => (
  <div>
    <h1>My Form</h1>
    <Formik
      initialValues={{ name: 'jared' }}
      onSubmit={(values, actions) => {
        setTimeout(() => {
          alert(JSON.stringify(values, null, 2));
          actions.setSubmitting(false);
        }, 1000);
      }}
      render={props => (
        <form onSubmit={props.handleSubmit}>
          <input
            type="text"
            onChange={props.handleChange}
            onBlur={props.handleBlur}
            value={props.values.name}
            name="name"
          />
          {props.errors.name && <div id="feedback">{props.errors.name}</div>}
          <button type="submit">Submit</button>
        </form>
      )}
    />
  </div>
);
```

### Reference

#### Props
Formik render methods and props, 三种方式使用render,
- `<Formik component>`
- `<Formik render>`
- `<Formik children>`
三种方式的render都有相同的props

`dirty: boolean`

如果values deeply equal 等于初始值，返回true否则false。dirty属性是只读的不应该直接改变它

`errors: { [field: string]: string }`

表单校验错误结果。必须符合initialValues定义的形状。如果你使用`validationSchema` (建议使用)，keys and shapes必须和你的模式完全配对。Formik在内部会为你转化Yup错误信息。如果使用`validate`，检验方法将会决定`erorrs`对象

`handleBulr: (e: any) => void`

`onBlur`事件处理函数。当你需要追踪input`touched`状态时非常有用。用法`<input onBlur={handleBlur}>`

`handleChange: (e: React.ChangeEvent<any>) => void`

通用的input change事件处理函数。它会更新`values[key]`，key是发射事件的input标签的name属性。如果input没有声明name属性，handleChange会尝试使用id属性

`handleReset: () => void`

重置处理函数。handleReset将会把form重置为初始状态state。用法`<button onClick={handleReset}>...</button>`

`handleSubmit: (e: React.FormEvent<HTMLFormEvent>) => void`

表单提交处理函数。用法`<form onSubmit={props.handleSubmit}>...</form>`.更多关于提交信息，查看指导=》表单提交

`isSubmitting: boolean`

提交状态。当表单处于提交阶段时为true，否则false。注意：Formik将会立即把isSubmitting设为true当提交发生时。

`isValid: boolean`

当没有任何错误时为true，或者`isInitialValid `的结果是表单的初始状态(例如表单还没有dirty)

`isValidating: boolean`

当Formik在执行校验是为true。提交或者调用validateForm都会进行校验。

`resetForm: (nextValues?: Values) => void`

立即重置表单。resetForm方法会清空`touched`和`errors`对象，设置 `isSubmitting` 为false, `isValidating` 为 false。同时使用当前组件的props运行`mapPropsToValues`, 或者使用传入的参数。而后者在 componentWillReceiveProps中使用resetForm非常有用。

`setErrors: (fields: {[field: string]: string}) => void`

设置errors对象

`setFieldError: (field: string, errorMsg: string) => void` 

设置字段错误信息。`field` 参数必须和errors对象的key匹配。在构建自定义input error处理函数时非常有用。

`setfieldTouched: (field: string, isTouched?: boolean, shouldValidate?: boolean) => void`

设置字段状态为touched。在自定义input blur事件处理时非常有用。调用此方法会触发校验，如果validateOnBlur设置为true的情况下。isTouched默认是true。你也可以决定是否执行或者阻止校验通过第三个参数。

`submitForm: () => Promise`

提交。如果form校验不通过promise被reject

`submitCount: number`

试图提交表单的次数。当handleSubmit调用时增加。handleReset调用时重置为0.submitCount是一个只读属性，不应该直接被改变。

`setFieldValue: (field: string, value: any, shouldValidate?: boolean)`

设置字段的值。用在构建自定义input change处理，使用此方法会触发校验若果validateOnChange的值是true。第三个参数会执行或阻止校验发生。

`setStatus: (status: any) => void`

设置顶层的status。用于控制与表单相关的顶级状态。例如，使用它将api响应传递给组件handleSubmit

`setSubmitting: (isSubmitting: boolean) => void`

设置isSubmitting的值

`setTouched: (fields: {[field: string]: string}) => void`

Set touched imperatively

`setValues: (fields: {[field: string]: string}) => void`

Set values imperatively

`status: any`

顶层的status对象，用来代表form state，不能通过其他方式暴露、改变。用于捕获、传递api 响应给inner组件。status只能通过setStatus改变

`touched: {[field: string]: boolean}`

Touched fields.每个key对应一个字段是否被触摸/访问

`values: { [field: string]: any}`

From values. 包含mapPropsToValues（如果有）所有结果或者所有传递给组件的非function 的props

`validateForm: (values?: any) => Promise<FormikErrors<Values>>`

立即调用validate 或者validateSchema，取决于指定了那个。可选参数会被用于校验过程。如果没有则使用当前form values

`validateField: (field: string) => void`

立即执行字段的validate指定的方法（如果指定）,Formik会使用当前form values进行校验

`component?: React.ComponentType<FormikProps<Values>>`

```jsx
<Formik component={ContactForm} />;

const ContactForm = ({
  handleSubmit,
  handleChange,
  handleBlur,
  values,
  errors,
}) => (
  <form onSubmit={handleSubmit}>
    <input
      type="text"
      onChange={handleChange}
      onBlur={handleBlur}
      value={values.name}
      name="name"
    />
    {errors.name && <div>{errors.name}</div>}
    <button type="submit">Submit</button>
  </form>
};
```

Warning: `<Formik component>`完全替代 `<Formik render>`，所以二者不能同时使用

`render: (props: FormikProps<Values>) => ReactNode`

```jsx
<Formik render={props => <ContactForm {...props} />} />

<Formik
  render={({ handleSubmit, handleChange, handleBlur, values, errors }) => (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        onChange={handleChange}
        onBlur={handleBlur}
        value={values.name}
        name="name"
      />
      {errors.name &&
        <div>
          {errors.name}
        </div>}
      <button type="submit">Submit</button>
    </form>
  )}
/>
```

`children?: React.ReactNode | (props: FormikProps<Values>) => ReactNode`

```jsx
<Formik children={props => <ContactForm {...props} />} />

// or...

<Formik>
  {({ handleSubmit, handleChange, handleBlur, values, errors }) => (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        onChange={handleChange}
        onBlur={handleBlur}
        value={values.name}
        name="name"
      />
      {errors.name &&
        <div>
          {errors.name}
        </div>}
      <button type="submit">Submit</button>
    </form>
  )}
</Formik>
```

`enableReinitiablize?: boolean`

默认false，决定Formik是否能够重置为初始状态

`isInitialValid?: boolean`

默认false，控制初始isValid的值。可以传递一个方法。用于某些场景开启/禁止提交和初始时的重置button

`initialStatus: any`

表单初始状态。如果重置表单，将恢复此值。

`initialValues: Values`

表单的字段初始值。render方法提供values参数使之可以被component访问。即使表单为空，你也要初始化所有字段的值。否则react抛出异常。因为input被改为受控的表单。

注意：initialValues不可以传递给高阶组件，请使用mapPropsToValues

`onReset?: (values: Values, formikBag: FormikBag) => void`

可选的reset回调方法，参数有values和“FormikBag”

`onSubmit: (values: Values, formikBag: FormikBag) => void`

提交。FormikBag包含一个对象，这个对象拥有注入props属性和方法的子集（举例：所有的方法形如set<Thing> + resetForm）和其他传递给组件的属性。

注意：errors, touched, status和所有的事件处理不被包含在FormikBag内。

`validate?: (values: Values) => FormikErrors<Values> | Promise<any>`

注意：作者推荐使用validateSchema and Yup。尽管，validate是一个无依赖的直接验证表单的方法。

校验方法validate可以是同步方法返回`errors`对象，也可以是异步方法，返回Promise对象，promise的错误信息是一个`errors` 对象


```jsx
// Synchronous validation
const validate = values => {
  let errors = {};

  if (!values.email) {
    errors.email = 'Required';
  } else if (!/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(values.email)) {
    errors.email = 'Invalid email address';
  }

  //...

  return errors;
};
```

```jsx
// Async Validation
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms));

const validate = values => {
  return sleep(2000).then(() => {
    let errors = {};
    if (['admin', 'null', 'god'].includes(values.username)) {
      errors.username = 'Nice try';
    }
    // ...
    if (Object.keys(errors).length) {
      throw errors;
    }
  });
};
```

`validateOnBlur?: boolean`

默认是true，是否在blur事件时执行校验。blur事件：handleBlur, setFieldTouched, or setTouched are called.

`validateOnChange?: boolean`

默认是true，是否在change事件时执行校验。change事件：handleChange, setFieldValue, or setValues are called.

`validationSchema?: Schema | (() => Schema)`



