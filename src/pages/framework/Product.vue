<template>
  <div class="product">
    <!-- 按钮 -->
    <div class="btns">
      <el-button size="small" type="primary" @click="toAdd">新增</el-button>
    </div>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table size="small" :data="products">
      <el-table-column label="编号" type="index" :index="1"></el-table-column>
      <el-table-column label="图片">
        <template v-slot="scope">
          <img style="width:50px; height:50px; border-radius:3px" :src="'http://121.199.29.84:8888/'+scope.row.photo" alt="">
        </template>
      </el-table-column>
      <el-table-column label="名称" prop="name"></el-table-column>
      <el-table-column label="单价" prop="price"></el-table-column>
      <el-table-column label="介绍" prop="introduce" :show-overflow-tooltip="true"></el-table-column>
      <el-table-column label="所属分类" prop="category.name"></el-table-column>
      <el-table-column label="操作" >
        <template v-slot="scope">
          <el-button type="danger" size="mini" @click="del(scope.row)"> 删除</el-button>
          <el-button type="primary" size="mini" @click="edit(scope.row)"> 修改</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格 -->
    <!-- 模态框 -->
    <el-dialog title="录入产品信息" :visible.sync="visable">
      <el-form label-width="80px" :model="form" ref="ruleForm" :rules="rules">
        <el-form-item label="产品名称" prop="name">
          <el-input v-model="form.name"  clearable placeholder="请输入产品名称"></el-input>
        </el-form-item>
        <el-form-item label="所属栏目">
          <el-cascader 
            v-model="form.categoryId" 
            :options="categories" 
            :props="{ label:'name',value:'id',checkStrictly: true,emitPath:false }" clearable>
          </el-cascader>
        </el-form-item>
        <el-form-item label="产品单价" prop="price">
          <el-input v-model="form.price" placeholder="请输入产品单价"></el-input>
        </el-form-item>
        <el-form-item label="产品介绍" prop="introduce">
          <el-input v-model="form.introduce" type="textarea" placeholder="请输入产品介绍"></el-input>
        </el-form-item>
       
        <el-form-item label="图片上传">
          <el-upload
            class="upload-demo"
            :multiple="false"
            :limit="1"
            :on-success="uploadSuccessHandler"
            :on-error="uploadErrorHandler"
            action="http://121.199.29.84:5588/file/upload"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="close('ruleForm')">取消</el-button>
        <el-button type="primary" size="small" @click="submit('ruleForm')">确定</el-button>
      </div>
    </el-dialog>
    <!-- 模态框 -->
  </div>
</template>
<script>
import {get,post} from '@/utils/request'
export default {
  data(){
    return {
      name:'产品管理',
      visable:false,
      products:[],
      categories:[],
      form:{},
      rules:{
          name: [
          { required: true, message: "请输入产品名称", trigger: "blur" }
        ],
        categoryId: [
          { required: true, message: "请输入产品所属栏目", trigger: "blur" }
        ],
        price: [{ required: true, message: "请输入状态", trigger: "change" }],
        introduce: [{ required: true, message: "请输入产品介绍", trigger: "blur" }]
        
      }
    }
  },
  created(){
    // 调用加载数据方法进行数据加载
    this.loadProducts();
    // 调用查询栏目的方法加载栏目数据
    this.loadCategories();
  },
  methods:{
     uploadSuccessHandler(response){
      //console.log(response);
      let photo = response.data.groupname+'/'+response.data.id;
      //this.form.photo = photo;
      this.form = {...this.form,photo}
    },
    uploadErrorHandler(error){
      this.$message({type:'error',message:'上传失败'})
    },
    // 提交
    submit(form){
        this.$refs[form].validate(valid =>{
            if (valid) {
                 let url = "http://www.xiaonainiu.top:8888/product/saveOrUpdate";
                 post(url,this.form).then((response)=>{
                    // 提示
                    this.$message({type:'success',message:response.message})
                    // 关闭模态框
                    this.visable = false;
                    // 刷新数据
                    this.loadProducts();
                   // this.$refs[form].resetFilds();
                });
            }else{
                return false;
            }
        });
    },
    toAdd(){
      // 重置form
      this.form = {}
      //打开模态框
      this.visable = true;
    },
    edit(row){
      //将当前行信息绑定
      this.form = row;
      // 打开模态框
      this.visable = true;
    },
    del(row){
      this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 删除
        let url = "http://www.xiaonainiu.top:8888/product/deleteById"
        get(url,{id:row.id}).then((response)=>{
          // 提示
          this.$message({type:'success',message:response.message})
          // 刷新数据
          this.loadProducts();
        })
      })
    },
    // 加载产品数据
    loadProducts(){
      let url = "http://www.xiaonainiu.top:8888/product/findAllWithCategory"
      get(url).then((response)=>{
        this.products = response.data;
      })
    },
    // 加载栏目数据
    loadCategories(){
      let url = "http://www.xiaonainiu.top:8888/category/findAllWithChild"
      get(url).then((response)=>{
        this.categories = response.data;
      })
    },
    close(form) {
      this.visable = false;
      this.loadProducts();
    }
  }
}
</script>
