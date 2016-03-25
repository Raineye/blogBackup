# Redux阅读笔记

## 介绍

**Redux**视图让**state**的变化变得可预测，这些限制条件反应在Redux的三大原则中。

### 三大原则

- 单一数据源


- State是只读的

	唯一改变state的方法就是触发action，action是一个用于描述已发生事件的普通对象。
	例如：
	
	```
	store.dispatch({
	  type: 'COMPLETE_TODO',
	  index: 1
	});
	```


- 使用纯函数来执行修改

	为了描述action如何改变state tree，你需要编写reducers。它只是一些纯函数，接受先前的state和action，并返回新的state tree的不同部分，而且你可以控制它们被调用的顺序，传入附加数据，甚至编写可复用的reducer来处理一些通用任务。`(state, action) => state`就是reduceres的核心概念。
	