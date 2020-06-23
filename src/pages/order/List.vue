<template>
  <div class="order">
    <!-- 选项卡 -->
    <el-tabs v-model="param.status" >
      <el-tab-pane label="所有订单" name="all"></el-tab-pane>
      <el-tab-pane label="待支付订单" name="待付款"></el-tab-pane>
      <el-tab-pane label="待派单订单" name="待派单"></el-tab-pane>
      <el-tab-pane label="待服务" name="待服务"></el-tab-pane>
      <el-tab-pane label="待确认" name="待确认"></el-tab-pane>
      <el-tab-pane label="已完成" name="已完成"></el-tab-pane>
    </el-tabs>
    <!-- 订单列表 -->
    <el-table :data="orders" size="small">
      <!-- 折叠列 -->
      <el-table-column type="expand">
        <template v-slot="scope">
          <!-- 订单项表格 -->
          <el-table :data="scope.row.orderLines" size="mini" border>
            <el-table-column label="图片" width="80">
              <template v-slot="scope">
                <img style="width:50px; height:50px; border-radius:3px" :src="'http://121.199.29.84:8888/'+scope.row.productPhoto" alt="">
              </template>
            </el-table-column>
            <el-table-column label="名称" prop="productName" width="120"></el-table-column>
            <el-table-column label="单价" prop="productPrice" width="60"></el-table-column>
            <el-table-column label="数量" prop="number" width="120"></el-table-column>
            <el-table-column label="介绍" prop="productIntroduce" :show-overflow-tooltip="true"></el-table-column>
          </el-table>
          <!-- /订单项表格 -->
        </template>
      </el-table-column>
      <!-- 普通列 -->
      <el-table-column label="编号" type="index" :index="1" align="center"></el-table-column>
      <el-table-column label="下单时间" prop="orderTime" width="150">
        <template slot-scope="scope">{{scope.row.orderTime | formatDate}}</template>
      </el-table-column>
      <el-table-column label="总价" prop="total" width="60"></el-table-column>
      <el-table-column label="顾客" prop="customer.realname" width="60"></el-table-column>
      <el-table-column label="员工" prop="employee.realname" width="60"></el-table-column>
      <el-table-column label="状态" prop="status" width="60"></el-table-column>
     
      <el-table-column label="地址" >
        <template v-slot="scope">
          <div>{{scope.row.address.province+" "+scope.row.address.city+" "+scope.row.address.area+" "+scope.row.address.address}}</div>
          <div>{{scope.row.address.username}} {{scope.row.address.telephone}}</div>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="160" align="center">
        <template slot-scope="scope">
          <el-button disabled type="primary" size="mini" v-if="scope.row.status==='待付款'" >待付款</el-button>
          <el-button type="primary" size="mini" v-if="scope.row.status==='待派单'" @click="sendOrder(scope.row.id)">待派单</el-button>
          
          <el-button type="primary" size="mini" v-if="scope.row.status==='待服务'" @click="rejectOrder(scope.row.id)">待服务</el-button>
          <el-button disabled type="primary" size="mini" v-if="scope.row.status==='待确认'">待确认</el-button>
          <el-button disabled type="success" size="mini" v-if="scope.row.status==='已完成'" >已完成</el-button>
        </template>
      </el-table-column>
    </el-table>
<!-- 模态框 -->
  <el-dialog title="派单" :visible.sync="visible">
    <el-radio v-if="e.status==='启用'" v-for="e in employee" v-model="employeeId" :label="e.id" :key="e.id">
      {{e.realname}}

    </el-radio>
    <div slot="footer" class="dialog-footer">
       <el-button  size="small" @click="close()">取消</el-button>
        <el-button type="primary" size="small" @click="submit()">确定</el-button>
    </div>
  
  </el-dialog>
  

  </div>
</template>

<script>
import {get} from '@/utils/request';
import { formatDate } from '@/utils/date.js';

 

export default {
  data(){
    return {
      param:{
        status:'all'
      },
      orders:[],
      OrderId:"",
      employee:[],
      visible:false,
      employeeId:""
    }
  },
   filters: {
    formatDate(time) {
      var date = new Date(time);
      return formatDate(date, "yyyy-MM-dd hh:mm");
    }
  },
  created(){
    // 初始化查询
    this.loadOrders();
  },
  watch:{
    // 监听param的变化，一旦变化立刻查询
    param:{
      handler:function(){
        this.loadOrders()
      },
      deep:true
    }
  },
  methods:{
    // 加载订单信息
    loadOrders(){
      let url = "http://www.xiaonainiu.top:8888/order/query"
      // 当状态为all，说明查询所有订单，不对订单状态进行过滤，删除这个属性即可
      // 1. 克隆param
      let param = Object.assign({},this.param);
      // 2. 处理数据
      if(param.status == 'all'){
        delete param.status;
      }
      get(url,param).then((response)=>{
        this.orders = response.data;
      })
    },
    sendOrder(id){
      this.loadEmployee();
      this.OrderId = id;
      this.visible = true;
      
    },
     rejectOrder(id){
        let url = "http://www.xiaonainiu.top:8888/order/rejectOrder?OrderId="+id
        get(url).then(response=>{
          this.$message({type:"success",message:response.message})
        })
        this.param.status="待确认";
      this.loadOrders();
    },
    //查找所有员工
     loadEmployee(){
      let url = "http://www.xiaonainiu.top:8888/user/findAllEmployee"
      get(url).then(response=>{
        this.employee = response.data;
      })
    },
    close(){
      this.visible = false;
    },
   
    submit(){
    
      let data = {
        OrderId:this.OrderId,
        employeeId:this.employeeId,
      }
      let url = "http://www.xiaonainiu.top:8888/order/sendOrder";
      get(url,data).then(response=>{
        this.$message({type:"success",message:response.message})
      })
     this.param.status="待服务";
      this.loadOrders();
      this.visible = false;
      
    }
    
  }
}
</script>