# yq-tree-selector

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### 简介
本组件主要用于vue手机项目不定层级渲染功能。基本实现了树状层级选择在手机端适配问题

### 安装

```
npm install yq-tree-selector
```

&nbsp;
## vue项目中使用

```
推荐在单页面使用

引入方法
import YQTreeSelector from 'yq-tree-selector'

在compenents内注册组件
components: {
  YQTreeSelector
}

页面内使用
<YQTreeSelector ref="yqTreeSelector" :data="showList" id-key="id" :load-data="loadTreeData" :on-select-changed="chooseTreeCheck" @on-error='handleError' :offset='30'/>
```

## 属性
| 属性 | 说明 | 类型 | 默认值 | 
| ------ | ------ | ------ | ------ |
| data | 可嵌套的节点属性的数组 | Array | [] |
| offset | 子节点与父节点缩进数据 | Number | 30 |

&nbsp;
### data属性
| 属性 | 说明 | 类型 | 必填 | 
| ------ | ------ | ------ | ------ |
| title | 节点名称 | String | 是 |
| children | 子节点键 | String | 是 |
| expand | 节点展开情况 | Boolean | 否 |

&nbsp;
&nbsp;
### data范例
```
data1: [
			{
				title: 'parent 1',
				expand: true,
				children: [
						{
								title: 'parent 1-1',
								expand: true,
								children: [
										{
												title: 'leaf 1-1-1'
										},
										{
												title: 'leaf 1-1-2'
										}
								]
						},
						{
								title: 'parent 1-2',
								expand: true,
								children: [
										{
												title: 'leaf 1-2-1'
										},
										{
												title: 'leaf 1-2-1'
										}
								]
						}
				]
		}
]
```

&nbsp;
&nbsp;

## 事件
| 事件名 | 说明 | 返回值 | 返回参数说明 | 
| ------ | ------ | ------ | ------ |
| load-data | 子节点懒加载事件 | item, callback | 点击的节点， 返回参数方法 |
| on-select-changed | 点击节点事件 | item | 点击的节点 |
| handleError | 选择出错事件 | err | 错误信息 |

&nbsp;
&nbsp;

## 方法
| 方法名 | 说明 | 参数 | 
| ------ | ------ | ------ |
| getCheckedNodes | 获取被勾选的节点 |无 |
| getSelectedNodes | 获取被选中的节点 | 无 |
| getCheckedAndIndeterminateNodes | 获取选中及半选节点 |无 |

### 范例
```
this.$refs["yqTreeSelector"].getSelectedNodes()
```
