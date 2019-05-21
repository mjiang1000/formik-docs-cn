## connect()

`connect()` 是一个高阶组件可以将一些钩子添加到Formik的context。用于构建`<Field />` 和`<Form>`,也可以用来构建任何你需要的组件

### 类型签名

```js
connect<OuterProps, Values = any>(Comp: React.ComponentType<OuterProps & FormikProps<Values>>) => React.ComponentType<OuterProps>
```
### Example

```jsx
import React from 'react';
import { connect, getIn } from 'formik';

// This component renders an error message if a field has
// an error and it's already been touched.
const ErrorMessage = props => {
  // All FormikProps available on props.formik!
  const error = getIn(props.formik.errors, props.name);
  const touch = getIn(props.formik.touched, props.name);
  return touch && error ? error : null;
};

export default connect(ErrorMessage);
```