<template>
  <div class="wrap">
    <div class="login">
      <div class="title">登录</div>
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" status-icon label-width="100px">
        <el-form-item label="账号" prop="username">
          <el-input v-model="ruleForm.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="ruleForm.password" type="password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="验证码" prop="code">
          <el-input v-model="ruleForm.code" autocomplete="off" class="input_float_left"></el-input>
          <span class="code_span" v-html="codeSVG" @click="getCode"></span>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  import MD5 from 'js-md5';

  export default {

    data() {
      // 自定义验证规则
      let checkUsername = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入姓名'));
        } else {
          callback();
        }
      };
      let validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
        } else {
          callback();
        }
      };

      return {
        ruleForm: {
          username: '',
          password: '',
          code: '',
        },
        // 验证码信息
        codeSVG: '',
        codeKey: '',

        rules: {
          username: [
            { required: true, validator: checkUsername, trigger: 'blur' }
          ],
          password: [
            { required: true, validator: validatePass, trigger: 'blur' }
          ],
          code: [
            { required: true, message: '请输入验证码', trigger: 'blur' },
          ],
        }
      };
    },

    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let dat = {
              user: this.ruleForm.username,
              password: MD5(this.ruleForm.password),
              code: this.ruleForm.code,
              key: this.codeKey
            };
            this.$Axios.post('/yun/blog/login', dat).then(res => {
              console.log(res, "登录返回的信息");
              if ( res.code === 200 ) {
                this.$message.success("登录成功");
                this.$router.push({name: 'IndexMain'});
              } else {
                this.$message.error(res.message);
              }
            });
          } else {
            console.log('验证失败');
            return false;
          }
        });
      },

      // 获取验证码
      getCode() {
        this.$Axios.get('/yun/verification/code').then(res => {
          console.log(res, "登录返回的信息");
          if ( res.code === 200 ) {
            this.codeSVG = res.data.svg;
            this.codeKey = res.data.key;
          } else {
            this.$message.error(res.message);
          }
        });
      },
    },

    created() {
      this.getCode();
    },
  }
</script>

<style lang="less" scoped>
  @import "~@/assets/less/theme.less";

  .wrap {
    width: 100%;
    height: 100vh;
    border: 1px solid transparent;
    background: rgba(0, 0, 0, 0.2);

    .login {
      width: 400px;
      margin: 300px auto;
      text-align: center;
      .title {
        color: #666666;
        font-size: 18px;
        font-weight: bold;
        margin: 30px 0 30px 100px;
        width: 300px;
        text-align: center;
      }
      button {
        width: 200px;
        margin-top: 50px;
      }
    }
    .input_float_left {
      width: 130px;
      float: left;
    }
    .code_span {
      display: inline-block;
      float: right;
      cursor: pointer;
      background: #333;
    }
  }
</style>
