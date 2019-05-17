## 校验
Formik旨在轻松管理具有复杂验证的表单，支持同步、异步表单级和字段级验证。此外，它还能通过Yup为基于模式的表单级验证提供支持。本指南将描述上述所有内容的细节。

#### 表单级别验证
表单级验证很有用，你可以随时访问表单的values和props，因此您可以同时验证相关字段。

Formik提供两种方式处理form-level验证

- `<Formik validate>` 和 `withFormik({ validate: ... })`
- `<Formik validationSchema>` 和 `withFormik({ validationSchema: ... })`


`validate`

`<Formik>`和`withForm()` 接受名为validate的prop，validate是一个同步或者一步的函数

```javascript
// Synchronous validation
const validate = (values, props /* only available when using withFormik */) => {
  let errors = {};

  if (!values.email) {
    errors.email = 'Required';
  } else if (!/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(values.email)) {
    errors.email = 'Invalid email address';
  }

  //...

  return errors;
};

// Async Validation
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms));

const validate = (values, props /* only available when using withFormik */) => {
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

更多关于`<Formik validate>`请参考api

`validationSchema` 基于协议的校验

如您所见，验证逻辑由您决定。您可以随意编写自己的验证器或使用第三方库。在我们内部，使用Yup。yup的api类似于React PropTypes
而且足够轻量级能够在浏览器快速运行。

### 字段级别验证

Formik通过`<Field>/<FastField>`组件接受validate属性来支持字段级别的验证功能。validate是一个返回 promise对象的同步或者一步的方法。
默认情况下，它将在任何onChange和onBlur触发之后运行。这个默认行为可以通过`validateOnChange`和 `validateOnBlur`改变。除了change/blur，所有的字段级别的校验会在表单提交前执行，校验结果会合并到顶层的校验结果中。

    注意：`<Field>/<FastField>` 组件的validate方法只在已经mouted状态下执行。也就是说，如果表单中任何unmount状态（例如使用 Material-UI，`<Tabs>` unmounts前一个tab）的字段在表单校验/提交过程中不会被校验

```javascript
import React from 'react';
import { Formik, Form, Field } from 'formik';
import * as Yup from 'yup';

function validateEmail(value) {
  let error;
  if (!value) {
    error = 'Required';
  } else if (!/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(value)) {
    error = 'Invalid email address';
  }
  return error;
}

function validateUsername(value) {
  let error;
  if (value === 'admin') {
    error = 'Nice try!';
  }
  return error;
}

export const FieldLevelValidationExample = () => (
  <div>
    <h1>Signup</h1>
    <Formik
      initialValues={{
        username: '',
        email: '',
      }}
      onSubmit={values => {
        // same shape as initial values
        console.log(values);
      }}
    >
      {({ errors, touched, isValidating }) => (
        <Form>
          <Field name="email" validate={validateEmail} />
          {errors.email && touched.email && <div>{errors.email}</div>}

          <Field name="username" validate={validateUsername} />
          {errors.username && touched.username && <div>{errors.username}</div>}

          <button type="submit">Submit</button>
        </Form>
      )}
    </Formik>
  </div>
);
```

### 手动触发校验
你可使用validateForm 和validateField 方法手动触发表单级别、字段级别的校验

```javascript
import React from 'react';
import { Formik, Form, Field } from 'formik';

function validateEmail(value) {
  let error;
  if (!value) {
    error = 'Required';
  } else if (!/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(value)) {
    error = 'Invalid email address';
  }
  return error;
}

function validateUsername(value) {
  let error;
  if (value === 'admin') {
    error = 'Nice try!';
  }
  return error;
}

export const FieldLevelValidationExample = () => (
  <div>
    <h1>Signup</h1>
    <Formik
      initialValues={{
        username: '',
        email: '',
      }}
      onSubmit={values => {
        // same shape as initial values
        console.log(values);
      }}
    >
      {({ errors, touched, validateField, validateForm }) => (
        <Form>
          <Field name="email" validate={validateEmail} />
          {errors.email && touched.email && <div>{errors.email}</div>}

          <Field name="username" validate={validateUsername} />
          {errors.username && touched.username && <div>{errors.username}</div>}
          {/** Trigger field-level validation
           imperatively */}
          <button type="button" onClick={() => validateField('username')}>
            Check Username
          </button>
          {/** Trigger form-level validation
           imperatively */}
          <button type="button" onClick={() => validateForm().then(() => console.log('blah')))}>
            Validate All
          </button>
          <button type="submit">Submit</button>
        </Form>
      )}
    </Formik>
  </div>
);
```

## 什么时候校验方法执行
你可以通过`<Formik validateOnChange>` 和 `<Formik validateOnBlur>`props按照你的需求控制Formik是否执行校验方法当表单的values发生变化。
通常Formik会按照如下执行校验方法

#### 在change 事件/方法之后(更新values)
- handleChange
- setFieldValue
- setValues

#### 在blur 事件/方法之后(更新touched)

- handleBlur
- setTouched
- setFieldTouched

#### 当表单提交时（Whenever submission is attempted）

- handleSubmit
- submitForm

此外还有用过props的提供的validateForm、validateField

## FAQ(Frequently Asked Questions)

怎么判断form正在校验？
`isValidating` prop is `true`

能否返回 `null` 作为错误信息?
不能，应该使用 `undefined`。Formik认为`undefined`校验返回值是通过验证。如果使用`null`，有些属性（如`isValid`）不会按照预期的那样

怎样测试校验方法？
Formik使用Yup进行了大量的单元测试所以你无需验证。如果你要测试你自己写的validate 方法，你需要对它们进行单元测试。

