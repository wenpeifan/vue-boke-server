<template>
  <div class="configure">

    <el-row :gutter="24">
      <el-col :span="16">
        <el-card class="box-card" shadow="hover">
          <div slot="header" class="clearfix">
            <span>友链设置</span>
            <el-button style="float: right" icon="el-icon-plus" circle size="mini" type="primary" plain
                       @click="friendSta = true"></el-button>
          </div>
          <div>

            <el-table
                :data="tableData"
                style="width: 100%">
              <el-table-column
                  prop=""
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
                  prop="address"
                  label="操作"
                  width="230">
                <template slot-scope="scope">
                  <a target="_blank" href="####">
                    <el-button size="mini">去看看</el-button>
                  </a>
                  <span>
                    <el-button size="mini" type="primary" @click="edit">修改</el-button>
                  </span>
                  <el-popover
                      :ref="scope.row.id"
                      placement="top"
                      width="160">
                    <p>确定删除吗？</p>
                    <div style="text-align: right; margin: 0">
                      <el-button size="mini" type="text" @click="$refs[scope.row.id].doClose()">取消</el-button>
                      <el-button type="primary" size="mini" @click="del(scope.row.id)">确定</el-button>
                    </div>
                    <el-button type="danger" slot="reference" size="mini">删除</el-button>
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
              <el-input></el-input>
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
        tableData: [
          {
            title: '王小虎',
            website: 'www.baidu.com'
          }, {
            title: '王小虎',
            website: 'www.baidu.com'
          }, {
            title: '王小虎',
            website: 'www.baidu.com'
          },
        ]
      }
    },
    methods: {
      edit() {
        this.friend = {
          title: '1',
          website: '1',
        };
        this.friendSta = true;
      },
      submitForm() {
        this.$refs.friend.validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      }
    }
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