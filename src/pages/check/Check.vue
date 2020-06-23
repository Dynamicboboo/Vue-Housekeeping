<template>
  <div class="check">
      审核
      <el-table :data="employee">
        <el-table-column prop="id" label="编号" width="60"/>
        <el-table-column prop="realname" label="姓名"  width="80"/>
        <el-table-column prop="telephone" label="手机号"  width="110"/>
        <el-table-column prop="idCard" label="身份证号码" width="165"/>
        <el-table-column prop="bankCard" label="银行卡号" width="170"/>
        <el-table-column prop="registerTime" label="注册时间">
          <template slot-scope="scope">{{scope.row.registerTime | formatDate}}</template>
        </el-table-column>
        <el-table-column prop="status" label="状态"/>
       
        <el-table-column label="操作" fixed="right">
           <template slot-scope="scope">
             <el-button type="primary" size="small" @click="shenhe(scope.row)">审核</el-button>
           </template>
        </el-table-column>
      </el-table>
      <el-dialog title="员工审核" :visible.sync="visible">
        <p>用户名：{{this.emp.realname}}</p>
        <p>身份证号：{{this.emp.idCard}}</p>
        <div>
          <p>身份证正面照</p>
          <img width="260" height="140" :src="this.emp.idPhotoPositive" alt="">
          <img width="260" height="140" :src="this.emp.idPhotoNegative" alt="">
        </div>
        <div>
              <el-button type="primary" size="small" @click="submit">审核通过</el-button>
              <el-button type="danger" size="small" @click="refuse">审核不通过</el-button>
        </div>

      </el-dialog>
  </div>
</template>
<script>
import {get,post} from "@/utils/request";
import { formatDate } from '@/utils/date.js';
export default {
  data(){
    return{
      visible: false,
      employee:[],
      emp:[]
    }
  },
  //时间戳格式转换
  filters: {
    formatDate(time) {
      var date = new Date(time);
      return formatDate(date, "yyyy-MM-dd hh:mm");
    }
  },
  created(){
    this.loadEmployee();
  },
  methods:{
    loadEmployee(){
      let url = "http://www.xiaonainiu.top:8888/user/findAllEmployee"
      get(url).then(response=>{
        this.employee = response.data;
      })
    },
    shenhe(row){
      this.emp = row;
      this.visible = true;
    },
    submit(){
      let url = "http://www.xiaonainiu.top:8888/user/auditing"
      get(url,{id:this.emp.id}).then(response=>{
        this.$message({type:"success",message:response.message});
        this.loadEmployee();
      })
      this.visible = false;
    },
    refuse(){
    let url = "http://www.xiaonainiu.top:8888/user/refuseAuditing"
      get(url,{id:this.emp.id}).then(response=>{
        this.$message({type:"success",message:response.message});
        this.loadEmployee();
      })
      this.visible = false;
    }
  }
}
</script>