创建一个Android app



### 主要项目结构

最重要的文件

首先，请确保已打开 **Project** 窗口（依次选择 **View > Tool Windows > Project**），并从该窗口顶部的下拉列表中选择 **Android** 视图。随后，您可以看到以下文件：

- **app > java > com.example.myfirstapp > MainActivity**

  这是主 Activity（您的应用的入口点）。当您构建和运行应用时，系统会启动此 `Activity` 的实例并加载其布局。

- **app > res > layout > activity_main.xml**

  此 XML 文件定义 Activity 界面的布局。它包含带有文本“Hello world!”的 `TextView` 元素。

- **app > manifests > AndroidManifest.xml**

  [清单文件](https://developer.android.google.cn/guide/topics/manifest/manifest-intro.html)描述应用的基本特性并定义其每个组件。

- **Gradle Scripts > build.gradle**

  您会看到两个具有此名称的文件：一个用于项目（“项目：MyFirstApp”），一个用于“app”模块（“模块：app”）。每个模块均有自己的 `build.gradle` 文件，但此项目当前仅有一个模块。您将主要使用模块的 `build.gradle` 文件来配置 Gradle 工具如何编译和构建您的应用。如需详细了解此文件，请参阅[配置编译系统](https://developer.android.google.cn/studio/build/index.html)。



### 打开布局编辑器

首先，请按照以下步骤设置您的工作区：

1. 在 Android Studio 的“Project”窗口中，打开 **app > res > layout > activity_main.xml**。
2. 要给布局编辑器留出更多空间，请隐藏 **Project** 窗口，方法是依次选择 **View > Tool Windows > Project**（或点击 Android Studio 左侧的 **Project** 图标 ![img](https://developer.android.google.cn/studio/images/buttons/window-project.png)）。
3. 如果您的编辑器显示 XML 源代码，请点击窗口底部的 **Design** 标签。
4. 点击 **Select Design Surface** 图标 ![img](https://developer.android.google.cn/studio/images/buttons/layout-editor-design.png)，然后选择 **Blueprint**。
5. 点击布局编辑器工具栏中的 **Show** 图标 ![img](https://developer.android.google.cn/studio/images/buttons/layout-editor-show-constraints.png)，并确保选中 **Show Constraints**。
6. 确保 Autoconnect 处于关闭状态。工具栏中的提示应为 **Turn On Autoconnect** 图标 ![img](https://developer.android.google.cn/studio/images/buttons/layout-editor-autoconnect-on.png)（因为它现在处于关闭状态）。
7. 点击工具栏中的 **Default Margins** 图标 ![img](https://developer.android.google.cn/studio/images/buttons/layout-editor-margin.png)，然后选择 **16**（您稍后仍然可以调整每个视图的外边距）。
8. 点击工具栏中的 **Device for Preview** 图标 ![img](https://developer.android.google.cn/studio/images/buttons/layout-editor-device.png)，然后选择 **5.5, 1440 × 2560, 560dpi (Pixel XL)**。

您的编辑器现在应如图 3 所示。

![img](https://developer.android.google.cn/training/basics/firstapp/images/layout-editor_2x.png)





