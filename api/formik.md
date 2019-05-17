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


