<template>
  <div class="container">
    <section class="tableOne">
      <h2 align="center">自定义表头样式</h2>
      <!-- 添加自定义方法 renderHeaderOne、renderHeaderTwo、renderHeaderThree -->
      <el-table
        :data="tableData"
        border
        stripe
        style="width: 100%">
        <el-table-column
          prop="name"
          label="姓名"
          align="center"
          :render-header="renderHeaderOne">
        </el-table-column>
        <el-table-column
          prop="date"
          label="日期"
          align="center"
          :render-header="renderHeaderTwo">
        </el-table-column>
        <el-table-column
          prop="address"
          label="地址"
          align="center"
          :render-header="renderHeaderThree">
        </el-table-column>
      </el-table>
    </section>

    <section class="tableTwo">
      <h2 align="center">自定义表头样式和整列的拖动</h2>
      <div @mouseleave="moveTableOutside">
        <el-table
          class="drag_table"
          :data="tableData"
          border
          stripe
          :cell-class-name="cellClassName"
          :header-cell-class-name="headerCellClassName"
          style="width: 100%">
          <el-table-column v-for="(col, index) in tableHeader" :key="index"
            :prop="col.prop"
            :label="col.label"
            :width="col.width"
            :min-width="col.minWidth"
            :type="col.type"
            :header-align="col.headerAlign"
            :column-key="index.toString()"
            :render-header="renderHeader">
          </el-table-column>
        </el-table>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data () {
    return {
      tableData: [{
        name: '王小虎',
        date: '2016-05-02',
        address: '上海市普陀区金沙江路 1518 弄'
      }, {
        name: '王老五',
        date: '2016-05-04',
        address: '上海市普陀区金沙江路 1517 弄'
      }, {
        name: '王大锤',
        date: '2016-05-01',
        address: '上海市普陀区金沙江路 1519 弄'
      }, {
        name: '王小龙',
        date: '2016-05-03',
        address: '上海市普陀区金沙江路 1516 弄'
      }],

      tableHeader: [{
        prop: 'name',
        label: '姓名'
      }, {
        prop: 'date',
        label: '时间'
      }, {
        prop: 'address',
        label: '地址'
      }],
      dragState: {
        startIndex: -1, // 拖动起始元素的index
        endIndex: -1, // 拖动结束元素的index
        afterMoveIndex: -1, // 拖动后元素的index
        dragging: false, // 是否正在拖动
        direction: null, // 拖动方向
        moveTableOutsideBack: false // 拖出到table外之后又拖回来
      }
    }
  },
  methods: {
    // 在渲染表头的时候,会调用此方法, h为createElement的缩写版, 也可以添加事件click、change等
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
    },
    renderHeaderTwo (h, { column, $index }) {
      return h('span', [
        h('span',
          {
            props: {

            },
            class: 'errorIcon2',
            style: {
              marginLeft: ''
            },
            on: {
              click: ($event) => { this.renderHeaderTwoClick($event, column.label, $index) }
            }
          }),
        h('span', column.label)
      ])
    },
    renderHeaderThree (h, { column, $index }) {
      return h('span', [
        h('span',
          {
            class: 'errorIcon3',
            on: {
              click: () => {
                console.log(`${column.label}   ${$index}`)
              }
            }
          }),
        h('span', column.label),
        h('span',
          {
            style: {
              cursor: 'pointer'
            },
            on: {
              click: () => {
                console.log(`${column.label}   ${$index}`)
              }
            }
          }, '@@')
      ])
    },
    renderHeaderTwoClick (event, columnLabel, index) {
      console.log(event)
      console.log(`${columnLabel}   ${index}`)
    },

    // drag_table在渲染表头时调用
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
    },

    // 按下鼠标开始拖动
    handleMouseDown (e, column) {
      // 判断是鼠标左键
      if (e.button === 0) {
        this.dragState.dragging = true
        this.dragState.startIndex = parseInt(column.columnKey)
        console.log(`开始移动的位置 ${this.dragState.startIndex}`)
        // 给当前要拖动列的th设置class
        document.querySelectorAll('.drag_table table thead tr th')[this.dragState.startIndex].className = 'dragging_column'
        // 给拖动时的虚拟容器添加宽高
        let table = document.getElementsByClassName('drag_table')[0]
        let virtual = document.getElementsByClassName('virtual')
        // 设置每一列的宽度、高度
        for (let item of virtual) {
          item.style.height = table.clientHeight - 1 + 'px'
          item.style.width = item.parentElement.parentElement.clientWidth + 'px'
        }
        this.dragState.moveTableOutsideBack = false
      }
    },

    // 拖动中
    handleMouseMove (e, column) {
      // 判断是鼠标左键
      if (e.button === 0) {
        if (this.dragState.dragging) {
          let currentIndex = parseInt(column.columnKey) // 拖动的当前列index
          console.log(`移动到了${currentIndex}`)
          if (currentIndex !== this.dragState.startIndex) {
            this.dragState.direction = currentIndex - this.dragState.startIndex < 0 ? 'left' : 'right' // 判断拖动方向
            this.dragState.afterMoveIndex = currentIndex
          } else {
            this.dragState.direction = null
          }
        } else {
          return false
        }
      }
    },

    // 鼠标放开结束拖动
    handleMouseUp (e, column) {
      // 判断是鼠标左键
      if (e.button === 0) {
        // 拖出当前table外之后又拖回来，不再进行易位操作（拖出去时已处理）
        if (this.dragState.moveTableOutsideBack) {
          return false
        } else {
          this.dragState.endIndex = parseInt(column.columnKey) // 记录结束列index
          console.log(`结束移动的位置 ${this.dragState.endIndex}`)
          if (this.dragState.startIndex !== this.dragState.endIndex) {
            this.dragColumn(this.dragState)
          }
          this.finishDragInit()
        }
      }
    },

    // 拖动到当前table之外的处理
    moveTableOutside () {
      if (this.dragState.dragging) {
        this.dragState.endIndex = this.dragState.startIndex
        console.log(`已移动到table外，结束移动的位置 ${this.dragState.endIndex}`)
        if (this.dragState.startIndex !== this.dragState.endIndex) {
          this.dragColumn(this.dragState)
        }
        this.finishDragInit()
        this.dragState.moveTableOutsideBack = true
      }
    },

    // 拖动易位
    dragColumn ({ startIndex, endIndex, direction }) {
      console.log(`从${startIndex}移动到了${endIndex}`)
      // 判断是向左移动还是向右移动
      if (direction === 'left') {
        this.tableHeader.splice(endIndex, 0, this.tableHeader[startIndex])
        this.tableHeader.splice(startIndex + 1, 1)
      } else {
        this.tableHeader.splice(endIndex + 1, 0, this.tableHeader[startIndex])
        this.tableHeader.splice(startIndex, 1)
      }
    },

    // 拖动完成后的初始化
    finishDragInit () {
      // 给当前要拖动列的th取消class
      document.querySelectorAll('.drag_table table thead tr th')[this.dragState.startIndex].className = ''
      // 再次初始化拖动状态
      this.dragState = {
        startIndex: -1,
        endIndex: -1,
        afterMoveIndex: -1,
        dragging: false,
        direction: null,
        moveTableOutsideBack: false
      }
    },

    // 动态给表头单元格添加 class，实现拖动中的虚线效果
    headerCellClassName ({ column, columnIndex }) {
      return columnIndex === this.dragState.afterMoveIndex ? `drag_active_${this.dragState.direction}` : ''
    },

    // 动态给表头单元格th添加class，实现拖动中的背景
    cellClassName ({ column, columnIndex }) {
      return (columnIndex === this.dragState.startIndex ? `dragging_column` : '')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scode>
.container {
  margin: 30px 80px;
}
.tableOne,
.tableTwo {
  padding-top: 50px;
}
.errorIcon {
  display: inline-flex;
  flex-direction: row;
  width: 15px;
  height: 15px;
  background: url("../assets/images/error.png") 100% 100% no-repeat;
}
.errorIcon2 {
  display: inline-flex;
  flex-direction: row-reverse;
  margin-right: 3px;
  width: 15px;
  height: 15px;
  background: url("../assets/images/error.png") 100% 100% no-repeat;
}
.errorIcon3::before {
  content: "##";
}
.errorIcon,
.errorIcon2,
.errorIcon3:hover {
  cursor: pointer;
}

.drag_table th {
  cursor: move;
}
.virtual {
  position: fixed;
  display: block;
  margin-top: -13px;
  margin-left: -11px;
}
.drag_active_left .virtual {
  border-left: 1px dotted #666;
  z-index: 99;
}
.drag_active_right .virtual {
  border-right: 1px dotted #666;
  z-index: 99;
}
.thead-cell {
  display: inline-flex;
  flex-direction: column;
}
.thead-cell:before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
.dragging_column {
  background-color: #f3f3f3 !important;
}
</style>
