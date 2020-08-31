# React + Redux + antd 实现全局 loading

在实际项目中，如果环境比较慢，接口会耗费比较长的时间来返回值，为了防止用户在这个过程中多次点击导致一些不可预测的问题，我们常常需要添加 loading 遮罩加载效果。

React 项目中，我们使用了 antd 的 Spin 组件，结合 Redux，实现了全局 loading。



