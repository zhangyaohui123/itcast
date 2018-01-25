<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>分类列表</el-breadcrumb-item>
    </el-breadcrumb>
    <div>
      <el-button type="success" plain @click='addHandler'>添加分类</el-button>
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
     <!-- 添加分类弹窗 -->
    <el-dialog
      title="添加分类"
      :visible="dialogVisible4Add"
      @close='closeUserDialog("cate")'
      width="50%">
      <div>
        <span>分类名称：</span>
        <el-input class='cname' v-model="cate.cat_name"></el-input>
      </div>
      <div>
        <span>父级分类：</span>
        <el-cascader
          :options="cateList"
          v-model="selectedOptions"
          :props='propsDefine'
          :show-all-levels="false"
          @change="handleChange">
        </el-cascader>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible4Add = false">取 消</el-button>
        <el-button type="primary" @click="submitCate">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import TreeGrid from './TreeGrid.vue'
import {getCategories, addCate} from '../../api/api.js'
export default {
  data () {
    return {
      propsDefine: {
        value: 'cat_id',
        label: 'cat_name'
      },
      cateList: [],
      selectedOptions: [],
      cate: {
        cat_name: '',
        cat_level: '',
        cat_pid: ''
      },
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
    submitCate () {
      // 加工分类参数数据
      if (this.selectedOptions.length === 0) {
        this.cate.cat_pid = 0
        this.cate.cat_level = 1
      } else {
        this.cate.cat_pid = this.selectedOptions[this.selectedOptions.length - 1]
      }
      // 提交表单
      addCate(this.cate).then(res => {
        if (res.meta.status === 201) {
          // 刷新列表
          this.initList()
          // 关闭窗口
          this.dialogVisible4Add = false
          // 提示
          this.$message({
            type: 'success',
            message: res.meta.msg
          })
        }
      })
    },
    handleChange () {
      console.log('handleChange')
    },
    addHandler () {
      // 获取所有的分类列表数据
      getCategories({type: 2}).then(res => {
        if (res.meta.status === 200) {
          this.cateList = res.data
          this.dialogVisible4Add = true
        }
      })
    },
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
      // 获取分类列表数据
      getCategories({type: 3, pagenum: this.currentPage, pagesize: this.pagesize}).then(res => {
        if (res.meta.status === 200) {
          this.dataSource = res.data.result
          this.pagesize = res.data.pagesize
          this.total = res.data.total
        }
      })
    },
    closeUserDialog (flag) {
      // 关闭添加用户弹窗
      if (flag === 'cate') {
        this.dialogVisible4Add = false
      } else if (flag === 'edit') {
        this.dialogVisible4Edit = false
      } else {
        this.dialogVisible4Role = false
      }
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
  .cname {
    width: 67%;
  }
</style>
