<template>
  <div class="configure">

    <el-row :gutter="24">
      <el-col :span="16">
        <el-card class="box-card" shadow="hover">
          <div slot="header" class="clearfix">
            <span>友链设置</span>
            <el-button style="float: right" icon="el-icon-plus" circle size="mini" type="primary" plain
                       @click="edit(0)"></el-button>
          </div>
          <div>

            <el-table
                :data="tableData"
                style="width: 100%">
              <el-table-column
                  type="index"
                  label="#"
                  width="50">
              </el-table-column>
              <el-table-column
                  prop="title"
                  label="名称">
              </el-table-column>
              <el-table-column
                  prop="website"
                  label="站点">
              </el-table-column>
              <el-table-column
                  prop="opera"
                  label="操作"
                  width="230">
                <template slot-scope="scope">
                  <a href="#">
                    <el-button size="mini">去看看</el-button>
                  </a>
                  <span>
                    <el-button size="mini" type="primary" @click="edit(scope.row)">修改</el-button>
                  </span>
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
                    <el-button type="danger" size="small" slot="reference">删除</el-button>
                  </el-popover>

                </template>
              </el-table-column>
            </el-table>

          </div>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card class="box-card" shadow="hover">
          <div slot="header" class="clearfix">
            <span>标识设置</span>
          </div>
          <div>
            <el-alert
                title="文章以及评论作者的特殊现实标识，如原作者、博主"
                type="info"
                show-icon>
            </el-alert>
            <div class="set_author">
              <el-input v-for="(item, i) in marks" v-model="item.val" :key="i"></el-input>
              <el-button type="primary" plain>保存</el-button>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <el-dialog
        title="添加修改友链"
        :visible.sync="friendSta"
        width="30%">
      <div class="friend_dig">
        <el-form :model="friend" status-icon :rules="rules" ref="friend" label-width="60px" class="demo-ruleForm">
          <el-form-item label="名称" prop="title">
            <el-input v-model="friend.title" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="站点" prop="website">
            <el-input v-model="friend.website"></el-input>
          </el-form-item>
          <el-form-item style="margin-top: 30px">
            <el-button type="primary" @click="submitForm()">保 存</el-button>
            <el-button @click="friendSta = false">取 消</el-button>
          </el-form-item>
        </el-form>
      </div>
    </el-dialog>

  </div>
</template>

<script>
  export default {
    data() {
      return {
        friend: {},
        rules: {
          title: [{required: true, message: '请输入活动名称', trigger: 'blur'},],
          website: [{required: true, message: '请输入活动名称', trigger: 'blur'},],
        },
        friendSta: false,
        tableData: [],
        marks: [],
      }
    },
    methods: {
      // 列表详情
      getData() {
        this.$Axios.get(`/yun/blog/friend_list?pageIndex=${this.pageIndex}&pageSize=${this.pageSize}`).then(res => {
          console.log(res, "配置列表");
          if ( res.code === 200 ) {
            this.tableData = res.data;
          } else {
            this.$message.error(res.message);
          }
        });
      },

      // 获取标识设置
      getMarks() {
        this.$Axios.get('/yun/blog/get_configure').then(res => {
          console.log(res, "获取标识设置");
          if ( res.code === 200 ) {
            this.marks = res.data;
          } else {
            this.$message.error(res.message);
          }
        });
      },

      // 编辑弹框
      edit( row ) {
        console.log(row);
        this.friend = {
          title: row.title,
          website: row.website,
        }
        if ( row !== 0 ) {
          this.friend.id = row.id;
        }
        this.friendSta = true;
      },

      // 提交表单
      submitForm() {
        this.$refs.friend.validate((valid) => {
          if (valid) {
            let dat = this.friend;
            this.$Axios.post('/yun/blog/operation_friend', dat).then(res => {
              if ( res.code === 200 ) {
                this.$message.success("编辑成功");
                this.friendSta = false;
                this.getData();
              } else {
                this.$message.error(res.message);
              }
            });
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },

      // 列表删除
      popoverCancle(id) {
        let dat = {
          id: id,
          sta: 1,
        };
        this.$Axios.post('/yun/blog/operation_friend', dat).then(res => {
          if ( res.code === 200 ) {
            this.$message.success("删除成功");
            this.getData();
          } else {
            this.$message.error(res.message);
          }
        });
      },

    },
    created() {
      this.getData();
      this.getMarks();
    },
  }
</script>

<style lang="less" scoped>
  @import "~@/assets/less/theme.less";
  .configure {
    .set_author {
      margin-top: 20px;
      button {
        margin-top: 10px;
      }
    }
    .friend_dig {
      padding: 10px 40px 0;
    }
  }
</style>