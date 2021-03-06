# Yelda 用户使用手册

## 1. 软件概述

### 概述

Yelda是一个专业的糖网眼底图像标注系统。

## 2. 运行环境

我们已将测试系统放在阿里云的服务器上。[系统入口](http://47.94.216.78:8080/)

## 3. 使用方法

### 登录

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307226087763.jpg)

本系统为内部系统，在初始化时，默认只有一个管理员。
账号:admin
密码:12345678 （后续可修改）

其他用户需要通过注册，并且管理员赋予权限才能进入系统。（权限分为：医生、客人和专家）

### 注册

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307226743144.jpg)

* 用户名用于登录。3位以上，32位以下，数字+字母。
* 昵称用于显示。（32位）建议输入中文名字。
* 密码：8位以上，32位以下，数字+字母
* 密码和确认密码需要一致。
* 在注册完成后，会自动跳转到登录界面。
* 但是进入系统需要管理员赋予权限。

### 管理员修改权限

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307227193072.jpg)

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307227350923.jpg)

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307227385550.jpg)


管理员进入系统之后，点击进入账户列表，可以对用户进行权限管理。在修改用户权限为医生后，该用户就可以进入系统进行标注。

### 上传头像

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307227554630.jpg)

用户在用户设置中可以选择上传照片作为头像。

### 修改用户信息

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307227687365.jpg)

* 在用户设置界面可以修改邮件和密码。
* 在修改之后，点击修改账户信息即可完成修改。

### 导入图像

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307227899655.jpg)

* 管理员需要先将需要标注的眼底图像上传到服务器中
	* 点击“选取文件”（可以批量选择文件）
	* 然后点击上传到服务器，等待上传完成即可。

### 分配任务

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307228057992.jpg)

点击状态栏，可以对状态进行筛选（一共有4种状态）

* 未分配：图像还没有被分配给医生进行标注。
* 进行中：图像已经被分配给医生进行标注，但医生还没有标注完成。
* 有分歧：医生都对该图像进行了标注，但医生对该图像的标注存在分歧，需要管理员进行标注，作为最终结果。
* 已完成：该图像标注工作已经完成。

选中出于未分配状态下的图像，即可对该图像进行分配任务（可以批量分配任务）


### 分配任务

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307228324995.jpg)

选中一批图像后，点击分配任务。在该界面中，需要选中两个以上医生，将任务分配给他们。


### 医生标注

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307228485079.jpg)

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307228533983.jpg)

医生进入该系统后，可以查看任务列表
任务状态分为三种

* 未标注：医生还没有开始进行标注
* 标注中：医生在进行标注中（标注中的任务可以进入任务继续修改）
* 已完成：医生已经完成该标注任务（已完成的任务不可点击进入）

我们提供了如下的图像处理功能：

* 自由缩放：点击后，再次点击图片即可以缩放图片。（只能自由缩放原图。）
* 调节对比度和亮度。
* 去除赤光。
* 如果需要查看调节完对比度和亮度的去除赤光图，需要在选择完对比度和亮度后才能点击。

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307229113144.jpg)


界面右侧提供标注选项。在完成标注之后，需要点击保存，完成保存标注。在确认标注无需修改后，点击提交，即可完成标注任务。

### 管理员管理图像列表

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307229358258.jpg)


* 未分配：图像还没有被分配给医生进行标注。
* 进行中：图像已经被分配给医生进行标注，但医生还没有标注完成。
* 有分歧：两个医生标注完后，如果存在分歧，则会交由第三个医生进行标注。如果仍然有分歧，则会分配给一个专家进行标注。
* 已完成：该图像标注工作已经完成。

### 工作质量统计

![](http://o7d2h0gjo.bkt.clouddn.com/2018-07-05-15307229546484.jpg)

管理员在用户列表中点击用户，可以进入工作质量统计界面。


