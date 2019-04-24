# validateUI

[![](https://jitpack.io/v/LongMaoC/validateui.svg)](https://jitpack.io/#LongMaoC/validateui)

## 一个表单验证的lib

  表单验证框架，支持对 **activity/Fragment** 内的 **TextView/EditText** 进行规则验证
  

  ```
    compile 'com.github.LongMaoC:validateui:v3.1'
  ```


### 部分效果图(demo在根目录下 ./apk/demo.apk)

![image](https://github.com/LongMaoC/validateui/blob/master/gif/photo_common.png)
![image](https://github.com/LongMaoC/validateui/blob/master/gif/photo_shield.png)
![image](https://github.com/LongMaoC/validateui/blob/master/gif/photo_return_act.png)

# 更新内容
  * 20180531 : 新增注解@StartsWith。
  * 20180525 : 对bug进行修改。对接口IValidateResult的 **onValidateError** 的参数进行修改，把参数 **EditText** 变为 **View** 
  * 20171010 : 新增输入监听，对@Money/@MaxLength/@Password1/@Password2 注解进行修改。使用方式不变。
      当在 **控件初始化后** 调用Validate.reg(this)方法，会自动给控件添加输入规则/监听事件。详细说明可以看MoneyActivity.class的注释。
        当在 **控件初始化前** 调用Validate.reg(this)方法，则不会添加任何规则/监听事件

  * 20170914 ：有网友说@Shield不清楚有什么用，所以<span style="color: #FF0000;">增加一个界面ShieldDemoActivity.java</span>，展示@Shield注解在项目中实际使用


# 特点
  * 不关心验证过程，只关注验证结果
  * 按照自定义顺序验证
  * 验证失败可自定义动画
  * 以注解的方式进行配置

## 版本
* v3.1 新增注解@StartsWith。
* v3.0 修复bug.对接口IValidateResult#onValidateError的参数进行修改，把参数EditText变为View
* v2.3
  * 对@Money/@MaxLength/@Password1/@Password2 注解进行修改，添加规则或监听。使用方式不变。
* v2.2
  * 添加 @Shield 注解。
  * 对 Fragment 进行支持
  * 添加 简单回调接口
* v2.1 修复 兼容7.0
* v2.0
  * 增加 可以在TextView上进行标记
  * 增加 @Password1   @Password2  两个注解
  * 删掉 @RepeatLast  @Repeat 两个注解
* v1.6
  * 增加Money注解
  * 增加 常用增则规则。(邮箱，仅中文，仅数字···)
* ~~v1.5正常使用，与ButterKnife兼容~~
* ~~v1.2版本删掉，提交有误~~
* ~~v1.1 添加验证，只允许在EditText上添加~~
* ~~v1.0 基础注解，更换了验证方式~~


# 功能

| 注解              |     功能     |
| :-------------- | :--------: |
| @StartsWith     |   以固定字符串为开头，进行校验   |
| @Shield         |   屏蔽当前控件   |
| @Index          |     索引     |
| @MaxLength      |   最大长度验证   |
| @MinLength      |   最小长度验证   |
| @Money          |    金额验证    |
| @NotNull        |    非空验证    |
| @RE             |    正则验证    |
| @Password1      |  密码验证第一次   |
| @Password2      |  密码验证第二次   |
| ~~@Repeat~~     | ~~重复类型验证~~ |
| ~~@RepeatLast~~ | ~~重复类型验证~~ |



## 配置

root build.gradle
```
allprojects {
  repositories {
    ...
    maven { url "https://jitpack.io" }
  }
}
```

app build.gradle
```
  compile 'com.github.LongMaoC:validateui:v3.1'
```

## 详细说明

```
   @Index(1)
   @NotNull(msg = "不能为空！")
   @Bind(R.id.et_notnull)
   EditText etNotnull;

   @Index(3)
   @Bind(R.id.et_max)
   @MaxLength(length = 3, msg = "超出最大长度")
   EditText et_max;

   @Index(4)
   @NotNull(msg = "两次密码验证->密码一不为能空！")
   @Password1()
   @Bind(R.id.et_pw1)
   EditText etPw1;

   @Index(5)
   @NotNull(msg = "两次密码验证->密码二不为能空！")
   @Password2(msg = "两次密码不一致！！！")
   @Bind(R.id.et_pw2)
   EditText etPw2;

   @Index(10)
   @Money(msg = "格式不正确，请重新输入", keey = 2)
   @Bind(R.id.et_money)
   EditText etMoney;

   @Index(11)
   @RE(re = RE.only_Chinese, msg = "仅可输入中文，请重新输入")
   @Bind(R.id.et_only_Chinese)
   EditText etOnlyChinese;

   @Index(15)
   @NotNull(msg = "tv不能为空")
   @Bind(R.id.tv_textview)
   TextView tvTextview;

   @Index(1)
   @NotNull(msg = "请输入手机号")
   @MinLength(length = 11, msg = "请输入正确的手机号")
   @Bind(R.id.et_phone)
   EditText etPhone;

   @Index(2)
   @NotNull(msg = "请输入密码")
   @MinLength(length = 6, msg = "请输入6位以上的密码")
   @MaxLength(length = 12, msg = "请输入12位以下的密码")
   @Shield
   @Bind(R.id.et_pwd)
   EditText etPwd;

```


## 使用

#### onCreate中注册
```
Validate.reg(this);
```

#### onDestroy解注册
```
 Validate.unreg(this);
```

#### 回调(两种回调，参考 cxy.com.validateui.activity.ReturnActivity.java )

##### 方法1 在按钮的点击事件中添加简单回调
```
public void onClick(View v ){
    Validate.check(this, new SimpleValidateResult() {
        @Override
        public void onValidateSuccess() {
            Toast.makeText(ReturnActivity.this, "验证通过", Toast.LENGTH_SHORT).show();
        }
    });
}
```
##### 方法2 在按钮的点击事件中添加回调
```
 public void buttonClick() {
    Validate.check(CommonActivity.this, new IValidateResult() {
        @Override
        public void onValidateSuccess() {
            Toast.makeText(CommonActivity.this, "验证成功", Toast.LENGTH_SHORT).show();
        }

        @Override
        public void onValidateError(String msg, View view) {
            view.setFocusable(true);
            Toast.makeText(CommonActivity.this, msg, Toast.LENGTH_SHORT).show();
        }

        @Override
        public Animation onValidateErrorAnno() {
            return ValidateAnimation.horizontalTranslate();
        }
    });
}

```


#### 注解说明

以下请参照cxy.com.validateui.activity包下的activity 以便理解

|       注解        | 说明                                       | 方法                                       |
| :-------------: | :--------------------------------------- | :--------------------------------------- |
|   @StartsWith    | 以固定字符串为开头，进行校验      | msg: 提示信息<br>ignoreCase:是否忽略大小写<br>value:校验开头的字符串|
|     @Shield     | 屏蔽当前属性，不进行验证，配合Validate.check(Object,boolean,IValidateResult)使用。可参考cxy.com.validateui.activity.SHieldActivity.java | -----                                    |
|     @Index      | 索引                                       | value: 索引标记，用来标记验证的先后顺序，必须有这个注解          |
|    @NotNull     | 非空验证                                     | msg: 提示信息                                |
|       @RE       | 正则验证                                     | msg: 提示信息<br>re:正则表达式<br>RE.only_Chinese：仅中文<br>RE.only_number:仅数字<br>RE.number_letter_underline:数字、字母、下划线<br>RE.email:邮箱 |
|   @MinLength    | 最低长度验证                                   | msg: 提示信息<br>length:最低长度(包含)             |
|   @MaxLength    | 最大长度验证                                   | msg: 提示信息<br>length:最大长度(包含)             |
|     @Money      | 金额验证                                     | msg: 提示信息<br>keey:保留位数，范围1或2，大于范围取边界值    |
|   @Password1    | 密码验证第一次                                  | -----                                    |
|   @Password2    | 密码验证第二次                                  | msg:两次密码不一样时候的提示信息                       |
|   ~~@Repeat~~   | ~~分组<br>Repeat: 多个edittext关联时，非最后一个~~    | ~~flag: 标记组，当一个界面的两个或多个edittext需要关联验证时，可以设置flag分组，flag值相同为一组~~ |
| ~~@RepeatLast~~ | ~~分组<br>多个edittext关联时，最后一个~~             | ~~msg: 提示信息<br>flag:标记~~                 |


## -END-
  * 邮件(chenxingyu_cxy@163.com)
  * QQ: 1209101049
