### React Native

Formik 100%兼容React Native and React Native Web. 由于ReactDom和React Native处理form和text input的差异，
有些差异需要注意。本节将向您介绍它们以及我们认为最佳实践

### 要点
在进一步讨论之前，这里有一个关于如何使用Formik和React Native实例，它展示了关键的区别
```jsx
// Formik x React Native example
import React from 'react';
import { Button, TextInput, View } from 'react-native';
import { Formik } from 'formik';

export const MyReactNativeForm = props => (
  <Formik
    initialValues={{ email: '' }}
    onSubmit={values => console.log(values)}
  >
    {props => (
      <View>
        <TextInput
          onChangeText={props.handleChange('email')}
          onBlur={props.handleBlur('email')}
          value={props.values.email}
        />
        <Button onPress={props.handleSubmit} title="Submit" />
      </View>
    )}
  </Formik>
);
```

如您所见，使用Formik与React DOM和React Native之间的差异是:

1. `props.hanldeSubmit` 传给了 `<Button onPress={...} />` 而不是html `<form onSubmit={...} />`，因为RN中没有 `<form />`元素
2. `<TextInput />` 使用 `props.handleChange(fieldName)` 和`handleBlur(fieldName)`而不是直接赋值callbacks，因为RN中不能像web一样能轻易自动的获取`fieldName`，所以要传参。一样可以使用`setFieldValue(fieldName, value)` 和 `setFieldTouched(fieldName, bool)`