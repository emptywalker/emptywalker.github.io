#### 创建 layout xml 文件的正确姿势
刚刚在创建 `ActionBar` 的 `menu` 布局文件时，根据文档要求声明成 `res/menu/main_activity_actions.xml`。于是我就在 `res/` 下创建了一个 `menu` 文件夹，然后创建 `main_activity_actions.xml` 文件。创建的时候还让我选择了布局方式是线性的还是相对的。我看文档的 `toot-layout` 是 `menu` ，于是就手动改成 `menu` 了。

但我在 activity 中引用时候：

```android
menuInflater.inflate(R.menu.main_activity_actions, menu);
```
告诉我没有找到这个资源文件，根据提示，我又创建了一个，发现 `AndroidStudio` 自动帮我创建好了文件路径以及 `xml` 的`root-layout`。

**正确方式：** 在创建 `xml` 的 `layout` 文件时，先写好引用，然后让 `AndroidStudio` 自动创建。