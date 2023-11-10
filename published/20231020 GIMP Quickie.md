[#]: subject: "GIMP 快速教程"
[#]: via: "https://www.gimp.org/tutorials/GIMP_Quickies/"
[#]: author: "Pat David"
[#]: translator: "TimXiedada"
[#]: keywords: "GIMP"
[#]: url: "https://linux.cn/article-16365-1.html"

GIMP 快速教程：缩放、裁剪和旋转图像
======

![][0]

> 本文翻译自 GIMP 官网，是 GIMP 教程的一部分。

### 目的

恭喜你！你在电脑上安装了 GIMP！GIMP 是一个非常强大的图像处理软件，但是不要被它吓到。即使你没有时间学习高级的电脑图形处理技能，GIMP 仍然可以是一个非常有用和方便的快速修改图像的工具。

我希望这些例子能帮助你解决那些需要对图像应用进行快速修改的小需求。希望这也能让你学习到 GIMP 更强大的图像编辑能力。

为了便于快速查看，我将在这篇快速教程中涵盖以下四个要点：

 - 更改图像的大小（尺寸），即缩放 
 - 更改 JPEG 的大小（文件大小） 
 - 剪裁图像 
 - 旋转或翻转图像 

为了与这个页面之前的版本保持保持一致，我将使用 NASA 提供的天文学家每日图像（APOD）中的一张图片。

为了跟随这些快速示例，你只需要知道如何找到你的图片并打开它：“<ruby>文件<rt>File</rt></ruby> → <ruby>打开<rt>Open</rt></ruby>”。

### 调整图像的大小（尺寸），即缩放

你可能会遇到一个图像太大，不适合特定用途的问题（例如，嵌入网页、在线发布或包含在电子邮件中）。在这种情况下，你通常会希望将图像缩小到更小的尺寸，以便更好地满足你的需求。

在 GIMP 中轻松完成这个任务非常简单。

我们使用的图片是哈勃望远镜拍摄的马头星云红外成像图。

当你第一次在 GIMP 中打开图像时，很可能会发现图像被缩放，以便整个图像都能适合你的画布。对于这个示例，需要注意的是，GIMP 窗口顶部的窗口装饰会显示一些关于图像的信息。

![GIMP画布的视图，顶部显示窗口信息](https://www.gimp.org/tutorials/GIMP_Quickies/Scale-View-Pixel-Size-Original.png)

请注意，窗口顶部的信息显示了当前图像的像素尺寸（在这个例子中，像素尺寸为 1225×1280）。

要调整图像的大小到新的尺寸，我们只需要调用“<ruby>缩放图像<rt>Scale Image</rt></ruby>”对话框：“<ruby>图像<rt>Image</rt></ruby> → <ruby>缩放图像...<rt>Scale Image ...</rt></ruby>”。

这将打开“缩放图像”对话框：

![“缩放图像”对话框](https://www.gimp.org/tutorials/GIMP_Quickies/Scale-Image-Dialog.png)

在“缩放图像”对话框中，你会发现一个*可以输入新宽度和高度的地方*。如果你知道所需图的新尺寸，可以在这里填写相应的值。

“<ruby>宽度<rt>Width</rt></ruby>”和“<ruby>高度<rt>Height</rt></ruby>”输入框右侧，你也会**注意到一个小链**。这个图标显示了宽度和高度值被相互锁定，这意味着改变一个值会导致另一个值的变化，以保持相同的宽高比（图像中不会出现奇怪的压缩或拉伸）。

例如，如果你想要将图像宽度调整到 600 像素，你可以在这个宽度输入框中输入这个值，高度将自动更改以保持图像的宽高比：

![调整大小到 600 像素](https://www.gimp.org/tutorials/GIMP_Quickies/Scale-Image-Dialog-Scaled.png)

如你所见，在宽度一栏输入 600 像素后会自动将高度更改为 627 像素。

此外，我还展示了 “<ruby>质量<rt>Quality</rt></ruby>” 选项下的不同选项。默认值是“<ruby>立方<rt>Cubic</rt></ruby>”，但为了保持最佳质量，最好使用 “Sinc（Lanczos3）”。

如果你想使用不同类型的值（而不是像素大小）指定一个新的尺寸，可以通过点击“px”下拉菜单来更改输入值的类型：

![更改输入值类型](https://www.gimp.org/tutorials/GIMP_Quickies/Scale-Image-Dialog-Value-Types.png)

这种情况的一个常见用途是，如果你想要以原始尺寸的百分比指定一个新的尺寸。在这种情况下，你可以更改为“<ruby>百分比<rt>percent</rt></ruby>”，然后在任何字段中输入 50 来将图像缩小一半。

一旦你缩放了图像，别忘了保存你所做的更改：选中 “<ruby>文件<rt>File</rt></ruby> → <ruby>导出...<rt>Exprert ...</rt></ruby>” 以新的文件名导出，或者 “<ruby>文件<rt>File</rt></ruby> → <ruby>覆盖 {文件名}<rt>Overwrite {FILENAME}</rt></ruby>” 覆盖原始文件（谨慎使用）。

有关使用缩放图像的更多信息，你可以查看文档。

### 修改 JPEG 文件的大小

你也可以在导出为 JPEG 等格式时修改图像的文件大小。JPEG 是一种**有损**压缩算法，这意味着在将图像保存为 JPEG 格式时，你将牺牲一些图像质量来获得较小的文件大小。

我使用已经将其调整为 200 像素宽（请参见上方）的“马头星云”图像，并使用不同级别的 JPEG 压缩将其导出，以比较 JPEG 压缩的效果： 

![比较不同级别的 JPEG 压缩](https://www.gimp.org/tutorials/GIMP_Quickies/JPG-Compression-Sample.png)

如你所见，即使在质量设置为 80 的情况下，图像的文件大小显著减少了 77％，而图像质量仍然相当合理。

当你完成任何正在进行的图像修改，并准备导出时，只需通过以下方式调用导出对话框：“文件 → 导出 …” 这将调用“<ruby>导出图像<rt>Export Image</rt></ruby>”对话框： 

![导出图像对话框](https://www.gimp.org/tutorials/GIMP_Quickies/Export-Image-Dialog.png)

你可以在此*输入新的文件名*。如果文件名里包含扩展名（此时为 “.jpg”），GIMP 会尝试为你导出对应的文件格式。此处将图像导出为 JPEG 格式。

如果你需要将文件导出到不同的位置，也可以通过位置窗格导航到计算机上的新位置。当你准备好导出图像时，只需按“<ruby>导出<rt>Expert</rt></ruby>”按钮。

这将调用“<ruby>导出图像为 JPEG<rt>Export Image as JPEG</rt></ruby>”对话框，你可以在其中更改导出的质量：
 
![从“导出为JPG”对话框中更改导出的质量](https://www.gimp.org/tutorials/GIMP_Quickies/Export-Image-as-JPEG.png)
  
现在你可以在此对话框中更改导出质量。如果你还勾选了“<ruby>在图像窗口中显示预览<rt>Show preview in image window</rt></ruby>”选项，画布上的图像将更新以反映你输入的质量值。这也将启用“<ruby>文件大小：<rt>File size:</rt></ruby>”信息，告诉你导出后的文件大小。你可能需要移动一些窗口才能在背景中查看画布上的预览。

当你对结果满意时，按“导出”按钮进行导出。 

要查看有关导出不同图像格式的更多详细信息，请参阅手册中的“从 GIMP 中获取图像”。

### 裁剪图像

有很多原因可能会使你想要裁剪图像。你可能想要删除无用的边框或信息，或者你可能希望最终图像的焦点集中在某些特定的细节上。 

简而言之，裁剪就是一个将图像缩小到比你开始时小的操作： 

![原始图像（左），裁剪图像（右）](https://www.gimp.org/tutorials/GIMP_Quickies/Crop-Example.png)

裁剪图像的步骤非常简单。你可以通过工具面板使用裁剪工具： 

![工具面板上的裁剪工具](https://www.gimp.org/tutorials/GIMP_Quickies/Crop-Tool.png)

或者通过菜单访问裁剪工具：“<ruby>工具<rt>Tools</rt></ruby> → <ruby>变换工具<rt>Transform Tools</rt></ruby> → <ruby>裁剪<rt>Crop</rt></ruby>”。

![GIMP裁剪工具光标](https://www.gimp.org/tutorials/GIMP_Quickies/Crop-Cursor.png)
 
 一旦激活该工具，画布上的鼠标光标会改变,以表示正在使用裁剪工具。
 
 现在你可以在画布上的任何位置单击鼠标，然后拖动鼠标到新位置以高亮显示初始选择区域以进行裁剪。在这个阶段，你不必担心精确度，因为在实际裁剪之前，你可以修改最终选区。
  
![使用裁剪工具的第一步](https://www.gimp.org/tutorials/GIMP_Quickies/Crop-First.png)

在选择要裁剪的区域后，你会发现选区仍然处于活动状态。在这一点上，将鼠标光标悬停在选区的任何四个角或边缘上都会改变鼠标光标，并高亮显示该区域，以对裁剪进行精调。

你可以点击并拖动任何一侧或一角来移动该部分的选区。

一旦你对裁剪区域满意，只需按键盘上的回车键即可提交裁剪。在你想从头开始或决定不裁剪时，可以按键盘上的 `Esc` 键退出操作。

有关在GIMP中裁剪的更多信息，请参阅文档。 

#### 另一种方法

另一种裁剪图像的方法是首先使用矩形选择工具进行选择： 

![裁剪选区 矩形选择工具](https://www.gimp.org/tutorials/GIMP_Quickies/Crop-Select-Tool.png)

或者通过菜单：“<ruby>工具<rt>Tools</rt></ruby> → <ruby>选择工具<rt>Selection Tools</rt></ruby> → <ruby>矩形选择<rt>Rectangle Select</rt></ruby>”，然后你可以以与裁剪工具相同的方式高亮显示选区，并调整选区。一旦你有一个喜欢的选区，你就可以通过以下方式将图像裁剪到该选区的大小：“<ruby>图像<rt>Image</rt></ruby> → <ruby>裁剪到选区<rt>Crop to Selection</rt></ruby>”。

### 旋转或翻转图像

可能有时你需要旋转图像。例如，你可能使用相机在垂直方向拍摄了图像，但是 GIMP 并没有自动旋转（GIMP 通常会为你自动处理，但并非总是如此）。

有时你也可能想翻转图像。这些命令都位于同一个菜单项下：“<ruby>图像<rt>Image</rt></ruby> → <ruby>变换<rt>Transform</rt></ruby>”。

#### 翻转图像

如果你想翻转你的图像，变换菜单提供了两种选项：水平翻转或垂直翻转。此操作将沿着指定的轴翻转（镜像）图像。例如，这里显示了在单个图像上应用的所有翻转操作： 

![应用到基图像（左上角）的所有翻转](https://www.gimp.org/tutorials/GIMP_Quickies/Flip-Sample-Arrow.jpg)

#### 旋转图像

变换菜单中的图像旋转限制为 90° 顺时针/逆时针或 180°。 不要误解这意味著 GIMP 不能执行任意角度旋转。任意旋转是针对每个图层进行处理的，而这里的图像旋转适用于整个图像。 

![旋转示例：原始（左上角），顺时针旋转90°（右上角），逆时针旋转90°（左下角），180°（右下角）。 ](https://www.gimp.org/tutorials/GIMP_Quickies/Rotate-Sample.jpg)

### 总结

这里展示的简单示例只是冰山一角。然而，这些是许多没有学习太多图像处理知识的人经常进行的常见修改。希望这个教程对你有所帮助。 我鼓励你阅读其他教程，了解更高级的图像处理方法！

*（题图：MJ/9bbe01ba-7cc1-49b1-91a6-2b3d13594503）*

------
via: https://www.gimp.org/tutorials/GIMP_Quickies/

作者：Pat David
译者：[TimXiedada](https://github.com/TimXiedada)
编辑：[wxy](https://github.com/wxy)

本文由贡献者投稿至 [Linux 中国公开投稿计划](https://github.com/LCTT/Articles/)，采用 [CC-BY-SA 协议](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 发布，[Linux中国](https://linux.cn/) 荣誉推出


[0]: https://img.linux.net.cn/data/attachment/album/202311/10/095101mn4v16pnt2wt6wqn.png