<template>
  <div class="details">
      <el-link type="primary" @click="backHandler">返回</el-link>
    <div class="tab">
      <el-tabs v-model="activeName">
        <!--基本信息-->
        <el-tab-pane label="基本信息" name="first">
          <el-row class="demo-avatar demo-basic">
            <el-col >
          
              <div class="demo-basic--circle">
                <div class="block"><el-avatar :size="90" :src="'http://121.199.29.84:8888/'+customer.userFace" /></div>
              </div>
              <br>
              <el-tag class="sub-title">姓    名： {{customer.realname}}</el-tag>
              <el-tag class="telephone">联系方式：{{ customer.telephone }}</el-tag>
              <el-tag class="status">状态：{{ customer.status }}</el-tag>
              <!-- <template v-slot="scope">
          <div>{{scope.row.address.province+" "+scope.row.address.city+" "+scope.row.address.area+" "+scope.row.address.address}}</div>
          <div>{{scope.row.address.username}} {{scope.row.address.telephone}}</div>
        </template> -->
             
              <el-divider />

            </el-col>
          </el-row>
        </el-tab-pane>
        <!--基本信息-->
        <!--订单信息-->
        <el-tab-pane label="订单信息" name="second">
          <!-- 表格 -->
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
            <el-table-column label="名称" prop="productName" width="90"></el-table-column>
            <el-table-column label="单价" prop="productPrice" width="60"></el-table-column>
            <el-table-column label="数量" prop="number" width="90"></el-table-column>
            <el-table-column label="介绍" prop="productIntroduce"></el-table-column>
          </el-table>
          <!-- /订单项表格 -->
        </template>
      </el-table-column>
      <!-- 普通列 -->
      <el-table-column label="编号" type="index" :index="1" align="center"></el-table-column>
      <el-table-column label="下单时间" prop="orderTime" ></el-table-column>
      <el-table-column label="总价" prop="total"></el-table-column>
      <el-table-column label="状态" prop="status" ></el-table-column>
      <el-table-column label="地址" >
        <template v-slot="scope">
          <div>{{scope.row.address.province+" "+scope.row.address.city+" "+scope.row.address.area+" "+scope.row.address.address}}</div>
          <div>{{scope.row.address.username}} {{scope.row.address.telephone}}</div>
        </template>
      </el-table-column>
    </el-table>
          <!-- 表格 -->
        </el-tab-pane>
        <!--订单信息-->
      </el-tabs>
    </div>
  </div>
</template>
<script>
import {get,post} from "@/utils/request";
export default {
  data(){
    return{
      activeName: 'first',
       orders:[],
       customer:[],  
    }
  },
created() {
    this.findAddressById();
    this.findCustomer();
  },
  methods: {
    backHandler() {
      this.$router.push({ name: 'customer' })
    },
    findAddressById(){
      let url = "http://www.xiaonainiu.top:8888/order/findOrderByUserId?UserId="+this.$route.params.id
      get(url).then((response)=>{
        this.orders = response.data;
      })
    },
    findCustomer(){
      let url="http://www.xiaonainiu.top:8888/user/findById?id="+this.$route.params.id
        get(url).then((response)=>{
            this.customer = response.data;       
          })
   } 
  }
}
</script>
<style scoped>
.tab{
    margin-top: 1em;
}
.btn{
    margin-top:1em;
}
.photo{
    width:88px;
    height:88px;
    border-radius: 44px;
}
.block{
  margin-left:55px;
  margin-top:28px;
}
.sub-title{
  margin-left:15px;
  margin-top:22px;
  font-size: 22px;
  font-weight: 600;
}
.title{
  margin-left: 112px;
  margin-top: 15px;
  font-size: 14px;
  color: #333;
}
.telephone{
  margin-left:90px;
  margin-top: 38px;
   font-size: 22px;
  font-weight: 600;
}
.status{
  margin-left:90px;
  margin-top: 12px;
  font-size: 22px;
  font-weight: 600;
}
.addrrs{
  margin-left: 10px;
  margin-top: 12px;
  font-size: 15px;
  color: #333;
}
</style>

