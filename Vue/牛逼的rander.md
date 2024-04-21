```vue
<template>
  <!-- <div class="main">
    <div>
      <BlockTable :columns="tableLeftCol">
      </BlockTable>
    </div>
    <div>
      <BlockTable :columns="tableRightCol">
      </BlockTable>
    </div>
  </div> -->
  <div class="">
    <sg-row>
      <sg-col>
        <div>
          <sg-button>添加</sg-button>
          <sg-button>保存</sg-button>
          <sg-button>删除</sg-button>
        </div>
        <BlockTable :columns="tableLeftCol"
                    :data="disposeColData"
                    @row-click="rowClick">
        </BlockTable>
      </sg-col>
      <sg-col>
        <div>
          <sg-row>
            <sg-col>
              <sg-input></sg-input>
            </sg-col>
            <sg-col>
              <sg-button>添加</sg-button>
              <sg-button>删除</sg-button>
            </sg-col>
          </sg-row>
        </div>
        <BlockTable :columns="tableRightCol"
                    :data="rightData">
        </BlockTable>
      </sg-col>
    </sg-row>
  </div>
</template>

<script>
import BlockTable from './buildingBlock/BlockTable.vue'
import api from '@/api/index.js'
import cloneDeep from 'lodash/cloneDeep'

export default {
  data () {
    let _this = this
    return {
      test: false,
      dbFlag: 0,
      tableLeftCol: [
        {
          type: 'selection',
          width: 60,
          align: 'center'
        },
        {
          title: '列标题',
          key: 'title'
          // render: function (h, params) {
          //   let renderValue = _this.renderInputTemplate(h, params, 'title')
          //   return renderValue
          // }
        },
        {
          title: '列宽度',
          key: 'width',
          render: function (h, params) {
            if (params.row.widthTrig) {
              return h(
                'el-select', {
                props: {
                  value: params.row.width
                },
                on: {
                  'on-input': function (event) {
                    params.row.width = event.target.value
                  }
                },
                nativeOn: {
                  // 两个关闭方法，一是按回车关闭编辑
                  keydown: function (event) {
                    if (event.keyCode === 13) {
                      params.row.widthTrig = false
                    }
                  }
                }
              }
              )
            } else {
              return h(
                'span', {
                on: {
                  dblclick: function () {
                    params.row.widthTrig = true
                  },
                  // 点别的关闭,但是this数据拿不到
                  click: function () {

                  }
                }
              }, params.row.width
              )
            }
          }
        },
        {
          title: '类型',
          key: 'type'
        },
        {
          title: '代码值',
          key: 'code',
          render: function (h, params) {
            let renderValue = _this.renderInputTemplate(h, params, 'code')
            return renderValue
          }
        },
        {
          title: '排序',
          key: 'order'
        }
      ],
      tableRightCol: [
        {
          type: 'selection',
          width: 60,
          align: 'center'
        },
        {
          title: '字段名',
          key: 'field'
        },
        {
          title: '字段描述',
          key: 'text'
        }
      ],
      leftData: [],
      rightData: []
    };
  },

  components: {
    BlockTable
  },

  computed: {
    disposeColData () {
      let data = cloneDeep(this.leftData)
      data.forEach(ele => {
        ele.titleTrig = false
        ele.widthTrig = false
        ele.typeTrig = false
        ele.codeTrig = false
        ele.orderTrig = false
      });
      return data
    }
  },

  mounted () {

  },
  methods: {
    dblClick (params) {
      console.log(params)
    },
    rowClick (row) {
      this.rightData = row.children
    },
    changeContent () {

    },
    renderInputTemplate (h, params, colName) {
      if (params.row[colName + 'Trig']) {
        return h(
          'sg-input', {
          props: {
            value: params.row[colName]
          },
          on: {
            'on-input': function (event) {
              params.row[colName] = event.target.value
            }
          },
          nativeOn: {
            // 两个关闭方法，一是按回车关闭编辑
            keydown: function (event) {
              if (event.keyCode === 13) {
                params.row[colName + 'Trig'] = false
              }
            }
          }
        }
        )
      } else {
        // let content;
        // if (params.row[colName] === '') {
        //   content = <div>&nbsp;</div>
        // } else {
        //   content = params.row[colName]
        // }
        return h(
          'span', {
          on: {
            dblclick: function () {
              params.row[colName + 'Trig'] = true
            },
            // 点别的关闭,但是this数据拿不到
            click: function () {

            }
          }
        }, '\u00A0'
        )
      }
    }
  },
  created () {
    api.queryColumnsData('/flowengine/job/colset/listWorkJobColumns', {
      params: {
        listName: '创建工作'
      }
    }).then((res) => {
      let { rows } = res.data
      this.leftData = rows
    }).catch((err) => {
      console.log(err)
    })
  }
}

</script>
<style>
.main {
  display: flex;
  justify-content: space-between;
}
</style>

```

