<template>
  <div>
    <h1>{{id? '编辑':'新建'}}广告位</h1>
    <el-form label-width="120px" @submit.native.prevent="save">
      <el-form-item label="名称">
        <el-input v-model="model.name" width="50px"></el-input>
      </el-form-item>
      <el-form-item label="广告">
          <el-button type="text" @click="model.items.push({})">
        <i class="el-icon-plus"></i>添加
      </el-button>
      <el-row type="flex" style="flex-wrap: wrap;">
        <el-column :md="24" v-for="(item, index) in model.items" :key="index">
          <el-form-item label="跳转链接">
            <el-input v-model="item.url"></el-input>
          </el-form-item>
          <el-form-item label="图片" style="margin-top: 0.5rem">
            <el-upload
              class="avatar-uploader"
              :action="$http.defaults.baseURL+'/upload'"
              :show-file-list="false"
              :on-success="res =>$set(item, 'image', res.url)"
              :before-upload="beforeAvatarUpload"
            >
              <img v-if="item.image" :src="item.image" class="image" />
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
          </el-form-item>
          <el-form-item>
            <el-button size="small" type="danger" @click="deleteSkill(index)">删除</el-button>
          </el-form-item>
        </el-column>
      </el-row>
      </el-form-item>
      
      <el-form-item>
        <el-button type="primary" native-type="submit">保存</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  props: {
    id: {}
  },
  data() {
    return {
      model: {
          items: [],
      }
    };
  },
  methods: {
      deleteSkill(index){
      this.$confirm("是否确定要删除该广告位吗", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          await this.model.items.splice(index, 1);
          this.$message({
            type: "success",
            message: "删除成功!"
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    async save() {
      let res;
      if (this.id) {
        res = await this.$http.put("/rest/ads/" + this.id, this.model);
      } else {
        res = await this.$http.post("/rest/ads/", this.model);
      }
      this.$router.push("/ads/list");
      this.$message({
        type: "success",
        message: "保存成功"
      });
    },
    async fetch() {
      const res = await this.$http.get("/rest/ads/" + this.id);
      this.model = Object.assign({}, this.model, res.data);
    }
  },
  created() {
    this.id && this.fetch();
  }
};
</script>

