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

      <el-form-item label="首图" prop="img">
        <div class="upload_style">
          <el-upload
              class="avatar-uploader"
              :show-file-list="false"
              :http-request="imgUpload">
            <img v-if="form.img" :src="form.img" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </div>
      </el-form-item>

      <el-form-item label="内容" prop="content">
        <div id="editor">
          <mavon-editor
              ref="mavon"
              @imgAdd="imgAdd"
              :boxShadow="false"
              codeStyle="monokai-sublime"
              style="height: 100%"
              v-model="form.content">

          </mavon-editor>
        </div>
      </el-form-item>

      <el-form-item label="标签" prop="tag">
        <el-checkbox-group v-model="form.tag">
          <el-checkbox v-for="(item, i) in tag_list" :label="item.name" :value="item.id" :key="i"></el-checkbox>
        </el-checkbox-group>
      </el-form-item>

      <el-form-item label="自定义发布时间" prop="time">
        <el-date-picker type="date" placeholder="选择日期" v-model="form.time"></el-date-picker>
      </el-form-item>

      <el-form-item label="允许评论">
        <el-switch v-model="form.is_evaluate"></el-switch>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm('form', 0)">{{id !== "0" ? '修改' : '发布' }}</el-button>
        <el-button type="primary" @click="submitForm('form', 1)">存为草稿</el-button>
        <el-button @click="resetData">重置</el-button>
      </el-form-item>

    </el-form>
  </div>
</template>

<script>
  import {mavonEditor} from 'mavon-editor';
  import 'mavon-editor/dist/css/index.css';
  import moment from 'moment';

  export default {

    components: {
      mavonEditor,
    },
    data() {
      return {
        id: this.$route.query.id,
        form: {
          title: '',
          // type: '',
          img: '',
          tag: [],
          content: '',
          time: '',
          is_draft: 0,
          is_evaluate: true,
        },
        tag_list: [],

        rules: {
          title: [
            { required: true, message: '请输入标题', trigger: 'blur' }
          ],
          img: [
            { required: true, message: '请上传图片', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '请编辑内容', trigger: 'blur' }
          ],
          tag: [
            { required: true, message: '请选择标签', trigger: 'blur' }
          ],
        }
      }
    },
    methods: {
      // 判断是否是添加页
      isAddPage(){
        if ( this.id !== "0" ) {
          this.getDetail();
        }
      },

      // 获取标签列表
      getTag() {
        this.$Axios.get('/yun/blog/tag_list').then(res => {
          console.log(res, "标签数据");
          if ( res.code === 200 ) {
            this.tag_list = res.data.list;
          } else {
            this.$message.error(res.message);
          }
        });
      },

      // 获取详情数据
      getDetail() {
        this.$Axios.get('/yun/blog/detail?id=' + this.id).then(res => {
          console.log(res, "详情数据");
          if ( res.code === 200 ) {
            let tag = [];
            this.tag_list.map(item => {
              item.id === res.data.tag_id && (tag = [item.name]);
            });
            this.form = {
              ...res.data,
              tag: tag,
              is_evaluate: res.data.is_evaluate === 0 ? false : true,
              time:  moment(res.data.time),
            };
          } else {
            this.$message.error(res.message);
          }
        });
      },

      // 表单提交
      submitForm(formName, is_draft) {
        // is_draft = 0 为正常，1为草稿
        /**
         * lw 添加、修改博客
         * @param id 博客编号
         * @param tag_id 标签编号字符串
         * @param title 标题
         * @param content html内容
         * @param markdown markdown内容
         * @param is_draft 是否为草稿
         * @param img 首图
         * @param is_evaluate 是否关闭评论 1:关闭 0:默认允许
         */

        this.$refs[formName].validate((valid) => {
          if (valid) {
            let tag_id = 0;
            this.tag_list.map(item => {
              this.form.tag[0] === item.name && (tag_id = item.id);
            });

            let dat = {
              id: this.id,
              tag_id: tag_id,
              title: this.form.title,
              content: this.form.content,
              img: this.form.img,
              is_draft: is_draft ? 1 : 0,
              is_evaluate: this.form.is_evaluate ? 0 : 1,
              time: this.form.time._i,
            };
            console.log(dat, "dat");
            this.$Axios.post('/yun/blog/edit', dat).then(res => {
              console.log(res, "$$$$$$$$$$$$$4");
              if ( res.code === 200 ) {
                this.$message.success("修改成功");
                this.$router.push({name: 'BlogMain'});
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

      // 重置
      resetData() {
        this.form.id = this.id;
        this.form.tag = [];
        this.form.title = '';
        this.form.content = '';
        this.form.img = '';
        this.form.time = '';
        this.form.is_evaluate = true;
      },

      // 编辑器图片上传
      imgAdd(name, file) {
        console.log(file);
        this.upload(file, url => {
          this.$refs.mavon.$img2Url(name, url);
        });
      },

      // 首图图片上传
      imgUpload(file) {
        console.log(file);
        this.upload(file.file, url => {
          this.form.img = url;
        });
      },

      // 图片上传
      upload(file, callback) {
        let formdata = new FormData();
        formdata.append('file', file);
        this.$Axios.post('/yun/oss/upload', formdata, '', {headers: {'Content-Type': 'multipart/form-data'}}).then(res => {
          console.log(res);
          if (res.code === 200) {
            callback(`${res.data.url}?x-oss-process=style/bstu.cn`);
          } else {
            this.$message.error(res.message);
          }
        });
      },

    },
    created() {
      this.getTag();
      this.isAddPage();
    },

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

  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
  .upload_style /deep/ .el-upload {
    border: 1px dashed #ccc;
  }
</style>
