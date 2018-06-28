# ElementUI-Table-column_render-header
ElementUI的Table表格，官方网站上提供了很多样式，但是在日常开发中还会碰到各种情况，显然官方提供的是不能满足需求的。那么，我们就根据自己的需求对table进行改造。

先丢出关于Table的官方文档的传送门，请戳这里→ [http://element-cn.eleme.io/#/zh-CN/component/table](http://element-cn.eleme.io/#/zh-CN/component/table)
官方对Table相关的Attributes介绍的不是很详细，对于不太熟悉人，很难搞明白，实际操作起来很难受。

下面就关于**Table-column**中**render-header**的运用，稍作说明,具体请移步此项目文件中查看。
|参数|说明|类型|可选值|默认值|
|-|-|-|-|-|
|render-header|列标题 Label 区域渲染使用的 Function|Function(h, { column, $index })|—|—|

#### 一、自定义表头样式
![自定义表头样式](https://github.com/darkerxi/ElementUI-Table-column_render-header/raw/master/My_image/tableHead.jpg)
```js
renderHeaderOne (h, { column, $index }) {
  return h('span', [
    h('span', column.label),
    h('span', {
      class: 'errorIcon',
      on: {
        click: () => {
          console.log(`${column.label}   ${$index}`)
        }
      }
    })
  ])
}
```

#### 二、自定义表头样式和整列的拖动
![自定义表头样式和整列的拖动](https://github.com/darkerxi/ElementUI-Table-column_render-header/raw/master/My_image/tableDrag.gif)
```js
renderHeader (h, { column, $index }) {
  // 这里可以根据$index的值来对自身需求进行修改，
  return h('span', {
    'class': ['thead-cell'],
    on: {
      mousedown: ($event) => { this.handleMouseDown($event, column) },
      mouseup: ($event) => { this.handleMouseUp($event, column) },
      mousemove: ($event) => { this.handleMouseMove($event, column) }
    }
  }, [
    h('span', [
      h('span',
        {
          class: $index === 0 ? 'el-icon-star-off' : $index === 1 ? 'el-icon-time' : $index === 2 ? 'el-icon-location' : '',
          style: {
            // marginLeft: ''
          },
          on: {
          }
      }),
      h('span', column.label)
    ]),
    h('span', {
      'class': ['virtual']
    })
  ])
}
```
