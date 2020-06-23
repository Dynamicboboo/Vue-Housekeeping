<template>
<div class="product">
  <!-- 按钮 -->
  <div class="btns">
    <el-button type="primary" size="small" @click="toAdd">新增</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
  </div>
    <!-- /按钮 -->

<el-table size="small" :data="cate" row-key="id" :tree-props="{children: 'children'}">
   <!-- <el-table-column  type="index" :index="1" label="编号"></el-table-column>-->
    <el-table-column prop="name" label="名称"></el-table-column>
    <el-table-column prop="nu" label="数字"></el-table-column>

    
    <el-table-column label="操作">
        <template v-slot="slot"> <!--solt了解一下-->
            <el-button type="danger" size="mini" @click.prevent="del(slot.row)">删除</el-button>
            <el-button type="primary" size="mini" @click.prevent="edit(slot.row)">修改</el-button>
        </template>
    </el-table-column>
</el-table>
 <!--分页-->
<el-pagination
    layout="prev, pager, next"
    :total="1000">
  </el-pagination>
  <!--分页结束-->
   <!--模态框-->
  <el-dialog
  title="栏目信息"
  :visible.sync="visible"
  width="30%">
  {{form}}
  <el-form :model="form" label-width="80px">
        <el-form-item label="栏目名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
   
        <el-form-item label="数字">
          <el-input type="id" v-model="form.nu"></el-input> 
        </el-form-item>
        <el-form-item label="名称">
          <el-select v-model="form.parentId">
            <el-option v-for="c in category" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select> 
        </el-form-item>
   
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button type="primary" size="small" @click="submit">确 定</el-button>
  </span>
</el-dialog>
<!--模态框结束-->
</div>
</template>
<script>
import {get,post} from '@/utils/request'
export default {

data(){
    return {
      name:'栏目管理',
      visible:false,
      cate:[],
      category:[],
      
      form:{}
    }
  }, 
  created(){
   this.loaddata();
    // 调用查询栏目的方法加载栏目数据
    this.loadCategories();
  },

//用于存放网页中需要调用的方法
    methods:{
      loadCategories(){
        let url = "http://www.xiaonainiu.top:8888/category/findAllWithChild"
        get(url).then((response)=>{
          this.cate = response.data;
        })
      },

      loaddata(){
        let url = "http://www.xiaonainiu.top:8888/category/findAll"
        get(url).then((response)=>{
            //将查询结果放置到category中,then()中使用“=>”保证this指向外部函数的this。👆
            this.category=response.data;
        })
      },
      //this.form对象---字符串--->后台
      //通过request有后台进行交互，并且要携带参数
        edit(row){
          //获取当前行信息绑定
          this.form = row;
          //打开模态框
          this.visible = true;
        },
        del(row){
          //调用后台接口完成删除操作
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          let url="http://www.xiaonainiu.top:8888/category/deleteById";
          get(url,{id:row.id}).then((response)=>{
            //提示
            this.$message({type:'success',message:response.message})
            //刷新数据
               this.loadCategories();
          })
          })
        },
        //取消提交
        closeModalHandler(){
            this.visible=false;
            //刷新解决
            this.loadCategories();
        },    
        submit(){
            let url = "http://www.xiaonainiu.top:8888/category/saveOrUpdate";
            post(url,this.form).then((response)=>{
        // 提示
        this.$message({type:'success',message:response.message})
        // 关闭模态框
        this.visible = false;
        // 刷新数据
        this.loadCategories();
            })
         },
         //新增栏目管理
         toAdd(){
            // 重置form
            this.form = {}
            //打开模态框
            this.visible = true;
          }
    }
}
</script>
