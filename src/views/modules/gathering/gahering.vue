<template>
  <div class="mod-menu">
    <el-form :inline="true" :model="dataForm">
      <el-form-item>
        <el-button v-if="isAuth('sys:menu:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="dataList" row-key="id" border style="width: 100%; ">
      <el-table-column prop="id" header-align="center" label="活动编号" ></el-table-column>
      <el-table-column prop="name" header-align="center" label="活动名称" ></el-table-column>
      <el-table-column prop="summary" header-align="center" label="简介" ></el-table-column>
      <el-table-column prop="detail" header-align="center" label="详细说明" ></el-table-column>
      <el-table-column prop="sponsor" header-align="center" label="主办方" ></el-table-column>
      <el-table-column prop="starttime" header-align="center" label="开始时间" ></el-table-column>
      <el-table-column prop="endtime" header-align="center" label="截止时间" ></el-table-column>
      <el-table-column prop="address" header-align="center" label="举办地点" ></el-table-column>
      <el-table-column prop="enrolltime" header-align="center" label="报名截止" ></el-table-column>
      <el-table-column prop="name" header-align="center" label="是否可见" >
        <template slot-scope="state">
          <el-tag v-if="scope.row.state === 1" type="ok">可见</el-tag>
          <el-tag v-if="scope.row.state === 0" size="small" type="danger">不可见</el-tag>
        </template>
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:menu:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.menuId)">修改</el-button>
          <el-button v-if="isAuth('sys:menu:delete')" type="text" size="small" @click="deleteHandle(scope.row.menuId)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './gathering-add-or-update'
  export default {
    data () {
      return {
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataForm: {},
        dataList: [],
        dataListLoading: false,
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/gathering/gathering/page'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize
          })
        }).then(({data}) => {
          if (data && data.code === 20000) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {

      },
      // 删除
      deleteHandle (id) {
        this.$confirm(`确定对[id=${id}]进行[删除]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {

        }).catch(() => {})
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      }
    }
  }
</script>
