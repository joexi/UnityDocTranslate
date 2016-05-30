## Memory Area

## 内存部分

There are two modes in which you can inspect memory usage of your application. This is selected in the dropdown in the top of the bottom panel.

你有两种方式可以检查应用程序的内存使用状况。你可以在下方窗口的顶部进行下拉选择。

### Simple View
### “简介”试图

The simple mode shows how memory is used through out unity on a higher level in realtime on a per frame basis.

“简介”模式实时显示了每一帧Unity各处的内存的高阶（概要）使用情况。

Unity reserves memory pools for allocations in order to avoid asking the os for memory too often. This is displayed as a reserved amount and how much is used. The areas covered by this is:

Unity会维护一个内存池来进行内存的分配，而不是频繁的请求操作系统。这将会显示为“预留量”以及有多少已经被使用。其中涉及到的：


* Unity The amount of memory tracked by allocations in native unity code
* Unity unity原生代码占用的内存总量


* Mono  The Total heap size and used heap size used by Managed Code - this memory is garbagecollected
* Mono  整个堆大小以及托管代码占用的堆大小 - 这部分内存是受到“垃圾管理”的


* GfxDriver  The estimated amount of memory the driver is using on Textures, Rendertargets, shaders and Meshdata
* GfxDriver  驱动程序在纹理，渲染目标，着色器，以及网格数据上的内存预计开销

* FMOD  The Audio drivers estimated memory usage
* FMOD  音频驱动的预计内存占用

* Profiler  Memory used for the profilerdata The numbers that are displayed are not going to show the same amount as the Task Manager or Activity Monitor, because there are some usage that is untracked by the memory profiler. This includes memory used by some drivers and memory used for Executable code.
* Profiler  分析数据的内存使用，这部分显示的数据和“Task Manager”和“Activity Monitor”并不一致，因为有一部分的内存使用是内存分析无法追踪到的。这里还包括了驱动以及可执行代码的内存占用。


Memory stats are shown for some of the most common asset/object types and include the count and the used memory (main and video memory)

内存状态显示了大部分常见的asset/object的类型，并且包括了他们的数量以及占用的内存（包括内存以及显存）

* Textures
* 纹理
* Meshes
* 网格
* Materials
* 材质
* Animations
* 动画
* Audio
* 音频

Object Count is the total number of Objects that are created. If this number rises over time then it means your game is creating some objects that are never destroyed.

Object Count 是创建对象的数量。如果这个数量随着时间增长，那就说明了你的游戏持续的创建对象而从不销毁他们。

###Detailed View

### “详情”视图

The Detailed View, will let you take a snapshot of the current state. Use the “Take Sample” button to capture detailed memory usage. Obtaining this data takes some time, so the Detailed View should not be expected to give you real-time details. After taking a sample the profiler window will be updated with a treeview where you can explore different memory usage.

“详情”视图,会让你对当前的状态作一个快照。通过“Take Sample”按钮来对内存使用的详情进行采样。获取这部分数据会消耗一些时间，所以“详情”视图并不能提供给你实时的详情。完成采样之后，profiler窗口会刷新一个树状图来来给你查看不同的内存使用状况。

This will display individual asset and game object memory usage. It will also display a reason for an object to be in memory. The reasons can be of the following:

它会单独显示每一个asset以及game object的内存使用状况。他也会显示对象处于内存中的原因。这些原因包括了:

**Assets**  Asset referenced from user or native code


**Assets**  用户或者源代码引用到的资源


**Built-in** Resources  Unity editor resources or Unity default resources


**Built-in** Resources  Unity 编辑器占用的资源以及Unity的默认资源


**Not Saved**  Objects marked as DontSave


**Not Saved**  那些被标示为DontSave的对象


**Scene Memory**  Game object and components


**Scene Memory**  游戏对象以及组件


**Other**  Objects not marked in the above categories


**Other**  上述选项没有包含的对象

When in the editor, clicking on an object in the list, will take you to the object in either the project or the scene view.

当你在编辑器中点击一个列表中的对象时，他会关联到项目中或者场景视图（scene view）中的对象。

When profiling in the editor, all numbers displayed by the memory profiler are the usage by the editor. These will be somewhat larger than when running in a player, because of editor overhead. For more precise numbers and memory usage for your app, use the profiler connection to connect to the running player. This will give the actual usage on the device.

当你在编辑器中进行分析时,所有由内存分析器（memory profiler）显示的数据都是由编辑器使用的。由于编辑器的开销，这可能会一定程度上比终端运行时更大。如果需要应用程序更精确的数据和内存使用情况，使用分析器连接（profiler connection）来连接运行中的终端，这会给你更准确的设备使用数据。

Memory reported under System.ExecutableAndDlls is read-only memory, therefore OS might discard these pages at need and later reload from the file system. So it makes little memory pressure and it rarely directly contributes to OS decision to kill application. Also some of these pages might be shared with other applications (which are using the same frameworks).

在System.ExecutableAndDlls中汇报的内存是只读内存，所以操作系统可能会丢弃这些页面然后在需要的时候重新从文件系统中加载。所以它只会造成很小的内存压力，并且它也很少影响操作系统是否杀死某应用程序的决定。而且某些页面是和其他应用程序共享的（当他们使用了同样的framework时）。


