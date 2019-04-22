# 概述

众所周知，react中使用表单是一件很麻烦的事情。大多数工具使用了太多太多黑魔法或者是有损性能的方式来处理react表单。Formik是一个小而美的library帮你处理好3件事情，让你远离react表单的痛苦。

1. 获取表单中（所有）字段的值
2. 验证表单和处理错误信息
3. 处理表单submit事件

Formik将上述内容组织在一起有序处理，使得表单的测试、重构、代码理解都变得容易起来

## 为什么要创造Formik

我([@jaredpalmer](https://twitter.com/jaredpalmer)) 和 ([@eonwhite](https://twitter.com/eonwhite))在开发一个大型内部的管理系统的时候写了formik，来帮我们处理大概30个独立的表单。很快我们发现，formik带来的不仅仅是标准化的input组件，而是标准化的表单中数据流动(data flows in form state)

### 为什么不是redux-from

到目前为止，你可能在思考,“为什么不用[Redux-Form](https://github.com/erikras/redux-form)?”

1. 根据大神[Dan Abramov](https://github.com/gaearon)的说法,

  [form state is inherently ephemeral and local, so tracking it in Redux (or any kind of Flux library) is unnecessary](https://github.com/reactjs/redux/issues/1287#issuecomment-175351978)
  表单的状态本质上是短暂的、局部的，所以不需要使用Redux或者其他类Flux工具来追踪、存储。
2. 当键盘输入时，每一次按下键盘都，Redux-form都会多次调用顶层的Redux reducer。这种方式在小型app上没有问题，但是当你app的功能逐渐增加，潜在的input也在继续增加、最终这种Redux reducer调用方式可能回导致性能问题
3. Formik的体积更小。Redux-Form is 22.5 kB minified gzipped (Formik is 12.7 kB)

Formik的目标是，一个小而美、可扩展的高效的表单工具帮助你解决那些烦人的事情（验证、处理错误信息、提交），让你只需要关心逻辑

### 作者的演讲
[talk at React Alicante](https://youtu.be/oiNtnehlaTo)

## 灵感来源
Formik 开始于扩展hoc，吸取了Redux-Form、最近流行的React-Motion and React-Router 4的优点。不管你有没有了解以上的框架，只需要花几分钟就可以快速上手formik

## 安装
可以通过npm, yarn来安装，或者通过 script标签引入

### NPM
```sh
npm install formik --save
```
or
```
yarn add formik
```

Formik 稳定运行于React v15+、ReactDom、React-native
使用前行体验
**[demo of Formik on CodeSandbox.io](https://codesandbox.io/s/zKrK5YLDZ)**

### CDN

```html
<script src="https://unpkg.com/formik/dist/formik.umd.production.js"></script>
```

## 要领
Formik追踪表单的状态并且通过props，暴露给Form，并且提供一些可复用的方法和事件处理函数(handleChange, handleBlur, handleSubmit)。handleChange和handleBlur方法根据通过name或者id属性来更新字段的状态

```javascript
import React from 'react';
import { Formik } from 'formik';

const values = { email: '', password: '' }
const Basic = () => (
    <div>
        <h1>Anywhere in your app!</h1>
        <Formik
            initialValues={values}
            validate={values => {
                let errors = {};
                if (!values.email) {
                    errors.email = 'Required';
                } else if (
                    !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(values.email)
        ) {
            errors.email = 'Invalid email address';
        }
        return errors;
      }}
      onSubmit={(values, { setSubmitting }) => {
            setTimeout(() => {
                alert(JSON.stringify(values, null, 2));
                setSubmitting(false);
            }, 400);
        }}
        >
      {({
            values,
            errors,
            touched,
            handleChange,
            handleBlur,
            handleSubmit,
            isSubmitting,
            /* and other goodies */
        }) => (
                <form onSubmit={handleSubmit}>
                    <input
                        type="email"
                        name="email"
                        onChange={handleChange}
                        onBlur={handleBlur}
                        value={values.email}
                    />
                    {errors.email && touched.email && errors.email}
                    <input
                        type="password"
                        name="password"
                        onChange={handleChange}
                        onBlur={handleBlur}
                        value={values.password}
                    />
                    {errors.password && touched.password && errors.password}
                    <button type="submit" disabled={isSubmitting}>
                        Submit
          </button>
                </form>
            )}
    </Formik>
  </div >
);

export default Basic;
```

### Reducing 解析
以上代码演示了Formik做了什么。`onChange` -> `handleChange`, `onBlur` -> `handleBlur`等等。Formik提供了额外的组件来节省时间和代码：`<Form />`,`<Field />`,`<ErrorMessage />`. 这些组件利用react context获取钩子来访问父级 `<Formik />` 状态/方法
```javascript
// Render Prop
import React from 'react';
import { Formik, Form, Field, ErrorMessage } from 'formik';

const values = { email: '', password: '' }
const Basic = () => (
  <div>
    <h1>Any place in your app!</h1>
    <Formik
      initialValues={values}
      validate={values => {
        let errors = {};
        if (!values.email) {
          errors.email = 'Required';
        } else if (
          !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(values.email)
        ) {
          errors.email = 'Invalid email address';
        }
        return errors;
      }}
      onSubmit={(values, { setSubmitting }) => {
        setTimeout(() => {
          alert(JSON.stringify(values, null, 2));
          setSubmitting(false);
        }, 400);
      }}
    >
      {({ isSubmitting }) => (
        <Form>
          <Field type="email" name="email" />
          <ErrorMessage name="email" component="div" />
          <Field type="password" name="password" />
          <ErrorMessage name="password" component="div" />
          <button type="submit" disabled={isSubmitting}>
            Submit
          </button>
        </Form>
      )}
    </Formik>
  </div>
);

export default Basic;
```
### 补充npm包
以上你可以看到，表单的校验全凭自己。你可以使用第三方工具或是自己写校验过程。我个人使用的是[Yup](https://github.com/jquense/yup)。