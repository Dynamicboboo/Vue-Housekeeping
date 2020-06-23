<template>
  <div class="detailsemp">
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
              <el-divider />
            </el-col>
          </el-row>
        </el-tab-pane>
        <!--基本信息-->
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
      this.$router.push({ name: 'employee' })
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

