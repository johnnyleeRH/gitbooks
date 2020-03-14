# 宏观视角
- 为了降低编程的负担，窗口系统往往接管了桌面程序的主逻辑，提供了一套基于事件驱动的编程框架，业务代码由窗口系统提供的界面框架所驱动
- 上图的 GDI 含义是图形设备接口（Graphic Device Interface），它指的是一组向指定窗口（注意不是屏幕）绘制图形的方法集合。绘制的对象包括有几何图形、图像、文本等。
- 这也说明了一点，桌面操作系统和服务端操作系统的演进方向非常不一样。桌面操作系统的演进方向主要是交互范式的迭代，在向着越来越自然、越来越智能的交互前进
# 图形界面程序框架
- 大体流程
  - 键盘、鼠标、触摸屏等硬件产生了一个硬件中断；
  - 操作系统的硬件中断处理程序收到对应的事件（Event）；
  - 确定该事件的目标进程；
  - 将事件放入目标进程的事件队列（Event Queue）
- 响应事件的常见机制
  - 事件处理类
  - 委托（delegate）---不是收到事件的人自己来做，而是委托给别人，譬如signal-slot机制
- 在操作系统的所有子系统中，交互相关的子系统是毫无疑问的差异性最大的子系统。我们这里列了一个简单的对比表格</br>
  
|   类别   |                        windows                        |                 ios                 |         android         |
| :------: | :---------------------------------------------------: | :---------------------------------: | :---------------------: |
| 事件处理 |                 MSG  </br> WindowProc                 | UIEvent </br> UIResponder,delegate  | InputEvent</br>delegate |
| 事件分派 | GetMessage</br>Translate Message</br>Dispatch Message |               RunLoop               |         Looper          |
|   窗口   |                        Window                         |               UIView                |          View           |
| 绘制(2D) |         GDI</br>GDI+</br>Direct2D/DirectDraw          |            CoreGraphics             |         Canvas          |
| 绘制(3D) |        Direct3D</br>OpenGL</br>Vulkan(非官方)         | Metal</br>OpenGL</br>Vulkan(非官方) |    OpenGL</br>Vulkan    |
|   应用   |                           -                           |            UIApplication            |       Application       |
# 架构建议
- Model层：
  - Model层即承载业务逻辑的DOM
  - 如果我们用一句话来描述 Model 层的职责，那么应该是**负责业务需求的内核逻辑**，我们以前经常叫它**DataCore**
  - 从界面编程角度看，Model 层越厚越好。为什么这么说？因为这是和操作系统的界面程序框架最为无关的部分，是最容易测试的部分，也同时是跨平台最容易的部分
- View层：
  - View 层不一定会负责生成所有用户看到的 View。有的 View 是 Controller 在做某个逻辑的过程中临时生成的，那么这样的 View 就应该是 Controller 的一部分
  - View 层可能需要非常友好的委托（delegate）机制的支持。例如，支持一组界面元素的交互事件共同做委托（delegate）
  - 负责界面呈现，意味着 View 层和 Model 层的关系非常紧密，紧密到需要知道数据结构的细节，这可能会导致 Model 层要为 View 层提供一些专享的只读访问接口
  - 负责界面呈现，看似只是根据数据绘制界面，似乎很简单，但实则不简单。原因在于：为了效率，我们往往需要做局部更新的优化
- Controller层：
  - 可以有很多个 Controller，分别负责不同的用户交互需求
  - 但负责用户交互的 Controller 层，是可以被正交分解的，而且应该作正交分解，彼此完全没有耦合关系
  - Controller 层最应该思考的问题是代码的内聚性。哪些代码是相关的，是应该放在一起的，需要一一理清
  - 如果我们做得恰当，Controller 之间应该是完全无关的。而且要干掉某一个交互特别容易，都不需要删除该 Controller 本身相关的代码，只需要把创建该 Controller 的一行代码注释掉就可以了
  - 从分层角度，我们会倾向于认为 Model 层在最底层；View 层在中间，它持有 Model 层的 DOM 指针；Controller 层在最上方，它知道 Model 和 View 层，它通过 DOM 接口操作 Model 层，但它并不操作 View 去改变数据，而只是监听自己感兴趣的事件
- MVC 是很好的模型来支持用户交互。但这不是桌面程序面临的全部。另一个很重要的需求是提供应用程序的二次开发接口（API，全称为 Application Programming Interface）
# Web开发
