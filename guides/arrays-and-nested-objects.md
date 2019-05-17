## 数组和嵌套对象

Formik提供了对nested objects 和 arrays 支持，这两种类型使用相同的语法

### Nested Objects
Formik中的name属性可以使用类似lodash通过"."操作符访问nested objects的值。这意味着不需要展开Formik values

```javascript
import React from 'react';
import { Formik, Form, Field } from 'formik';

export const NestedExample = () => (
  <div>
    <h1>Social Profiles</h1>
    <Formik
      initialValues={{
        social: {
          facebook: '',
          twitter: '',
        },
      }}
      onSubmit={values => {
        // same shape as initial values
        console.log(values);
      }}
    >
      <Field name="social.facebook" />
      <Field name="social.twitter" />
      <button type="submit">Submit</button>
    </Formik>
  </div>
);
```

### Arrays
Formik提供了类似lodash通过“[]”语法

```jsx
import React from 'react';
import { Formik, Form, Field } from 'formik';

export const BasicArrayExample = () => (
  <div>
    <h1>Friends</h1>
    <Formik
      initialValues={{
        friends: ['jared', 'ian'],
      }}
      onSubmit={values => {
        // same shape as initial values
        console.log(values);
      }}
    >
      <Field name="friends[0]" />
      <Field name="friends[1]" />
      <button type="submit">Submit</button>
    </Formik>
  </div>
);
```

更多关于items in list信息，查看api 参考`<FieldArray>`部分

