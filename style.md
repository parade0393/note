### **样式系统介绍**

​		Android系统为我们提供了丰富的样式系统可以应用在我们的App上的控件，可以让控件呈现不同的外观。你可以使用themes, styles, and view attributesl来影响默认的样式系统，下图显示了每种样式方法的优先顺序，优先级从上至下，也就是说如果你在theme里设置了字体大小，同时也在xml里使用view attributes 设置了字体大小，那么view attributes会覆盖theme

#### **View attributes**

* 使用view attributes为每个view显示设置属性(view atributes和style一样不可重复使用)
* view attributes一般用于自定义view或者不具备统一性的样式(例如margin和padding还有constrain)

#### Styles

* 使用style可以创建一个样式集合，这个集合里的样式可重复应用在多个view上。例如集合里可包括(字体大小和字体颜色)
* 适用于整个应用中少量的通用设计,例如可为头部应用一个style集合，也可以为一些按钮一个用一个style集合用来覆盖Defalut style

#### Default style

* Android系统提供的默认样式

#### Themes

* 利用theme为整个应用设置color
* 利用theme为整个应用设置font
* 应用在同一个类型的所有控件，例如 text views 或者 radio buttons
* 可用作配置属性以便在整个应用中应用

#### **TextAppearance**

* 仅适用于对文本属性进行设置，例如fontFamily

Android在为视图设置样式时，你可以自定义使用theme，style，attributes的组合，attributes总会覆盖style和theme中指定的任何内容，style总会覆盖theme中指定的任何内容