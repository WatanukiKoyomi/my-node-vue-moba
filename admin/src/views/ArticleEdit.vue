<template>
    <div>
        <h1>{{id? '编辑':'新建'}}文章</h1>
        <el-form label-width="120px" @submit.native.prevent="save">
            <el-form-item label="所属分类">
                <el-select v-model="model.categories" placeholder="请选择" multiple>
                    <el-option v-for="item in categories" :key="item._id" 
                    :label="item.name" :value="item._id"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="标题">
                <el-input v-model="model.title" width="50px"></el-input>
            </el-form-item>
            <el-form-item label="内容">
                <vue-editor v-model="model.body" useCustomImageHandler @imageAdded="handlerImageAdded"></vue-editor>
                <!-- <quill-editor v-model="model.body"></quill-editor> -->
            </el-form-item>
            <el-form-item>
                <el-button type="primary" native-type="submit">保存</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
import { VueEditor } from 'vue2-editor'

// import 'quill/dist/quill.core.css'
// import 'quill/dist/quill.snow.css'
// import 'quill/dist/quill.bubble.css'
 
// import { quillEditor } from 'vue-quill-editor'

export default {
    components: {
        VueEditor,
        // quillEditor
    },
    props:{
        id:{}
    },
    data(){
        return {
            model: {},
            categories: []
        }
    },
    methods: {
        async handlerImageAdded(file, Editor, cursorLocation, resetUploader){
            const formData = new FormData();
            formData.append('file', file);

            const res = await this.$http.post('upload', formData);
            Editor.insertEmbed(cursorLocation, 'image', res.data.url);
            resetUploader();
        },
        async save(){
            let res
            if(this.id){
                res = await this.$http.put('/rest/articles/'+this.id,this.model)
            }else{
                res = await this.$http.post('/rest/articles/',this.model)
            }
            this.$router.push('/articles/list')
            this.$message({
                type: 'success',
                message: '保存成功'
            })
        },
        async fetch(){
            const res=await this.$http.get('/rest/articles/'+this.id)
            this.model=res.data
        },
        async fetchCategories(){
            const res=await this.$http.get('/rest/categories')
            this.categories=res.data
        }
    },
    created(){
        this.fetchCategories()
        this.id && this.fetch()
    }
}
</script>

