##CPU Usage Area






当在下方页面中选中一个项目时，他所涉及到的CPU图标会被高亮显示（其余的则会变暗）。再次点击该项目则会取消选中。

Render.OpaqueGeometry 被选中，并且他涉及到的部分在图标中被高亮显示。




下面的描述简单介绍了一下每一个高阶物理分析器标志的含义:







	* Physics.TriggerEnterExits: 由FixedUpdate进行调用。OnTriggerEnter和OnTriggerExit事件在这里处理。
	
	* Physics.TriggerStays: 由FixedUpdate进行调用。OnTriggerStay事件在这里处理。
	
	* Physics.Contacts: 由FixedUpdate进行调用。OnCollisionEnter，OnCollisionExit以及OnCollisionStay事件在这里处理。
	





分析器可以探测一些常见的性能问题并且发出警告。这些警告会在下方的warning列中显示，如果正在查看CPU使用率的话。



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