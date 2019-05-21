## withFormik()

构建高阶组件。通过withFormik方法传递props和handles给自定义组件。

### Example

```jsx
import React from 'react';
import { withFormik } from 'formik';

const MyForm = props => {
  const {
    values,
    touched,
    errors,
    handleChange,
    handleBlur,
    handleSubmit,
  } = props;
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        onChange={handleChange}
        onBlur={handleBlur}
        value={values.name}
        name="name"
      />
      {errors.name && touched.name && <div id="feedback">{errors.name}</div>}
      <button type="submit">Submit</button>
    </form>
  );
};

const MyEnhancedForm = withFormik({
  mapPropsToValues: () => ({ name: '' }),

  // Custom sync validation
  validate: values => {
    const errors = {};

    if (!values.name) {
      errors.name = 'Required';
    }

    return errors;
  },

  handleSubmit: (values, { setSubmitting }) => {
    setTimeout(() => {
      alert(JSON.stringify(values, null, 2));
      setSubmitting(false);
    }, 1000);
  },

  displayName: 'BasicForm',
})(MyForm);
```

`options`

`displayName?: string`

当你的inner component是一个无状态的组件，可以使用displayName给组件一个合适的名字。这样在 React DevTools就能方便的找到这个组件withFormik(displayName)。在使用class component时不需要displayName

`enableReInitilize?: boolean`

默认是false，决定form是否reset当包裹组件的props变化时

`handleSubmit: (values: Valuse, formikBag: FormikBag) => void`

"FormikBage": 
- `props` (传给包裹组件的props)
- `resetForm`
- `setErrors`
- `setFieldError`
- `setFieldTouched`
- `setFieldValue`
- `setStatus`
- `setSubmitting`
- `setTouched`
- `setValues`

Note: errors, touched, status and all event handlers are NOT included in the FormikBag

`isInitialValid:? boolean | (props: Props) => boolean`
同formik

`mapPorpsToValues?: (values: Values) => Values`

如果指定，Formik将使用转换的结果来更新表单状态，并且通过props.values供给新的组件访问。否则，所有的非函数props都会提供给inner component。

`mapPropsToStatus?: (props: Props) => any`

如果指定，Formik将使用转换的结果来更新表单状态，并且通过props.status供给新的组件访问。用于在form中存储状态。提醒一下：status会被重置初始值当form reset。

`validate: (values: Values, props: Props) => FormikErrors<Values> | Promise<any>` 
同formik


`validateOnBlur?: boolean`

同formik

`validateOnChange?: boolean`

同formik

`validationSchema?: Schema | ((props: Props) => Schema)`

同formik

### 注入props和方法
和props `<Formik render={props => ...} />`一样



