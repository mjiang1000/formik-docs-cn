## <ErrorMessage />

`<ErrorMessage />` 是用来显示某个被访问过（`touched[name] === true`）的字段错误信息的组件。并且ErrorMessage期望所有字段例如<Field/ >, <FastField />, <FieldArray>的错误信息都以字符串类型存储，支持类似lodash风格的语法。

### Example

```jsx
import React from 'react';
import { Formik, Form, Field, ErrorMessage } from 'formik';
import * as Yup from "yup";

const SignupSchema = Yup.object().shape({
  name: Yup.string()
    .min(2, 'Too Short!')
    .max(70, 'Too Long!')
    .required('Required'),
  email: Yup.string()
    .email('Invalid email')
    .required('Required'),
});

export const ValidationSchemaExample = () => (
  <div>
    <h1>Signup</h1>
    <Formik
      initialValues={{
        name: '',
        email: '',
      }}
      validationSchema={SignupSchema}
      onSubmit={values => {
        // same shape as initial values
        console.log(values);
      }}
    >
      {({ errors, touched }) => (
        <Form>
          <Field name="name"  />
-           {errors.name && touched.name ? (
-            <div>{errors.name}</div>
-          ) : null}
+         <ErrorMessage name="name" />
          <Field name="email" type="email" />
-           {errors.email && touched.email ? (
-            <div>{errors.email}</div>
-          ) : null}
+         <ErrorMessage name="email" />
          <button type="submit">Submit</button>
        </Form>
      )}
    </Formik>
  </div>
);
```

### Reference

`children?: (message? string) => React.ReactNode`

一个只在字段被访问过且存在错误信息时被调用的方法，返回值是有效的react节点

```jsx
// the render callback will only be called when the
// field has been touched and an error exists and subsequent updates.
<ErrorMessage name="email">{msg => <div>{msg}</div>}</ErrorMessage>
```

`component?: string | React.ComponentType<FieldProps>`

用来包裹错误信息。React 组件和HTML元素都可以。没有指定，直接渲染字符串。

```jsx
<ErrorMessage component="div" name="email" />
// --> {touched.email && error.email ? <div>{error.email}</div> : null}

<ErrorMessage component="span" name="email" />
// --> {touched.email && error.email ? <span>{error.email}</span> : null}

<ErrorMessage component={Custom} name="email" />
// --> {touched.email && error.email ? <Custom>{error.email}</Custom> : null}

<ErrorMessage name="email" />
// This will return a string. React 16+.
// --> {touched.email && error.email ? error.email : null}
```

`name: string` Required

A field's name in Formik state

`render?: (error: string) => React.ReactNode`

只在字段被访问过且存在错误信息时被调用

```jsx
// the render callback will only be called when the
// field has been touched and an error exists and subsequent updates.
<ErrorMessage name="email" render={msg => <div>{msg}</div>} />
```