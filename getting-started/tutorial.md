## 通过构建formik来学习

作者在React Alicante的演讲是最好的学习资源，其中简单介绍了formik，和一些示例表单（包含array，个性化输入字段等），视屏时长45分钟。地址：https://youtu.be/oiNtnehlaTo

## 基本用法

这是一个快速通过代码了解基本用法的示例

假设你需要创建一个可以编辑用户信息的表单，你的api提供有如下结构的数据

```javascript
{
   id: string,
   email: string,
   social: {
     facebook: string,
     twitter: string,
     // ...
   }
}
```

```javascript
// EditUserDialog.js
import React from 'react';
import Dialog from 'MyImaginaryDialogComponent'; // this isn't a real package, just imagine it exists.
import { Formik } from 'formik';

const EditUserDialog = ({ user, updateUser, onClose }) => {
  return (
    <Dialog onClose={onClose}>
      <h1>Edit User</h1>
      <Formik
        initialValues={user /** { email, social } */}
        onSubmit={(values, actions) => {
          MyImaginaryRestApiCall(user.id, values).then(
            updatedUser => {
              actions.setSubmitting(false);
              updateUser(updatedUser);
              onClose();
            },
            error => {
              actions.setSubmitting(false);
              actions.setErrors(transformMyRestApiErrorsToAnObject(error));
              actions.setStatus({ msg: 'Set some arbitrary status or data' });
            }
          );
        }}
        render={({
          values,
          errors,
          status,
          touched,
          handleBlur,
          handleChange,
          handleSubmit,
          isSubmitting,
        }) => (
          <form onSubmit={handleSubmit}>
            <input
              type="email"
              name="email"
              onChange={handleChange}
              onBlur={handleBlur}
              value={values.email}
            />
            {errors.email && touched.email && <div>{errors.email}</div>}
            <input
              type="text"
              name="social.facebook"
              onChange={handleChange}
              onBlur={handleBlur}
              value={values.social.facebook}
            />
            {errors.social &&
              errors.social.facebook &&
              touched.facebook && <div>{errors.social.facebook}</div>}
            <input
              type="text"
              name="social.twitter"
              onChange={handleChange}
              onBlur={handleBlur}
              value={values.social.twitter}
            />
            {errors.social &&
              errors.social.twitter &&
              touched.twitter && <div>{errors.social.twitter}</div>}
            {status && status.msg && <div>{status.msg}</div>}
            <button type="submit" disabled={isSubmitting}>
              Submit
            </button>
          </form>
        )}
      />
    </Dialog>
  );
};
```

Formik 提供了一些helpers来减轻代码书写工作
* `<Field />`
* `<Form />`
使用 `<Field />`和 `<Form />`实现上面的form，代码如下

```javascript
// EditUserDialog.js
import React from 'react';
import Dialog from 'MySuperDialog';
import { Formik, Field, Form } from 'formik';

const EditUserDialog = ({ user, updateUser, onClose }) => {
  return (
    <Dialog onClose={onClose}>
      <h1>Edit User</h1>
      <Formik
        initialValues={user /** { email, social } */}
        onSubmit={(values, actions) => {
          MyImaginaryRestApiCall(user.id, values).then(
            updatedUser => {
              actions.setSubmitting(false);
              updateUser(updatedUser);
              onClose();
            },
            error => {
              actions.setSubmitting(false);
              actions.setErrors(transformMyRestApiErrorsToAnObject(error));
              actions.setStatus({ msg: 'Set some arbitrary status or data' });
            }
          );
        }}
        render={({ errors, status, touched, isSubmitting }) => (
          <Form>
            <Field type="email" name="email" />
            {errors.email && touched.email && <div>{errors.email}</div>}
            <Field type="text" name="social.facebook" />
            {errors.social && errors.social.facebook &&
              touched.social.facebook && <div>{errors.social.facebook}</div>}
            <Field type="text" name="social.twitter" />
            {errors.social && errors.social.twitter &&
              touched.social.twitter && <div>{errors.social.twitter}</div>}
            {status && status.msg && <div>{status.msg}</div>}
            <button type="submit" disabled={isSubmitting}>
              Submit
            </button>
          </Form>
        )}
      />
    </Dialog>
  );
};
```

这样好多了，但是错误信息`errors`和`touched`这一部分逻辑仍然有重复。Formik提供了一个组件`<ErrorMessage/>`可以让事情更简单。`<ErrorMessage/>`可以灵活选择接受 render props或者是 component props

```javascript
// EditUserDialog.js
import React from 'react';
import Dialog from 'MySuperDialog';
import { Formik, Field, Form, ErrorMessage } from 'formik';

const EditUserDialog = ({ user, updateUser, onClose }) => {
  return (
    <Dialog onClose={onClose}>
      <h1>Edit User</h1>
      <Formik
        initialValues={user /** { email, social } */}
        onSubmit={(values, actions) => {
          MyImaginaryRestApiCall(user.id, values).then(
            updatedUser => {
              actions.setSubmitting(false);
              updateUser(updatedUser);
              onClose();
            },
            error => {
              actions.setSubmitting(false);
              actions.setErrors(transformMyRestApiErrorsToAnObject(error));
              actions.setStatus({ msg: 'Set some arbitrary status or data' });
            }
          );
        }}
        render={({ errors, status, touched, isSubmitting }) => (
          <Form>
            <Field type="email" name="email" />
            <ErrorMessage name="email" component="div" />  
            <Field type="text" className="error" name="social.facebook" />
            <ErrorMessage name="social.facebook">
              {errorMessage => <div className="error">{errorMessage}</div>}
            </ErrorMessage>
            <Field type="text" name="social.twitter" />
            <ErrorMessage name="social.twitter" className="error" component="div"/>  
            {status && status.msg && <div>{status.msg}</div>}
            <button type="submit" disabled={isSubmitting}>
              Submit
            </button>
          </Form>
        )}
      />
    </Dialog>
  );
};
```