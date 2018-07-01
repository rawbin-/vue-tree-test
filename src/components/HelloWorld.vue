<template>
  <div>
    <div class="tree-show">
      <h1>iview Tree</h1>
      <iview-tree @on-check-change="handleCheck" :data="treeData":show-checkbox="showCheckbox" :render-content="renderContent"></iview-tree>
    </div>
    <div class="tree-show">
      <h1>element Tree</h1>
      <element-tree @on-check-change="handleCheck" :data="treeData" :show-checkbox="showCheckbox" :render-content="renderContent"></element-tree>
    </div>
  </div>
</template>

<script>

  import "iview/dist/styles/iview.css"
  import "element-ui/lib/theme-chalk/index.css"
  import iviewTree from 'iview/src/components/tree'
  import elementTree from 'element-ui/packages/tree'

  const treeWalker = function(dataList,walker,key){
    dataList = dataList || [];
    dataList.forEach(function(item,index){
      walker(item);
      treeWalker(item[key] || item.children,walker);
    })
  };

  const respData = require('./testOrgData')
  const treeData = respData.data || [];
  treeWalker(treeData,item => {
    item.label = item.title = item.orgName;
  });
  export default {
    name: 'HelloWorld',
    components: {
      iviewTree,
      elementTree
    },
    data() {
      return {
        treeData,
        showCheckbox:true
      }
    },
    methods:{
      renderContent(h, {root, node, data}) {
        return h('span', {
          style: {
            display: 'inline-block',
            width: '90%',
            position:'relative'
          }
        }, [
          h('span', {
              style: {
                cursor: 'pointer',
                display: 'inline-block',
              }
            }, [
              h('span', {
                style: {
                  padding: "5px 4px",
                  borderRadius: "3px"
                }
              }, data.orgName)
            ]
          ),
          h('span', {
            style: {
              display: 'inline-block',
              position:'absolute',
              right:'0'
//							float: 'right',
//							marginRight: '50px',
            }
          }, data.taxCode)
        ]);
      },
      transTreeSelected(dataList,transedList){
        let dataObj;
        for(var i = 0,len = dataList.length; i < len; i++){
          dataObj = dataList[i];
          if(dataObj.checked || dataObj.indeterminate){
            transedList.push(dataObj)
          }
          if(dataObj.children){
            this.transTreeSelected(dataObj.children,transedList)
          }

        }
      },
      //勾选组织机构树复选框
      handleCheck(data) {
        let resultList = [];
        let transedList = [],orgInfo;
        this.transTreeSelected(this.treeData,transedList);
        for (let i = 0, len = transedList.length; i < len; i++) {
          orgInfo = transedList[i];
          resultList.push({
            orgName:orgInfo.orgName,
            orgId:orgInfo.orgId
          });
        }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .tree-show{
    display:inline-block;
    width:40%;
    vertical-align: top;
  }
  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }
</style>
