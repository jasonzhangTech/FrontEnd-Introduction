<!--
 * @Author: zhy
 * @Date: 2021-03-06 19:11:25
 * @LastEditTime: 2021-03-10 19:50:03
 * @LastEditors: Please set LastEditors
 * @Description: 极客时间-React实战精讲笔记
-->

# 拆分复杂度
> 按领域模型（feature）组织代码，降低耦合度【将业务逻辑拆分成高内聚松耦合的模块】；
> React技术栈实现，其实就是从根部加载多个feature对应的components、router、reducer
> 文件夹结构：1.按照feature组织源文件；2.组件和样式文件同一级；3.Redux单独文件夹；4.单元测试保持同样目录结构放在tests文件夹
> ## 注意：组件的样式文件应该进行单独引入，如果在组件中进行引用，则在组件多次引用时，打包时形成代码冗余。##
> 在rootRedux.js和rootAction中load各个feature中的redux.js和action.js
> 在routeConfig.js中load子路由route.js
> Rekit


# 开发列表也要考虑的技术要点
1. 如何翻页
2. 如何进行内容搜索
3. 如何缓存数据
4. 如何进行页面刷新

> 设计Redux的Store模型
- listItems: array
- keyword: string
- page: number
- byId: object
- fetchListPending: bool
- fetchListError: object
- listNeedReload: bool

> URL 设计以及和Store同步
- /list/page?keyword=xxx
- component --> action --> store

> 页面数据需要多个来源进行请求
1. 请求之间无依赖关系，可以并发进行
2. 请求有依赖，需要进行依次进行
3. 请求完成之前，页面显示loading状态
