<template>
<div class="category">
    <!-- 按钮 -->
    <el-button type="primary" size="small" @click="toAdd">新增</el-button>
    <!-- /按钮 -->
 
<el-table size="small" :data="categories" style="with:100%;margin-bottom:20px;" row-key="id"  default-expand-all
    :tree-props="{children: 'children'}">
 <el-table-column prop="name" label="栏目名称" width="420"></el-table-column>
      <el-table-column prop="nu" label="序号 "  ></el-table-column>
      <el-table-column fixed="right" label="操作" width="160" align="center">
        <template slot-scope="scope">
          <el-button type="danger" @click="del(scope.row.id)"  size="mini">删除</el-button>
          <el-button type="primary" @click="edit(scope.row)"  size="mini">编辑</el-button>
        </template>
      </el-table-column>
</el-table>
   <!--模态框-->
  <el-dialog title="栏目信息" :visible.sync="visible" width="30%">
  <el-form :model="form" label-width="80px" :rules="rules" ref="ruleForm">
        <el-form-item label="栏目名称" prop="name">
          <el-input v-model="form.name" clearable  placeholder="请输入栏目名称"></el-input>
        </el-form-item>
   
        <el-form-item label="序号" prop="nu">
          <el-input v-model="form.nu" clearable  placeholder="请输入序号"></el-input> 
        </el-form-item>
        <el-form-item label="所属栏目" prop="parentId">
          <el-select v-model="form.parentId"  placeholder="请选择所属栏目">
            <el-option v-for="c in categories" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select> 
        </el-form-item>
      
  </el-form>

    <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="close('ruleForm')">取消</el-button>
        <el-button type="primary" size="small" @click="submit('ruleForm')">确定</el-button>
    </div>
</el-dialog>
<!--模态框结束-->
</div>
</template>
<script>
import {get,post} from "@/utils/request";
export default {
    data(){
        return {
            name: "栏目管理",
            visible: false,
            form: {},
            categories: [],
            ruleForm:{
                name:"",
                nu:"",
                parentId:"",
            },
            //校验规则
            rules:{
                name:[{required:true,message:"请输入栏目名称",trigger:"blur"}],
                nu:[{required:true,message:"请输入数量",trigger:"blur"}],
            }    
        };
    },

        created(){
                this.loadCategories();
            },
            methods:{
                //加载栏目信息
                loadCategories(){
                    let url = "http://www.xiaonainiu.top:8888/category/findAllWithChild"
                    get(url). then(response=>{
                        this.categories = response.data;
                    })
                },
                toAdd(){
                    this.form = {};
                    this.visible =  true;

                },
                del(id){
                    
                this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then(() => {
                        // 删除
                        let url = "http://www.xiaonainiu.top:8888/category/deleteById"
                        get(url,{id:id}).then((response)=>{
                        // 提示
                        this.$message({type:'success',message:response.message})
                        // 刷新数据
                        this.loadCategories();
                        })
                    })
                },
                edit(row){
                    this.visible = true;
                     this.form = row
                },
                close(form){
                    this.visible = false;
                    this.loadCategories();
                },
                submit(form){
                    this.$refs[form].validate(valid=>{
                        if (valid) {
                            let url =  "http://www.xiaonainiu.top:8888/category/saveOrUpdate"
                    post(url,this.form).then(response=>{
                        this.$message({type:"success",message:response.message});
                        this.visible = false;
                        this.loadCategories();
                    });
                        }else{
                            return false;
                        }
                    
                    });
                    
                }
            }

}
</script>
