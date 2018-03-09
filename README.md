<--.vue文件-->

<template>
  <div class="list">
      <el-row :span='24' style="margin:20px;">
        <el-button type="success" plain size="mini">添加</el-button>
        <el-button type="primary" plain size="mini">删除</el-button>
        <el-button type="info" plain size="mini">修改</el-button>
        </el-row>
        <!-- @cell-dblclick="dbclick" -->
      <el-table
            :data="TableDate"
            
            border
            style="width: 50%">
            <el-table-column
                type="selection"
                width="55"
            >

            </el-table-column>

            <!-- 部门名称 -->
            <el-table-column
              label="部门名称"
              >
              <template scope="scope">
                  <!--  -->
                  <span v-for="(space, levelIndex) in scope.row._level" :key="levelIndex" class="ms-tree-space"></span>
                  <span class="button is-outlined is-primary is-small" v-if="toggleIconShow(scope.row)" @click="toggle(scope.row)">
                      <i v-if="!scope.row._expanded" class="el-icon-arrow-right" aria-hidden="true"></i>
                      <i v-if="scope.row._expanded" class="el-icon-arrow-down" aria-hidden="true"></i>
                  </span>
                  <span v-else class="ms-tree-space"></span>
                  <span :title="scope.row.name">
                      {{ scope.row.name }}
                      
                  </span>
              </template>
            </el-table-column>
            <!-- 组织机构代码 -->
            <el-table-column
              label="组织机构代码"
             >
              <template scope="scope">
                    <el-input size="small" v-model="scope.row.age" placeholder="请输入内容" @change="handleEdit(scope.$index, scope.row)"></el-input> 
                    <span :title="scope.row.age">
                        {{ scope.row.age }}
                    </span>
              </template>
            </el-table-column>
            <!-- 所属地区 -->
            <el-table-column
              label="所属地区"
              >
              <template scope="scope">
                  <span :title="scope.row.sex">
                      {{ scope.row.sex }}
                  </span>
              </template>
            </el-table-column>
            <!-- 上级部门 -->
            <el-table-column
              label="上级部门"
              >
              <template scope="scope">
                  <span :title="scope.row.id">
                      {{ scope.row.id }}
                  </span>
              </template>
            </el-table-column>
        </el-table>


  </div>
</template>

<script>
  import TreeJs from './Tree.js'

  export default TreeJs
</script>
<style scoped lang="less">  
  .ms-tree-space{position: relative;  
    top: 1px;  
    display: inline-block;  
    font-family: 'Glyphicons Halflings';  
    font-style: normal;  
    font-weight: 400;  
    line-height: 1;  
    width: 18px;  
    height: 14px;}  
  .ms-tree-space::before{content: ""}  
  table td{  
    line-height: 26px;  
  }  
</style>



<--.js文件-->
Array.prototype.removeByValue = function(val) {
  //对数组原型添加删除指定项的方法
  for(var i=0; i<this.length; i++) {
      if(this[i] == val) {
          this.splice(i, 1);
          break;
      }
  }
};

export default {
    name: 'article-tree',
    data () {
      return {
        TableDate:[
         {  
          id: 1,  
          parentId: 0,  
          name: '测试1',  
          age: 18,  
          sex: '男',  
          children: [  
            {  
              id: 2,  
              parentId: 1,  
              name: '测试2',  
              age: 22,  
              sex: '男'  
            }  
          ]  
         },  
         {  
          id: 3,  
          parentId: 0,  
          name: '测试3',  
          age: 23,  
          sex: '女',  
          children: [  
            {  
              id: 4,  
              parentId: 3,  
              name: '测试4',  
              age: 22,  
              sex: '男'  
            },  
            {  
              id: 5,  
              parentId: 3,  
              name: '测试5',  
              age: 25,  
              sex: '男'  
            },  
            {  
              id: 6,  
              parentId: 3,  
              name: '测试6',  
              age: 26,  
              sex: '女',  
              children: [  
                {  
                  id: 7,  
                  parentId: 6,  
                  name: '测试7',  
                  age: 27,  
                  sex: '男'  
                }  
              ]  
            }  
          ]  
         },  
         {  
          id: 18,  
          parentId: 0,  
          name: '测试8',  
          age: 18,  
          sex: '男',
          children:[
            {  
              id: 19,  
              parentId: 18,  
              name: '测试9',  
              age: 22,  
              sex: '男'  
            },  
          ]  
         }  
         ]  
      }
    },
    methods: {
      handleEdit(row,col){
         
      },
        /**
         * 表格双击促发时间
         */
        dbclick(row, column, cell, event){
          console.log(column)
            console.log(cell)
        },
        /**
         * 点击展开和关闭的时候，图标的切换
         */
      toggleIconShow (record) {
        if (record.children && record.children.length > 0) {
            return true
        }
        return false
       },
       /**
       * 展开下级行数据rowData
       */
      toggle(rowData) {
          let me = this;
          let childLen = rowData.children.length;
          if(rowData._expanded){
                // 收缩
                let dataArr=[];
                dataArr.push(rowData);
                let arr = me.getChildFlowId(dataArr,[]);
                for(let i=0; i < childLen; i++){
                    me.TableDate.map((value)=>{
                        console.log(value.parentId)
                        if(arr.indexOf(value.parentId) > -1){
                            me.TableDate.removeByValue(value);
                        }
                    });
                }
                
          } else{
                //展开
                rowData.children = me.setSpaceIcon(rowData.children,rowData._level);
                let index = me.TableDate.indexOf(rowData);
                let pre = me.TableDate.slice(0,index+1);
                let last = me.TableDate.slice(index+1);
                let concatChildren = pre.concat(rowData.children);
                me.TableDate = concatChildren.concat(last);
                console.log(me.TableDate)
          }
          rowData._expanded = !rowData._expanded;
      },
       // 获取子级的flowId递归函数
        getChildFlowId(data,emptyArr){
          let me = this;
          Array.from(data).forEach((record)=>{
              emptyArr.push(record.id);
              if(record.children&&record.children.length > 0){
                  let childFlowIdArr = me.getChildFlowId(record.children,emptyArr);
                  emptyArr.concat(childFlowIdArr);
              }
          });
          return emptyArr;
         },
        setSpaceIcon(data,level){
          // 设置第一列的空格和方向按钮
          let me = this;
          let _level = 0;
          data.forEach((value)=>{
              value._expanded = false;
              if(level !== undefined && level !== null){
                  _level = level + 1;
              } else {
                  _level = 1;
              }
              value._level = _level;
              if(value.children&&value.children.length > 0){
                  me.setSpaceIcon(value.children, _level);
              }
          });
          return data;
        }

    },

  }
  
