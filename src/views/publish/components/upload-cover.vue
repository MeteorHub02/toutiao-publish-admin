<template>
  <div class="upload-cover" @click="showCoverSelect">
    <div class="cover-wrap">
      <img class="cover-image" :src="value" ref="cover-image" />
    </div>
    <el-dialog
      title="选择封面"
      :visible.sync="dialogVisible"
      width="30%"
      append-to-body
    >
      <el-tabs v-model="activeName" type="card">
        <!-- <el-tab-pane label="素材库" name="first">内容</el-tab-pane> -->
        <el-tab-pane label="上传图片" name="second">
          <input type="file" ref="file" @change="onFileChange" />
          <img ref="preview-image" width="100" alt="" />
        </el-tab-pane>
      </el-tabs>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="onCoverConfirm">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { uploadImage } from "@/api/article";
export default {
  name: "UploadCover",
  components: {},
  props:['value'],
  data() {
    return {
      dialogVisible: false,
      activeName: "second",
    };
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {},
  methods: {
    showCoverSelect() {
      this.dialogVisible = true;
    },
    onFileChange() {
      //   获取文件对象
      const file = this.$refs.file.files[0];
      //   图片预览
      const blob = window.URL.createObjectURL(file);
      this.$refs["preview-image"].src = blob;
      //   防止用户选择同一个文件不触发 change 事件
      //   this.$refs.file.value = "";
    },
    // 处理弹出框的点击事件
    onCoverConfirm() {
      // 如果 tabs 是上传事件，并且 input-file 有选择的文件，才执行上传图片的操作
      if (this.activeName === "second") {
        const file = this.$refs.file.files[0];
        console.log(file);
        if (!file) {
          this.$message("请选择文件");
          return;
        }
        // 执行上传文件的操作
        const fd = new FormData();
        fd.append("image", file);
        uploadImage(fd).then((res) => {
          console.log(res);
          //   关闭弹出层
          this.dialogVisible = false;
          // 展示上传的图片
          this.$refs["cover-image"].src = res.data.data.url;
          //   将图片地址发送给父组件
          this.$emit("input", res.data.data.url);
        });
      }
    },
  },
};
</script>

<style scoped lang="less">
.cover-wrap {
  width: 150px;
  height: 120px;
  border: 1px solid #000;
  .cover-image {
    height: 120px;
    max-width: 100%;
  }
}
</style>
