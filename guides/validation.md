## 校验
Formik旨在轻松管理具有复杂验证的表单，支持同步、异步表单级和字段级验证。此外，它还能通过Yup为基于模式的表单级验证提供支持。本指南将描述上述所有内容的细节。

#### 表单级别验证
表单级验证很有用，你可以随时访问表单的values和props，因此您可以同时验证相关字段。

Formik提供两种方式处理form-level验证

- `<Formik validate>` 和 `withFormik({ validate: ... })`
- `<Formik validationSchema>` 和 `withFormik({ validationSchema: ... })`


`validate`

`<Formik>`和`withForm()` 接受名为validate的prop，validate是一个同步或者一步的函数

```
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
默认情况下，它将在任何onChange和onBlur触发之后运行。
