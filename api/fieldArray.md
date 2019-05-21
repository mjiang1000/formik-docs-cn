## <FieldArray />

`<FieldArray />`是一个用于方便操作 array/list的组件。<FieldArray />的name属性是values的key，`values[key]`是一组数据。通过render props可以访问array帮助函数，这些方法方便验证和管理`touched`

```jsx
import React from 'react';
import { Formik, Form, Field, FieldArray } from 'formik';

// Here is an example of a form with an editable list.
// Next to each input are buttons for insert and remove.
// If the list is empty, there is a button to add an item.
export const FriendList = () => (
  <div>
    <h1>Friend List</h1>
    <Formik
      initialValues={{ friends: ['jared', 'ian', 'brent'] }}
      onSubmit={values =>
        setTimeout(() => {
          alert(JSON.stringify(values, null, 2));
        }, 500)
      }
      render={({ values }) => (
        <Form>
          <FieldArray
            name="friends"
            render={arrayHelpers => (
              <div>
                {values.friends && values.friends.length > 0 ? (
                  values.friends.map((friend, index) => (
                    <div key={index}>
                      <Field name={`friends.${index}`} />
                      <button
                        type="button"
                        onClick={() => arrayHelpers.remove(index)} // remove a friend from the list
                      >
                        -
                      </button>
                      <button
                        type="button"
                        onClick={() => arrayHelpers.insert(index, '')} // insert an empty string at a position
                      >
                        +
                      </button>
                    </div>
                  ))
                ) : (
                  <button type="button" onClick={() => arrayHelpers.push('')}>
                    {/* show this when user has removed all friends from the list */}
                    Add a friend
                  </button>
                )}
                <div>
                  <button type="submit">Submit</button>
                </div>
              </div>
            )}
          />
        </Form>
      )}
    />
  </div>
);
```

`name: string`

对应values的key或者路径。

`validateOnChange?: boolean`

默认是true。决定每次array操作之后是否运行校验。

### FieldArray Array Of Objects

可以通过`object[index]property` or `object.index.property` 赋值给 <Field /> or <FieldArray />的input元素的name属性

```jsx
<Form>
  <FieldArray
    name="friends"
    render={arrayHelpers => (
      <div>
        {values.friends.map((friend, index) => (
          <div key={index}>
            <Field name={`friends[${index}].name`} />
            <Field name={`friends.${index}.age`} /> // both these conventions do the same
            <button type="button" onClick={() => arrayHelpers.remove(index)}>
              -
            </button>
          </div>
        ))}
        <button
          type="button"
          onClick={() => arrayHelpers.push({ name: '', age: '' })}
        >
          +
        </button>
      </div>
    )}
  />
</Form>
```

### FieldArray Validation陷阱

FieldArray验证有点麻烦

如果使用validationSchema方式校验，展示错误信息有点棘手。例如：

```js
const schema = Yup.object().shape({
  friends: Yup.array()
    .of(
      Yup.object().shape({
        name: Yup.string()
          .min(4, 'too short')
          .required('Required'), // these constraints take precedence
        salary: Yup.string()
          .min(3, 'cmon')
          .required('Required'), // these constraints take precedence
      })
    )
    .required('Must have friends') // these constraints are shown if and only if inner constraints are satisfied
    .min(3, 'Minimum of 3 friends'),
});
```

因为Yup和自定义组件的validate方法都应该输出字符串作为错误信息。在展示之前，必须检查嵌套的错误信息的类型是array还是string

Bad

```jsx
// within a `FieldArray`'s render
const FriendArrayErrors = errors =>
  errors.friends ? <div>{errors.friends}</div> : null; // app will crash
```

Good

```jsx
// within a `FieldArray`'s render
const FriendArrayErrors = errors =>
  typeof errors.friends === 'string' ? <div>{errors.friends}</div> : null;
```

对于嵌套的field errors,你应该假设它没有定义任何部分，所以你要检查。你可以创建自定义的错误展示组件，例如：

```jsx
import { Field, getIn } from 'formik';

const ErrorMessage = ({ name }) => (
  <Field
    name={name}
    render={({ form }) => {
      const error = getIn(form.errors, name);
      const touch = getIn(form.touched, name);
      return touch && error ? error : null;
    }}
  />
);

// Usage
<ErrorMessage name="friends[0].name" />; // => null, 'too short', or 'required'
```

注意：从v0.12 / 1.0版本，新的meta属性添加到 Field和FieldArray。meta给出error & touch相关信息。不用再手动检查path和getIn。

### FieldArray Helpers

以下方法通过render props可以访

- `push: (obj: any) => void` 
- `swap: (indexA: number, indexB: number) => void` 交换
- `move: (from: bumber, to: number) => void` 移动
- `insert: (index: number, value: any) => void`
- `unshilft: (value) => number` 头部加入一个元素，返回新Array的长度
- `remove<T>(index: number): T | undefined`
- `pop<T>(): T | undefined`
- `replace: (index: number, value: any) => void`

### FieldArray render methods

三种方式render

- `<FieldArray name="..." component>`
- `<FieldArray name="..." render>`
- `<FieldArray name="..." children>`

`render: (arrayHelpers: ArrayHelpers) => React.ReactNode`

```jsx
import React from 'react';
import { Formik, Form, Field, FieldArray } from 'formik'

export const FriendList = () => (
  <div>
    <h1>Friend List</h1>
    <Formik
      initialValues={{ friends: ['jared', 'ian', 'brent'] }}
      onSubmit={...}
      render={formikProps => (
        <FieldArray
          name="friends"
          render={({ move, swap, push, insert, unshift, pop }) => (
            <Form>
              {/*... use these however you want */}
            </Form>
          )}
        />
    />
  </div>
);
```

`component: React.Reactnode`

```jsx
import React from 'react';
import { Formik, Form, Field, FieldArray } from 'formik'


export const FriendList = () => (
  <div>
    <h1>Friend List</h1>
    <Formik
      initialValues={{ friends: ['jared', 'ian', 'brent'] }}
      onSubmit={...}
      render={formikProps => (
        <FieldArray
          name="friends"
          component={MyDynamicForm}
        />
      )}
    />
  </div>
);


// In addition to the array helpers, Formik state and helpers
// (values, touched, setXXX, etc) are provided through a `form`
// prop
export const MyDynamicForm = ({
  move, swap, push, insert, unshift, pop, form
}) => (
 <Form>
  {/**  whatever you need to do */}
 </Form>
);
```

`children: func`

```jsx
import React from 'react';
import { Formik, Form, Field, FieldArray } from 'formik'


export const FriendList = () => (
  <div>
    <h1>Friend List</h1>
    <Formik
      initialValues={{ friends: ['jared', 'ian', 'brent'] }}
      onSubmit={...}
      render={formikProps => (
        <FieldArray name="friends">
          {({ move, swap, push, insert, unshift, pop, form }) => {
            return (
              <Form>
                {/*... use these however you want */}
              </Form>
            );
          }}
        </FieldArray>
      )}
    />
  </div>
);
```
