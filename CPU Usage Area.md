##CPU Usage Area##CPU使用部分The CPU Usage area displays where time is spent in your game. When it is selected, the lower pane displays hierarchical time data for the selected frame. For information on the Profiler timeline at the top of the window see here.CPU使用部分显示了你游戏中的时间开销都花费在哪里。当他被选中时，下方的界面显示了被选中那一帧的分级时间数据，在窗口的顶部可以查看有关分析器时间线的信息。* Hierarchy mode: Displays hierarchical time data.
* 分级模式: 显示分级时间数据
* Group Hierarchy mode: Groups time data into logical groups (Rendering, Physics, Scripts etc.). Because children of any group can be in different group (e.g. some script might call rendering functions), the percentages of group times often add up to more than 100%. (This is not a bug.)* 组分级模式: 将时间数据根据逻辑类型进行分组（渲染器，物理， 脚本等）。由于每个组的节点可能同时处于另一个组中（比如说，有一些脚本可能调用了渲染的方法），所以分组时间的百分比累加值经常是大于100%的。（这并不是一个bug）The way the CPU chart is stacked can be reordered by simply dragging chart labels up and down.
CPU图标的堆叠方式可以通过简单的拖懂图标标签来进行重新排序。
### Selecting Individual Items
### 选中单独的项目
When an item is selected in the lower pane, its contribution to the CPU chart is highlighted (and the rest are dimmed). Clicking on an item again de-selects it.
当在下方页面中选中一个项目时，他所涉及到的CPU图标会被高亮显示（其余的则会变暗）。再次点击该项目则会取消选中。 Render.OpaqueGeometry is selected and its contribution is highlighted in the chart.

Render.OpaqueGeometry 被选中，并且他涉及到的部分在图标中被高亮显示。In the hierarchical time data the self time refers to the amount of time spent in a particular function not including the time spent calling sub-functions. In the screenshot above, for example 41.1% of time is spent in the Camera.Render function. This function does a lot of work and calls the various drawing and culling functions. Excluding all these functions only 2.1% of time is spent actually in the Camera.Render function. The Time ms and Self ms columns show the same information, but presented in milliseconds. So, Camera.Render takes 0.01ms, but including all the functions it calls, 0.21ms are consumed. The GC Alloc column shows how much memory has been allocated in the current frame, which will be collected later by the garbage collector. By designing your code to keep this value at zero, you can avoid the Garbage Collector from causing hiccups in your framerate.
在分级时间数据中，“self”时间指的是在某个特定的方法上的时间开销，并不包括调用子函数的时间开销。在上面的截图中，比如41.1%的时间被花费在Camera.Render函数上。这个函数做了很多的事情并且调用了各种绘制和裁剪的方法。除去这些函数，那么Camera.Render真正的时间开销就只用2.1%。Time ms列和Self ms列显示了同样的信息，只是以毫秒为单位。所以，Camera.Render消耗了0.01ms,但是算上那些他调用的函数，那就消耗了0.21ms。GC Alloc列则显示了在这一帧的时间里有多少内存被分配并且会在之后由垃圾回收器进行回收。设计你的代码来将这个数值维持在0，你就可以避免垃圾回收引起的卡帧。The Others section of the CPU profiler records the total of all areas that do not fall into Renderer, Scripts, Physics, Garbage Collection or VSync. This includes Animation, AI, Audio, Particles, Networking, Loading, and PlayerLoop.CPU分析器的Other部分记录了其他不被包括进渲染器，脚本，物理，垃圾回收或者垂直同步中的部分。其中包括了动画，AI，音频，粒子，网络，加载以及播放器循环。
### Physics Markers
### 物理标志
The descriptions below provide a brief account of what each of the various high level physics profiler markers mean:
下面的描述简单介绍了一下每一个高阶物理分析器标志的含义:* Physics.Simulate: Called from FixedUpdate. This updates the present state of the physics by instructing PhysX to run its simulation.* Physics.Simulate: 由FixedUpdate进行调用。通过指示PhysX执行模拟来更新当前的物理状态。
* Physics.Processing: Called from FixedUpdate. This is where all the non-cloth physics jobs are processed. Expanding this marker will show low level detail of the work being done internally in PhysX.* Physics.Processing: 由FixedUpdate进行调用。所有被处理的非衣物的物理作业都在这里。展开这个标志会显示作业在PhysX内完成的低阶细节信息。
* Physics.ProcessingCloth: Called from FixedUpdate. This is where all the cloth physics jobs are processed. Expanding this marker will show low level detail of the work being done internally in PhysX.* Physics.ProcessingCloth: 由FixedUpdate进行调用。所有被处理的衣物的物理作业都在这里。展开这个标志会显示作业在PhysX内完成的低阶细节信息。
* Physics.FetchResults: Called from FixedUpdate. This is where the results of the physics simulation are collected from PhysX.
* Physics.FetchResults: 由FixedUpdate进行调用。由PhysX收集的物理模拟结果都在这里* Physics.UpdateBodies: Called from FixedUpdate. This is where all the physics bodies have their positions and rotations updated as well as where messages that communicate these updates are sent.* Physics.UpdateBodies: 由FixedUpdate进行调用。哪些物理体位置或者旋转信息更新了以及传达这些更新的消息。
* Physics.ProcessReports: Called from FixedUpdate. This stage is run once the physics fixed update has concluded and is where all the various stages of responding to the results of the simulation are processed. Contacts, joint breaks and triggers are updated and messaged here. There are four distinct sub stages:
* Physics.ProcessReports: 由FixedUpdate进行调用。这一阶段将在物理固定更新结束时执行，所有响应这个模拟结果的各个阶段在这里处理。
	* Physics.TriggerEnterExits: Called from FixedUpdate. This is where OnTriggerEnter and OnTriggerExit events are processed.	
	* Physics.TriggerEnterExits: 由FixedUpdate进行调用。OnTriggerEnter和OnTriggerExit事件在这里处理。
		* Physics.TriggerStays: Called from FixedUpdate. This is where OnTriggerStay events are processed.	
	* Physics.TriggerStays: 由FixedUpdate进行调用。OnTriggerStay事件在这里处理。
		* Physics.Contacts: Called from FixedUpdate. This is where OnCollisionEnter, OnCollisionExit and OnCollisionStay events are processed.	
	* Physics.Contacts: 由FixedUpdate进行调用。OnCollisionEnter，OnCollisionExit以及OnCollisionStay事件在这里处理。
		* Physics.JointBreaks: Called from FixedUpdate. This is where updates and messages relating to joints being broken is processed.	* Physics.JointBreaks: 由FixedUpdate进行调用。关节破裂时的更新和消息在这里处理* Physics.UpdateCloth: Called from Update. This is where updates relating to cloth and their skinned meshes are made.* Physics.UpdateCloth: 由Update进行调用。有关于衣物和他们的蒙皮的更新。
* Physics.Interpolation: Called from Update. This stage deals with the interpolation of positions and rotations for all the physics objects.* Physics.Interpolation: 由Update进行调用。关于所有物理物体位置和旋转的插值处理。
### Performance Warnings
### 性能警告
There are some common performance issues the profiler is able to detect and warn you about. These warnings appear in the warning column of the lower pane, when viewing the CPU Usage.

分析器可以探测一些常见的性能问题并且发出警告。这些警告会在下方的warning列中显示，如果正在查看CPU使用率的话。A profiler warning indicating that static colliders have been moved.
分析器警告表示有静态的碰撞器被移动了。
The specific issues the profiler can detect are:

分析器可以探测一下几种特殊的问题:

* Static Collider.Modify (Expensive delayed cost)

* Static Collider.Move (Expensive delayed cost)

* Static Collider.Create (Expensive delayed cost)

* Animation.DestroyAnimationClip [Triggers RebuildInternalState]

* Animation.AddClip [Triggers RebuildInternalState]

* Animation.RemoveClip [Triggers RebuildInternalState]

* Animation.Clone [Triggers RebuildInternalState]

* Animation.Deactivate [Triggers RebuildInternalState]

In the screenshot above, the profiler is showing the Static Collider.Move warning. The warning column shows that this warning has been triggered 12 times in the current frame. The term “Delayed Cost” means that although the entry in the profiler may show a low cost (in this case 0.00ms) the action may trigger expensive operations later on.

在上面的截图中，分析器显示了Static Collider.Move 的警告。warning列显示了这个警告在当前帧中被触发了12次。“Delayed Cost”项意味着即使这个挑图在分析器中显示了很低的开销（在这个例子中是0.00ms），这个行为也可能在之后触发高开销的操作。