## <FastField>

### 前言

`<FastField>`用于优化性能。你完全不需要使用它，直到你要优化性能之前，前提是你非常熟悉react shouldComponentUdpate是怎么工作的。

在就行进行之前，请阅读以下react官方介绍

- [React shouldComponentUpdate() Reference](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)
- [shouldComponentUpdate in Action](https://reactjs.org/docs/optimizing-performance.html#shouldcomponentupdate-in-action)

### FastField 概述

`<FastField />`是`<Field>`优化版本，用于大型表单(30+字段)，或者字段的校验逻辑性能开销很大。`<FastField />`与`<Field>`有相同的api，但是内部实现了`shouldComponentUpdate()`阻止所有的re-render，除非Formik state与字段相关的部分有更新。

例如：`<FastField name="firstName" />`在以下情况重新渲染

- `values.firstName`, `errors.firstName`, `touched.firstName`或者`isSubmitting`发生变化。判断变化是 shallow comparison

- 一个属性添加/移除给`<FastField name="firstName" />`

- name 属性变化

除了上述情况，Formik state其它部分发生变化不会触发FastField重新渲染。但是有FastField的更新会触发其它Field字段重新渲染。

### 什么时候使用<FastField>

如果某个字段是独立于表单内其它字段，可以使用

更具体地说，如果一个字段更新和渲染不依赖与其它字段，也不依赖Formik state的其它部分，就可以使用FastField 替代Field

### Example

```jsx
import React from 'react';
import { Formik, Field, FastField, Form } from 'formik';

const Basic = () => (
  <div>
    <h1>Sign Up</h1>
    <Formik
      initialValues={{
        firstName: '',
        lastName: '',
        email: '',
      }}
      validationSchema={Yup.object().shape({
        firstName: Yup.string().required(),
        middleInitial: Yup.string(),
        lastName: Yup.string().required(),
        email: Yup.string()
          .email()
          .required(),
      })}
      onSubmit={values => {
        setTimeout(() => {
          alert(JSON.stringify(values, null, 2));
        }, 500);
      }}
      render={formikProps => (
        <Form>
          {/** This <FastField> only updates for changes made to
           values.firstName, touched.firstName, errors.firstName */}
          <label htmlFor="firstName">First Name</label>
          <FastField name="firstName" placeholder="Weezy" />

          {/** Updates for all changes because it's from the
           top-level formikProps which get all updates */}
          {form.touched.firstName &&
            form.errors.firstName && <div>{form.errors.firstName}</div>}

          <label htmlFor="middleInitial">Middle Initial</label>
          <FastField
            name="middleInitial"
            placeholder="F"
            render={({ field, form }) => (
              <div>
                <input {...field} />
                {/**
                 * This updates normally because it's from the same slice of Formik state,
                 * i.e. path to the object matches the name of this <FastField />
                 */}
                {form.touched.middleInitial ? form.errors.middleInitial : null}

                {/** This won't ever update since it's coming from
                 from another <Field>/<FastField>'s (i.e. firstName's) slice   */}
                {form.touched.firstName && form.errors.firstName
                  ? form.errors.firstName
                  : null}

                {/* This doesn't update either */}
                {form.submitCount}

                {/* Imperative methods still work as expected */}
                <button
                  type="button"
                  onClick={form.setFieldValue('middleInitial', 'J')}
                >
                  J
                </button>
              </div>
            )}
          />

          {/** Updates for all changes to Formik state
           and all changes by all <Field>s and <FastField>s */}
          <label htmlFor="lastName">LastName</label>
          <Field
            name="lastName"
            placeholder="Baby"
            render={({ field, form }) => (
              <div>
                <input {...field} />
                {/** Works because this is inside
                 of a <Field/>, which gets all updates */}
                {form.touched.firstName && form.errors.firstName
                  ? form.errors.firstName
                  : null}
              </div>
            )}
          />

          {/** Updates for all changes to Formik state and
           all changes by all <Field>s and <FastField>s */}
          <label htmlFor="email">Email</label>
          <Field name="email" placeholder="jane@acme.com" type="email" />

          <button type="submit">Submit</button>
        </Form>
      )}
    />
  </div>
);
```