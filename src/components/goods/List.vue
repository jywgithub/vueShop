<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区域 -->
    <el-card>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input
            placeholder="请输入内容"
            v-model="queryInfo.query"
            clearable
            @clear="getGoodsList()"
          >
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="getGoodsList()"
            ></el-button>
          </el-input>
        </el-col>
        <el-col :span="8">
          <el-button type="primary" @click="goAddPage()">添加商品</el-button>
        </el-col>
      </el-row>
      <el-table :data="goodsList" border stripe>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="商品名称" prop="goods_name"></el-table-column>
        <el-table-column label="商品价格(元)" prop="goods_price" width="95px">
        </el-table-column>
        <el-table-column label="商品重量" prop="goods_weight" width="70px">
        </el-table-column>
        <el-table-column label="创建时间" prop="add_time" width="140px">
          <template slot-scope="scope">
            {{ scope.row.add_time | dateFomat }}
          </template>
        </el-table-column>
        <el-table-column label="操作" width="120px">
          <template slot-scope="scope">
            <el-button
              type="primary"
              size="mini"
              icon="el-icon-edit"
            ></el-button>
            <el-button
              type="danger"
              size="mini"
              icon="el-icon-delete"
              @click="deleteGoodsById(scope.row.goods_id)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[10, 15, 20, 40]"
        :page-size="10"
        layout="total, sizes, prev, pager, next, jumper"
        background
        :total="total"
      >
      </el-pagination>
    </el-card>
  </div>
</template>
<script>
export default {
  data() {
    return {
      queryInfo: {
        query: "",
        pagenum: 1,
        pagesize: 10,
      },
      goodsList: [],
      total: 0,
    }
  },
  created() {
    this.getGoodsList()
  },
  methods: {
    //   根据分页获取对呀的商品参数
    async getGoodsList() {
      const { data: res } = await this.$http.get("goods", {
        params: this.queryInfo,
      })
      if (res.meta.status !== 200)
        return this.$message.error("获取商品列表失败")
      this.goodsList = res.data.goods
      this.total = res.data.total
      console.log(this.goodsList)
    },
    handleSizeChange(val) {
      console.log(val)
      this.queryInfo.pagesize = val
      this.getGoodsList()
    },
    handleCurrentChange(val) {
      this.queryInfo.pagenum = val
      this.getGoodsList()
    },
    async deleteGoodsById(id) {
      const confirmResult = await this.$confirm(
        "此操作将永久删除该商品,是否继续?",
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).catch(err => err)
      if (confirmResult !== "confirm")
        return this.$message.info("已取消删除")
      const { data: res } = await this.$http.delete(`goods/${id}`)
      if(res.meta.status !==200)return this.$message.error('删除失败')
      this.$message.success('删除成功!')
      this.getGoodsList();
    },
    goAddPage(){
      this.$router.push('/goods/add')
    }
  },
}
</script>
<style lang="less" scoped>
</style>