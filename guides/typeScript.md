## TypeScript

Formik源码是用ts写的，所以你可以放心 Fromik types总是最新的。并且Formik types签名类似于React Router 4

#### Render props （`<Formik />` and  `<Field />`）

```ts
import * as React from 'react';
import { Formik, FormikActions, FormikProps, Form, Field, FieldProps } from 'formik';

interface MyFormValues {
  firstName: string;
}

export const MyApp: React.SFC<{}> = () => {
  return (
    <div>
      <h1>My Example</h1>
      <Formik
        initialValues={{ firstName: '' }}
        onSubmit={(values: MyFormValues, actions: FormikActions<MyFormValues>) => {
            console.log({ values, actions });
            alert(JSON.stringify(values, null, 2));
            actions.setSubmitting(false)
         }}
        render={(formikBag: FormikProps<MyFormValues>) => (
          <Form>
            <Field
              name="firstName"
              render={({ field, form }: FieldProps<MyFormValues>) => (
                <div>
                  <input type="text" {...field} placeholder="First Name" />
                  {form.touched.firstName &&
                    form.errors.firstName &&
                    form.errors.firstName}
                </div>
              )}
            />
          </Form>
        )}
      />
    </div>
  );
};
```