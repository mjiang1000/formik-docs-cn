## Form Submission

### 表单提交阶段
在Formik中要提交表单，你需要触发`handleSubmit(e)` 或者`submitForm`，二者之中的一个调用，每次都会执行以下代码

### Pre-submit
- Touch all fields. initialValues 是必填的，
- isSubmitting 变为 true
- submitCount + 1

### Validation
- isValidating 变为 true
- 异步执行所有的字段级别校验方法，将结果deeply merge
- 结果是否有错误
  - 是：中断提交，isValidating 、isSubmitting 变为false，设置errors
  - 否：isValidating 变为 false, 进入Submission阶段

### Submission
- 继续执行submission handler (i.e.onSubmit or handleSubmit)
- 您在处理程序中调用setSubmitting（false）来完成循环


### FAQ

怎么判断submission handler在执行？
isValidating is false并且isSubmitting is true

为什么在提交之前Formik touch 所有的字段？
通常只是显示已经访问过的输入框的错误提示。在提交前，Formik touch 所有的字段，这样那些隐藏的错误就会显示出来。

怎么防止二次提交？
当isSubmitting is true禁止提交

在提交之前，我如何知道表单何时进行验证？
If isValidating is true and isSubmitting is true
