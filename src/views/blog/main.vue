<template>
  <div class="blog">

    <div class="param">
      <el-form :inline="true" class="demo-form-inline">
        <el-form-item label="关键字">
          <el-input placeholder="关键字"></el-input>
        </el-form-item>
        <el-form-item label="标签">
          <el-select placeholder="标签">
            <el-option label="标签1" value="shanghai"></el-option>
            <el-option label="标签2" value="beijing"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary">查询</el-button>
        </el-form-item>

        <el-button style="float: right;" type="default" @click="goEdit(0)">添加</el-button>
      </el-form>
    </div>

    <div class="list">

      <el-table
          :data="tableData"
          border
          style="width: 100%">
        <el-table-column
            type="index"
            prop="date"
            label="序号"
            width="80">
        </el-table-column>
        <el-table-column
            prop="img"
            label="首图"
            width="100">
          <template slot-scope="scope">
            <img width="50" height="50" :src="scope.row.img" alt="">
          </template>
        </el-table-column>
        <el-table-column
            prop="title"
            label="标题"
            width="300">
        </el-table-column>
        <el-table-column
            prop="tag_name"
            label="标签">
        </el-table-column>
        <el-table-column
            prop="user_name"
            label="作者">
        </el-table-column>
        <el-table-column
            prop="time"
            label="创建时间">
          <template slot-scope="scope">
            {{scope.row.time.replace(/T/,' ').replace('.000Z','')}}
          </template>
        </el-table-column>
        <el-table-column
            prop="is_draft"
            label="是否为草稿">
          <template slot-scope="scope">
            <!--<span v-if="scope.row.is_draft === 1">是</span>-->
            <!--<span v-if="scope.row.is_draft === 0">否</span>-->

            <el-switch
                v-model="scope.row.is_draft"
                @change="editDraftState(scope.row.id, scope.row.is_draft)"
                active-color="#409eff"
                inactive-color="#dcdfe6"
                :active-value=1
                :inactive-value=0>
            </el-switch>

          </template>
        </el-table-column>
        <el-table-column
            prop="operate"
            label="操作"
            width="150px">
          <template slot-scope="scope">
            <el-button @click="goEdit(scope.row.id)" type="text">查看</el-button>
            <el-button @click="goEdit(scope.row.id)" type="text" style="margin-right: 10px;">编辑</el-button>
            <el-popover
                placement="top"
                width="160"
                title="操作提示"
                v-model="scope.row.visible">
              <p>确定删除吗？</p>
              <div style="text-align: right; margin-top: 10px;">
                <el-button type="text" @click="scope.row.visible = false" style="color: #9a9a9a;">取消</el-button>
                <el-button type="primary" @click="popoverCancle(scope.row.id)"
                           style="padding: 5px 10px; background: #409EFF; color: #fff;">确定
                </el-button>
              </div>
              <el-button type="text" slot="reference">删除</el-button>
            </el-popover>
          </template>
        </el-table-column>

      </el-table>

    </div>

    <div class="page">
      <el-pagination
          background
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageIndex"
          :page-sizes="[10, 20, 30, 50]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
      </el-pagination>
    </div>

  </div>
</template>

<script>

  export default {
    data() {
      return {
        tableData: [],
        // 分页
        pageIndex: 1,
        pageSize: 10,
        total: 0,
      }
    },
    methods: {
      // 列表详情
      getData() {
        this.$Axios.get(`/yun/blog/server_list?pageIndex=${this.pageIndex}&pageSize=${this.pageSize}`).then(res => {
          console.log(res, "博客列表");
          if ( res.code === 200 ) {
            this.tableData = res.data.list;
            this.total = res.data.total;
            this.pageIndex = res.data.pageIndex;
            this.pageSize = res.data.pageSize;
          } else {
            this.$message.error(res.message);
          }
        });
      },

      // 列表删除
      popoverCancle(id) {
        let dat = {
          id: id,
          sta: 1,
          draft: '',
        };
        this.$Axios.post('/yun/blog/blog_status', dat).then(res => {
          if ( res.code === 200 ) {
            this.$message.success("删除成功");
            this.getData();
          } else {
            this.$message.error(res.message);
          }
        });
      },

      // 分页
      handleSizeChange(val) {
        console.log(`每页 ${val} 条`);
        this.pageSize = val;
        this.getData();
      },
      handleCurrentChange(val) {
        console.log(`当前页: ${val}`);
        this.pageIndex = val;
        this.getData();
      },

      // 设置是否为草稿
      editDraftState(id, is_draft) {
        console.log(id, is_draft);
        let dat = {
          id: id,
          draft: 0,
        };
        if ( is_draft === 0 ) {
          dat.draft = 1;
        } else {
          dat.draft = 0;
        }
        this.$Axios.post('/yun/blog/blog_status', dat).then(res => {
          if ( res.code === 200 ) {
            this.$message.success("设置成功");
            this.getData();
          } else {
            this.$message.error(res.message);
          }
        });
      },

      // 跳转到编辑/详情页
      goEdit(id) {
        this.$router.push({
          name: 'BlogEdit',
          query: {
            id: id
          }
        });
      },

    },
    created() {
      this.getData();
    },
  }
</script>

<style lang="less" scoped>
  @import "~@/assets/less/theme.less";

  .blog {
    .param {
      border-bottom: 1px solid #f1f1f1;
    }
    .list {
      margin-top: 22px;
    }
    .page {
      text-align: right;
      margin-top: 22px;
    }
  }
</style>
