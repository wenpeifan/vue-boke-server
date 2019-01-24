<template>
  <div>

    <el-form ref="form" :model="form" :rules="rules" label-width="120px">
      <el-form-item label="标题" prop="title">
        <el-input v-model="form.title"></el-input>
      </el-form-item>
      <!--<el-form-item label="类型">
        <el-select v-model="form.type" placeholder="请选择活动区域">
          <el-option label="类型1" value="shanghai"></el-option>
          <el-option label="类型12" value="beijing"></el-option>
        </el-select>
      </el-form-item>-->

      <el-form-item label="首图" prop="title">
        <el-upload
            action="https://jsonplaceholder.typicode.com/posts/"
            list-type="picture-card"
            :on-preview="handlePictureCardPreview"
            :on-remove="handleRemove">
          <i class="el-icon-plus"></i>
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="dialogImageUrl" alt="">
        </el-dialog>
      </el-form-item>

      <el-form-item label="内容">
        <div id="editor">
          <mavon-editor
              ref="mavon"
              @imgAdd="imgAdd"
              :boxShadow="false"
              codeStyle="monokai-sublime"
              style="height: 100%"
              v-model="cont">

          </mavon-editor>
        </div>
      </el-form-item>

      <el-form-item label="标签">
        <el-checkbox-group v-model="form.tag">
          <el-checkbox label="标签1" value="type1"></el-checkbox>
          <el-checkbox label="标签2" value="type2"></el-checkbox>
          <el-checkbox label="标签3" value="type3"></el-checkbox>
          <el-checkbox label="标签4" value="type4"></el-checkbox>
        </el-checkbox-group>
      </el-form-item>
      <el-form-item label="自定义发布时间">
        <el-date-picker type="date" placeholder="选择日期" v-model="form.date"></el-date-picker>
      </el-form-item>
      <el-form-item label="允许评论">
        <el-switch v-model="form.delivery"></el-switch>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('form')">发布</el-button>
        <el-button type="primary" @click="submitForm('form')">存为草稿</el-button>
        <el-button>重置</el-button>
      </el-form-item>
    </el-form>

  </div>
</template>

<script>
  import {mavonEditor} from 'mavon-editor'
  import 'mavon-editor/dist/css/index.css'

  export default {

    components: {
      mavonEditor,
    },
    data() {
      return {
        /**
         * lw 添加、修改博客
         * @param id 博客编号
         * @param tag_id 标签编号字符串
         * @param title 标题
         * @param content 内容
         * @param is_draft 是否为草稿
         * @param img 首图
         */


        form: {
          title: '',
          // type: '',
          tag: ['标签1', '标签2'],

        },
        cont: '',
        dialogImageUrl: '',
        dialogVisible: false,

        rules: {
          title: [
            { required: true, message: '请输入标题', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '请输入验证码', trigger: 'blur' }
          ],
          code: [
            { required: true, message: '请输入验证码', trigger: 'blur' },
          ],
        }
      }
    },
    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let dat = {

            };
            console.log(dat, "dat");
            /*this.$Axios.post('/yun/blog/login', dat).then(res => {
              if ( res.code === 200 ) {
                // this.$message.success("登录成功");
                // this.$router.push({name: 'IndexMain'});
              } else {
                this.$message.error(res.message);
              }
            });*/
          } else {
            console.log('验证失败');
            return false;
          }
        });
      },
      imgAdd(name, file) {
        console.log(file);
        let formdata = new FormData();
        formdata.append('file', file);
        this.$Axios.post('/yun/oss/upload', formdata, '', {headers: {'Content-Type': 'multipart/form-data'}}).then(res => {
          console.log(res);
          if (res.code === 200) {
            this.$refs.mavon.$img2Url(name, `${res.data.url}?x-oss-process=style/bstu.cn`);
          }
        })
      },
      // 图像上传
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
      },
    }

  }
</script>

<style lang="less" scoped>
  @import "~@/assets/less/theme.less";

  #editor {
    width: 100%;
    height: 580px;
    margin: 0;
    /*/deep/ .v-note-op {*/
    /*box-shadow: none;*/
    /*border: 1px solid #e0e0e0;*/
    /*}*/
    /*/deep/ .v-note-panel {*/
    /*box-shadow: none;*/
    /*border: 1px solid #e0e0e0;*/
    /*margin-top: -1px;*/
    /*}*/
  }
</style>
