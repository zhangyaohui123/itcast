<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>分类列表</el-breadcrumb-item>
    </el-breadcrumb>
    <div>
      <el-button type="success" plain @click='dialogVisible4Add = true'>添加分类</el-button>
    </div>
    <div>
    <!-- columns表示所有的列  tree-structure 表示数据格式(树形或列表，true树形)  data-source 表示实际的列表数据  deleteCate处理删除操作  showFrom 处理编辑操作  refresh  处理刷新操作-->
    <tree-grid :columns="columns" :tree-structure="true" :data-source="dataSource" @deleteCate="deleteCategory" @showFrom="showEditFrom" @refresh="initList"></tree-grid>
    </div>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[5, 10, 50, 100]"
      :page-size="pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
  </div>
</template>
<script>
import TreeGrid from './TreeGrid.vue'
import {getCategories} from '../../api/api.js'
export default {
  data () {
    return {
      dataSource: [], // 数据源 来自后台
      columns: [{
        text: '分类名称',
        dataIndex: 'cat_name',
        width: 500
      }, {
        text: '是否有效',
        dataIndex: 'cat_deleted',
        width: 100
      }, {
        text: '排序',
        dataIndex: 'cat_level',
        width: 100
      }],
      dialogVisible4Add: false,
      currentPage: 1,
      pagesize: 5,
      total: 10
    }
  },
  methods: {
    deleteCategory () {
      console.log('deleteCategory')
    },
    showEditFrom () {
      console.log('showEditFrom')
    },
    refresh () {
      console.log('refresh')
    },
    handleSizeChange (val) {
      // 改变每页显示条数
      this.pagesize = val
      this.initList()
    },
    handleCurrentChange (val) {
      // 改变当前页码
      this.currentPage = val
      this.initList()
    },
    initList () {
      getCategories({type: 3, pagenum: this.currentPage, pagesize: this.pagesize}).then(res => {
        // console.log(res)
        if (res.meta.status === 200) {
          this.dataSource = res.data.result
          this.pagesize = res.data.pagesize
          this.total = res.data.total
        }
      })
    }
  },
  components: {
    TreeGrid
  },
  mounted () {
    this.initList()
  }
}
</script>
<style>
  .el-breadcrumb {
    background-color: #D3DCE6;
    height: 50px;
    line-height: 50px;
    padding-left: 10px;
  }
  .el-pagination {
    background-color: #D3DCE6;
    padding-top: 10px;
    height: 35px;
    line-height: 35px;
  }
</style>
